# BMAD Workflow Guide
## Від встановлення до реалізації задачі (BMAD-2)

> Цей гайд демонструє повний цикл роботи з BMAD на прикладі реального проєкту.
> **Проєкт:** `fastapi/full-stack-fastapi-template`
> **Задача:** BMAD-2 — Пошук та фільтрація Items (3 SP)

---

## Частина 1 — Підготовка середовища

### Крок 1.1 — Запустити проєкт

```bash
cd full-stack-fastapi-template
docker compose up -d --build
```

Перевір що все працює:
- Frontend: http://localhost:5173
- Backend API: http://localhost:8000/docs

### Крок 1.2 — Встановити BMAD

```bash
npx bmad-method install
```

> ⚠️ **Важливо:** На кроці вибору інструментів та модулів натискай **пробіл** для вибору, а потім **Enter**.

Обери:
- Tool: **claude-code**
- Modules: **BMad Method (bmm)**

Після встановлення в корені проєкту з'явиться папка `_bmad/`.

### Крок 1.3 — Відкрити проєкт у Claude Code

```bash
claude .
```

---

## Частина 2 — Ініціалізація проєкту в BMAD

### Крок 2.1 — Згенерувати Project Context

Перша і найважливіша команда для **існуючого** проєкту:

```
/bmad-generate-project-context
```

**Що відбувається:**
BMAD сканує кодову базу і генерує `_bmad-output/project-context.md` — компактний файл з правилами проєкту для всіх агентів. Наприклад, він знаходить що:
- Проєкт використовує **psycopg3**, не psycopg2
- Немає `tailwind.config.js` (Tailwind v4)
- Клієнтський код у `src/client/` **генерується автоматично** — не редагувати вручну
- Mypy у strict режимі — весь код має проходити type-check

> 💡 Цей файл завантажується кожним агентом при активації. Без нього агенти не знають специфіки проєкту і будуть робити типові помилки.

**Результат:** `_bmad-output/project-context.md`

---

### Крок 2.2 — Запустити Sprint Planning

```
/bmad-sprint-planning
```

Передай BMAD список задач з бекологу. Можна скопіювати з `TASKS_JIRA.md` або просто написати:

```
Here is our backlog:
- BMAD-2: Search and filtering for Items (High priority, 3 SP)
- BMAD-6: CSV Export (Low priority, 3 SP)
- BMAD-4: Dashboard with statistics (Medium priority, 5 SP)

Let's plan sprint 1 with BMAD-2 only.
```

**Що відбувається:**
BMAD створює файл спринту в `_bmad-output/implementation-artifacts/sprint-plan.md` зі статусами кожної story.

**Результат:** `_bmad-output/implementation-artifacts/sprint-plan.md`

---

## Частина 3 — Реалізація задачі BMAD-2

### Крок 3.1 — Створити Story файл

```
/bmad-create-story BMAD-2
```

Або більш детально:
```
Create story for BMAD-2: Search and filtering for Items.

Backend:
- Add query param `search` to GET /api/v1/items/ — ILIKE on title and description
- Add query param `sort_by` (created_at_asc, created_at_desc, title_asc, title_desc)
- Update count query to respect search filter

Frontend:
- Add Search input with debounce 300ms above items table
- Add sort dropdown (Newest / Oldest / A→Z / Z→A)
- Show "Found N items" counter
```

**Що відбувається:**
BMAD підготовує детальний файл story з усім контекстом, який потрібен агенту-розробнику: опис задачі, acceptance criteria, файли які треба змінити, технічні нотатки з project-context.md.

**Результат:** `_bmad-output/implementation-artifacts/BMAD-2-search-filter.md`

---

### Крок 3.2 — Передати задачу агенту Dev (Amelia)

Варіант A — через агента:
```
/bmad-agent-dev
```

Після активації Amelia скаже: *"Hi Vadym! I'm Amelia, your senior software engineer. Ready to execute stories."*

Потім:
```
DS
```
(Dev Story — shortcode з меню можливостей агента)

Або одразу:
```
dev this story _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

Варіант B — напряму:
```
/bmad-dev-story _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

**Що Amelia робить (автономно, без зупинок):**

**Backend:**
1. Читає весь story файл до початку
2. Модифікує `backend/app/api/routes/items.py`:
   ```python
   @router.get("/", response_model=ItemsPublic)
   def read_items(
       session: SessionDep,
       current_user: CurrentUser,
       skip: int = 0,
       limit: int = 100,
       search: str | None = None,        # ← нове
       sort_by: str | None = None,       # ← нове
   ) -> Any:
   ```
