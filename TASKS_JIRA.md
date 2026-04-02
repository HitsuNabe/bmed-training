# Backlog — Full Stack FastAPI Template

> Project: **BMAD-DEMO**
> Board: Kanban
> Stack: FastAPI · React · TypeScript · PostgreSQL · SQLModel · Docker

---

## BMAD-1: Додати систему тегів для Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу мати можливість додавати теги до Items, щоб категоризувати та швидко фільтрувати свої записи.

Зараз модель Item має лише `title` та `description`. Потрібно додати сутність Tag та зв'язок many-to-many між Item і Tag.

### Subtasks

- [ ] **[BE]** Створити модель `Tag` (id: UUID, name: str unique, max 50 chars)
- [ ] **[BE]** Створити link-таблицю `ItemTag` (item_id FK, tag_id FK)
- [ ] **[BE]** Написати Alembic міграцію
- [ ] **[BE]** Додати ендпоінт `GET /api/v1/tags/` — список тегів
- [ ] **[BE]** Додати ендпоінт `POST /api/v1/tags/` — створити тег
- [ ] **[BE]** Модифікувати `POST /PUT /api/v1/items/` — приймати `tag_ids: list[UUID]`
- [ ] **[BE]** Модифікувати `GET /api/v1/items/` — повертати теги в response + додати query-параметр `tag_id` для фільтрації
- [ ] **[FE]** Створити компонент `TagBadge` — кольоровий badge
- [ ] **[FE]** Додати multi-select тегів у форму створення/редагування Item
- [ ] **[FE]** Показувати теги в колонці таблиці Items
- [ ] **[FE]** Додати dropdown-фільтр по тегу на сторінці Items

### Acceptance Criteria

```gherkin
GIVEN я авторизований користувач
WHEN я створюю новий тег "urgent" через API
THEN тег з'являється в списку GET /api/v1/tags/

GIVEN існує тег "urgent"
WHEN я створюю Item з tag_ids: ["<urgent_id>"]
THEN Item повертається з масивом tags: [{id, name: "urgent"}]

GIVEN існують Items з різними тегами
WHEN я фільтрую по тегу "urgent" на фронтенді
THEN в таблиці відображаються тільки Items з тегом "urgent"
```

### Technical Notes

- Файли бекенду: `backend/app/models.py`, `backend/app/api/routes/items.py`, новий `backend/app/api/routes/tags.py`
- Файли фронтенду: `frontend/src/components/Items/`, `frontend/src/routes/_layout/items.tsx`
- Зареєструвати новий роутер в `backend/app/api/main.py`

---

## BMAD-2: Реалізувати пошук та сортування Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | High |
| **Story Points** | 3 |
| **Labels** | `backend`, `frontend`, `ux-improvement` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу мати можливість шукати Items по назві/опису та сортувати список, щоб швидко знаходити потрібні записи.

Зараз `GET /api/v1/items/` підтримує лише `skip` та `limit`.

### Subtasks

- [ ] **[BE]** Додати query-параметр `search: str` до `GET /api/v1/items/` — ILIKE по `title` та `description`
- [ ] **[BE]** Додати query-параметр `sort_by: str` (варіанти: `created_at_asc`, `created_at_desc`, `title_asc`, `title_desc`)
- [ ] **[BE]** Оновити count-запит щоб враховувати search-фільтр
- [ ] **[BE]** Написати тести для нових параметрів
- [ ] **[FE]** Додати input з іконкою Search зверху таблиці Items
- [ ] **[FE]** Реалізувати debounce 300ms на пошуковому input
- [ ] **[FE]** Додати dropdown для сортування (Newest / Oldest / A→Z / Z→A)
- [ ] **[FE]** Показувати "Found N items" під пошуковим рядком

### Acceptance Criteria

```gherkin
GIVEN існують Items з title "Test Report" та "Monthly Summary"
WHEN я вводжу "test" в поле пошуку
THEN в таблиці відображається тільки "Test Report"
AND показується "Found 1 item"

GIVEN я ввів пошуковий запит
WHEN я швидко набираю текст
THEN запит до API відправляється тільки через 300ms після останнього натискання клавіші

GIVEN я обрав сортування "A→Z"
WHEN Items завантажуються
THEN вони відсортовані по title в алфавітному порядку
```

### Technical Notes

- Файли бекенду: `backend/app/api/routes/items.py`
- Файли фронтенду: `frontend/src/routes/_layout/items.tsx`
- Для debounce можна використати `useDeferredValue` з React або кастомний хук

---

## BMAD-3: Додати коментарі до Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 8 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу залишати коментарі до Items та бачити коментарі інших користувачів, щоб обговорювати записи з командою.

Потрібно створити нову модель, API ендпоінти, та нову сторінку деталей Item.

### Subtasks

- [ ] **[BE]** Створити модель `Comment` (id: UUID, text: str max 1000, created_at, item_id: FK → Item, author_id: FK → User)
- [ ] **[BE]** Додати Relationship: Item.comments, User.comments
- [ ] **[BE]** Написати Alembic міграцію
- [ ] **[BE]** Додати `GET /api/v1/items/{item_id}/comments/` — список коментарів (з пагінацією, включати author name/email)
- [ ] **[BE]** Додати `POST /api/v1/items/{item_id}/comments/` — створити коментар (author = current_user)
- [ ] **[BE]** Додати `DELETE /api/v1/items/{item_id}/comments/{comment_id}` — видалити (автор або superuser)
- [ ] **[BE]** Написати тести для permissions (автор може видалити свій, superuser — будь-який, інші — 403)
- [ ] **[FE]** Створити роут `/items/$itemId` — сторінка деталей Item
- [ ] **[FE]** Компонент `CommentList` — список коментарів зі скролом
- [ ] **[FE]** Компонент `CommentForm` — textarea + кнопка "Send"
- [ ] **[FE]** Компонент `CommentCard` — текст, автор, дата, кнопка Delete (умовна)
- [ ] **[FE]** Зробити title в таблиці Items клікабельним → перехід на деталі

### Acceptance Criteria

```gherkin
GIVEN я авторизований та на сторінці деталей Item
WHEN я вводжу текст коментаря та натискаю "Send"
THEN коментар з'являється в списку з моїм ім'ям та поточною датою

GIVEN коментар залишений іншим користувачем
WHEN я (не superuser) намагаюсь його видалити
THEN кнопка Delete не відображається
AND API повертає 403

GIVEN я superuser
WHEN я натискаю Delete на будь-якому коментарі
THEN коментар видаляється

GIVEN я на сторінці Items
WHEN я натискаю на title Item
THEN я перенаправляюсь на /items/{id} з деталями та коментарями
```

### Technical Notes

- Новий файл: `backend/app/api/routes/comments.py`
- Новий роут: `frontend/src/routes/_layout/items/$itemId.tsx`
- Нові компоненти: `frontend/src/components/Comments/`
- Зареєструвати роутер коментарів в `backend/app/api/main.py`

---

## BMAD-4: Створити Dashboard зі статистикою

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `new-feature`, `analytics` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу бачити Dashboard з базовою статистикою на головній сторінці, щоб мати загальне уявлення про стан системи.

Зараз головна сторінка (`/`) практично порожня.

### Subtasks

- [ ] **[BE]** Створити ендпоінт `GET /api/v1/stats/` що повертає:
  - `total_items: int`
  - `total_users: int` (тільки для superuser)
  - `items_last_7_days: int`
  - `items_by_day: list[{date: str, count: int}]` — за останні 30 днів
- [ ] **[BE]** Для звичайного юзера — статистика тільки по його Items
- [ ] **[BE]** Для superuser — загальна статистика + total_users
- [ ] **[BE]** Написати тести для обох ролей
- [ ] **[FE]** Створити компонент `StatCard` — картка зі значенням, підписом та іконкою
- [ ] **[FE]** Розмістити 2–3 StatCards зверху Dashboard
- [ ] **[FE]** Додати лінійний графік "Items per day" за 30 днів (використати `recharts`)
- [ ] **[FE]** Оновити `frontend/src/routes/_layout/index.tsx` — замінити порожню сторінку на Dashboard

### Acceptance Criteria