3. Додає ILIKE фільтрацію та сортування до SQL-запиту
4. Оновлює count-запит
5. Запускає тести: `pytest backend/app/tests/api/routes/test_items.py`
6. Дописує нові тести для search/sort параметрів
7. Позначає backend subtasks як `[x]`

**Frontend:**
1. Модифікує `frontend/src/routes/_layout/items.tsx`:
   - Додає `useState` для search і sortBy
   - Реалізує debounce через `useDeferredValue`
   - Передає параметри в `ItemsService.readItems()`
2. Додає Search input з іконкою (Lucide `Search`)
3. Додає `Select` компонент для сортування
4. Додає рядок "Found N items"
5. Регенерує клієнт: `npm run generate-client`
6. Позначає frontend subtasks як `[x]`

> ⚠️ **Критичне правило Amelia:** вона ніколи не ставить `[x]` якщо тести не проходять. Не бреше про тести.

---

### Крок 3.3 — Code Review

Після того як Amelia завершила імплементацію:

```
CR
```

Або:
```
/bmad-code-review _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

**Що відбувається:**
BMAD перевіряє код на:
- Відповідність acceptance criteria зі story
- Type safety (mypy strict)
- Тести (чи існують, чи проходять)
- Патерни проєкту (з project-context.md)
- Edge cases

Якщо є проблеми → повертається до `DS` (Dev Story) для фіксу.
Якщо все ок → story отримує статус **Done**.

---

### Крок 3.4 — Оновити Sprint Status

```
/bmad-sprint-status
```

BMAD підсумовує стан спринту: скільки stories Done, In Progress, Not Started.

---

## Повний флоу одним поглядом

```
[Claude Code відкрито у папці проєкту]
         │
         ▼
/bmad-generate-project-context
→ Сканує кодобазу
→ Зберігає правила в _bmad-output/project-context.md
         │
         ▼
/bmad-sprint-planning
→ Приймає backlog
→ Створює sprint-plan.md
         │
         ▼
/bmad-create-story BMAD-2
→ Підготовує story файл з повним контекстом
→ _bmad-output/implementation-artifacts/BMAD-2.md
         │
         ▼
/bmad-agent-dev  →  DS  (або /bmad-dev-story)
→ Amelia читає story
→ Пише бек (items.py) + тести
→ Пише фронт (items.tsx) + regenerates client
→ Все тести проходять
         │
         ▼
CR  (або /bmad-code-review)
→ Review на відповідність критеріям
→ Якщо є зауваження → DS знову
→ Якщо ок → Story: Done ✅
         │
         ▼
/bmad-sprint-status
→ BMAD-2: Done ✅
→ Наступна story: BMAD-6
```

---

## Корисні команди в процесі

| Команда | Коли використовувати |
|---|---|
| `bmad-help` | Заплутався — питаєш що робити далі |
| `/bmad-correct-course` | Вимоги змінились під час спринту |
| `/bmad-party-mode` | Хочеш обговорити задачу з кількома агентами (PM + Architect + Dev) |
| `/bmad-review-adversarial-general` | Хочеш критичний review документа або коду |
| `/bmad-sprint-status` | Перевірити поточний стан спринту |

---

## Що зберігається після роботи

```
_bmad-output/
├── project-context.md              ← правила проєкту для агентів
├── planning-artifacts/
│   └── sprint-plan.md              ← план спринту
└── implementation-artifacts/
    ├── BMAD-2-search-filter.md     ← story файл з Dev Agent Record
    └── sprint-status.md            ← статус всіх stories
```

Story файл після завершення містить **Dev Agent Record** — що саме було зроблено, які файли змінено, які рішення прийнято. Це аудит-трейл роботи агента.

---

## Для студентів — Як починати

1. `docker compose up -d` — запустити проєкт
2. `npx bmad-method install` — встановити BMAD (пробіл для вибору!)
3. `claude .` — відкрити в Claude Code
4. `/bmad-generate-project-context` — ініціалізувати
5. Обрати задачу з `TASKS_JIRA.md`
6. `/bmad-sprint-planning` → `/bmad-create-story` → `/bmad-agent-dev` → `DS` → `CR`
7. `git commit` — зафіксувати результат

---

*Stack: FastAPI · React · TypeScript · PostgreSQL · Docker*
*BMAD version: 6.2.3*