```gherkin
GIVEN я авторизований як звичайний юзер з 5 Items
WHEN я відкриваю головну сторінку
THEN я бачу StatCard "Total Items: 5"
AND графік показує мої Items по днях

GIVEN я superuser
WHEN я відкриваю Dashboard
THEN я бачу загальний total_items, total_users та items_last_7_days
AND графік показує всі Items системи

GIVEN за останні 30 днів були дні без нових Items
WHEN графік рендериться
THEN ці дні відображаються з count = 0 (без пропусків)
```

### Technical Notes

- Новий файл: `backend/app/api/routes/stats.py`
- Фронтенд: `frontend/src/routes/_layout/index.tsx`
- Нові компоненти: `frontend/src/components/Dashboard/`
- Для графіка: `npm install recharts` (або додати в `package.json`)

---

## BMAD-5: Додати Favorites (Обране)

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Low |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу додавати Items до "Обраного" та переглядати їх окремим списком, щоб швидко повертатися до важливих записів.

### Subtasks

- [ ] **[BE]** Створити link-таблицю `Favorite` (user_id: FK, item_id: FK, created_at). PK = (user_id, item_id)
- [ ] **[BE]** Написати Alembic міграцію
- [ ] **[BE]** Додати `POST /api/v1/items/{item_id}/favorite` — додати до обраного (toggle: якщо вже є — 409)
- [ ] **[BE]** Додати `DELETE /api/v1/items/{item_id}/favorite` — прибрати з обраного
- [ ] **[BE]** Додати `GET /api/v1/items/favorites/` — список обраних Items поточного юзера
- [ ] **[BE]** В `GET /api/v1/items/` — додати поле `is_favorited: bool` для поточного юзера
- [ ] **[FE]** Додати іконку ☆/★ в кожний рядок таблиці Items
- [ ] **[FE]** Клік по зірочці — toggle favorite з оптимістичним оновленням UI
- [ ] **[FE]** Додати фільтр "Show favorites only" (toggle/checkbox) на сторінці Items
- [ ] **[FE]** Показувати badge "Favorites (N)" в sidebar

### Acceptance Criteria

```gherkin
GIVEN я бачу Item без зірочки (☆)
WHEN я натискаю на зірочку
THEN вона стає заповненою (★) миттєво (оптимістично)
AND API отримує POST /api/v1/items/{id}/favorite

GIVEN я увімкнув фільтр "Show favorites only"
WHEN в мене 2 з 10 Items обрані
THEN в таблиці відображаються тільки 2 Items

GIVEN User A додав Item до обраного
WHEN User B переглядає той самий Item
THEN зірочка для User B порожня (☆) — обране не спільне
```

### Technical Notes

- Модифікувати: `backend/app/models.py`, `backend/app/api/routes/items.py`
- Фронтенд: `frontend/src/components/Items/columns.tsx`, `frontend/src/routes/_layout/items.tsx`
- Для оптимістичного оновлення використовувати `useMutation` з `onMutate` (TanStack Query)

---

## BMAD-6: Експорт Items у CSV

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Low |
| **Story Points** | 3 |
| **Labels** | `backend`, `frontend`, `export` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як користувач, я хочу експортувати свої Items у CSV-файл, щоб використовувати дані в Excel або інших інструментах.

### Subtasks

- [ ] **[BE]** Додати ендпоінт `GET /api/v1/items/export/csv`
- [ ] **[BE]** Використати `StreamingResponse` з `text/csv` Content-Type
- [ ] **[BE]** Колонки: id, title, description, created_at
- [ ] **[BE]** Для superuser: додати колонку owner_email, експортувати всі Items
- [ ] **[BE]** Для звичайного юзера: тільки свої Items
- [ ] **[BE]** Header: `Content-Disposition: attachment; filename="items_export.csv"`
- [ ] **[BE]** Забезпечити UTF-8 кодування (BOM для Excel)
- [ ] **[FE]** Додати кнопку "Export CSV" поряд з "Add Item"
- [ ] **[FE]** Показувати loading-стан під час завантаження
- [ ] **[FE]** Disabled + tooltip "No items to export" якщо Items = 0

### Acceptance Criteria

```gherkin
GIVEN я маю 3 Items
WHEN я натискаю "Export CSV"
THEN браузер скачує файл items_export.csv
AND файл містить 4 рядки (header + 3 Items)
AND кирилиця відображається коректно в Excel

GIVEN я superuser
WHEN я експортую CSV
THEN файл містить всі Items системи з колонкою owner_email

GIVEN я маю 0 Items
WHEN я дивлюсь на кнопку "Export CSV"
THEN вона неактивна (disabled) з тултіпом
```

### Technical Notes

- Файл бекенду: `backend/app/api/routes/items.py`
- Для CSV: модуль `csv` + `io.StringIO` з Python stdlib
- Для download на фронтенді: `window.open()` або `fetch` + `Blob` + `URL.createObjectURL`

---

## BMAD-7: Журнал дій (Activity Log)

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | High |
| **Story Points** | 13 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature`, `admin` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

Як superuser, я хочу бачити журнал всіх дій в системі (створення, оновлення, видалення), щоб відстежувати активність користувачів та проводити аудит.

### Subtasks

- [ ] **[BE]** Створити модель `ActivityLog`:
  - id: UUID
  - user_id: FK → User (nullable, для видалених юзерів)
  - action: Enum (CREATED, UPDATED, DELETED)
  - entity_type: str ("item" | "user")
  - entity_id: UUID
  - details: JSON (optional, наприклад changed fields)
  - created_at: datetime
- [ ] **[BE]** Написати Alembic міграцію
- [ ] **[BE]** Створити helper-функцію `log_activity(session, user_id, action, entity_type, entity_id, details=None)`
- [ ] **[BE]** Додати виклик `log_activity` в:
  - Items: create, update, delete
  - Users: create, delete
- [ ] **[BE]** Додати ендпоінт `GET /api/v1/activity-log/` (superuser only):
  - Пагінація: skip, limit
  - Фільтри: user_id, entity_type, action, date_from, date_to
  - Response включає user email/name
- [ ] **[BE]** Написати тести: permissions (403 для не-superuser), фільтрація
- [ ] **[FE]** Додати пункт "Activity Log" в sidebar (видимий тільки superuser)
- [ ] **[FE]** Створити роут `/_layout/activity.tsx`
- [ ] **[FE]** Компонент `ActivityTable` — таблиця з колонками: User, Action, Entity Type, Entity ID, Date
- [ ] **[FE]** Кольорове маркування action: зелений badge (CREATED), жовтий (UPDATED), червоний (DELETED)
- [ ] **[FE]** Фільтри: dropdown Action, dropdown Entity Type, date range

### Acceptance Criteria

```gherkin
GIVEN я створив новий Item
WHEN superuser відкриває Activity Log
THEN він бачить запис: user=my_email, action=CREATED, entity=item, date=now

GIVEN superuser застосовує фільтр action=DELETED + entity_type=item
WHEN завантажується список
THEN відображаються тільки записи про видалені Items

GIVEN я звичайний юзер
WHEN я намагаюсь відкрити /activity
THEN я отримую 403 або redirect

GIVEN superuser видалив юзера
WHEN він перевіряє Activity Log
THEN є запис з action=DELETED, entity_type=user
```

### Technical Notes

- Нові файли бекенду: `backend/app/api/routes/activity.py`, можливо `backend/app/activity.py` для helper
- Новий роут: `frontend/src/routes/_layout/activity.tsx`
- Нові компоненти: `frontend/src/components/Activity/`
- Зареєструвати роутер в `backend/app/api/main.py`
- Sidebar: `frontend/src/components/Sidebar/Main.tsx`
- Для date range: можна використати два input[type="date"] або бібліотеку

---

## Зведена таблиця Backlog

| Key | Summary | Type | Priority | SP | Labels |
|---|---|---|---|---|---|
| BMAD-1 | Теги для Items | Story | Medium | 5 | backend, frontend, database |
| BMAD-2 | Пошук та сортування Items | Story | High | 3 | backend, frontend |
| BMAD-3 | Коментарі до Items | Story | Medium | 8 | backend, frontend, database |
| BMAD-4 | Dashboard зі статистикою | Story | Medium | 5 | backend, frontend, analytics |
| BMAD-5 | Favorites (Обране) | Story | Low | 5 | backend, frontend, database |
| BMAD-6 | Експорт Items у CSV | Story | Low | 3 | backend, frontend, export |
| BMAD-7 | Activity Log | Story | High | 13 | backend, frontend, database, admin |

**Total Story Points: 42**
