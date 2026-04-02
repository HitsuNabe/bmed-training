# TASKS.md — Задачі для студентів

> Проєкт: `fastapi/full-stack-fastapi-template`
> Стек: FastAPI · React · TypeScript · PostgreSQL · SQLModel · Docker

Нижче — список задач зростаючої складності. Кожна задача зачіпає і **бекенд**, і **фронтенд**. Задачі незалежні одна від одної — можна виконувати в будь-якому порядку.

---

## Задача 1 — Теги для Items (легка)

**Контекст:** Зараз Item має лише `title` та `description`. Потрібно додати можливість призначати теги кожному Item.

### Backend

- Додати модель `Tag` (SQLModel, table=True): `id` (UUID), `name` (str, unique, max 50 символів)
- Додати зв'язок many-to-many між `Item` і `Tag` через link-таблицю `ItemTag`
- Додати ендпоінт `GET /api/v1/tags/` — список усіх тегів
- Додати ендпоінт `POST /api/v1/tags/` — створити тег
- Модифікувати `POST /api/v1/items/` і `PUT /api/v1/items/{id}` — приймати список `tag_ids`
- Модифікувати `GET /api/v1/items/` — повертати теги в кожному Item
- Написати Alembic міграцію

### Frontend

- Додати компонент `TagBadge` — відображає тег як кольоровий badge
- В формі створення/редагування Item — додати multi-select для вибору тегів
- В таблиці Items — показувати теги в окремій колонці
- Додати фільтр по тегу на сторінці Items

### Acceptance Criteria

- Можна створити тег через API
- Можна призначити 0..N тегів до Item
- Теги відображаються в таблиці Items
- Фільтр по тегу працює

---

## Задача 2 — Пошук та фільтрація Items (легка)

**Контекст:** Зараз `GET /api/v1/items/` повертає всі Items з пагінацією (skip/limit), але без пошуку чи фільтрації.

### Backend

- Додати query-параметр `search` до `GET /api/v1/items/` — пошук по `title` та `description` (case-insensitive, ILIKE)
- Додати query-параметр `sort_by` — сортування по `title`, `created_at` (asc/desc)
- Оновити count-запит щоб враховувати фільтри

### Frontend

- Додати поле пошуку (input з іконкою Search) зверху таблиці Items
- Реалізувати debounce (300ms) для пошуку — щоб не робити запит на кожну літеру
- Додати dropdown для сортування (Newest first / Oldest first / A-Z / Z-A)
- Показувати кількість знайдених результатів: "Found 5 items"

### Acceptance Criteria

- Пошук по "test" знаходить Items де title або description містять "test"
- Сортування змінює порядок елементів в таблиці
- Debounce працює — запит йде тільки після паузи в 300ms
- При порожньому пошуку показуються всі Items

---

## Задача 3 — Коментарі до Items (середня)

**Контекст:** Потрібна можливість залишати коментарі до Items. Коментарі видно всім авторизованим юзерам.

### Backend

- Додати модель `Comment` (SQLModel): `id` (UUID), `text` (str, max 1000), `created_at`, `item_id` (FK → Item), `author_id` (FK → User)
- Додати Relationship: Item → Comments (one-to-many), User → Comments (one-to-many)
- Ендпоінти:
  - `GET /api/v1/items/{item_id}/comments/` — список коментарів до Item (з пагінацією)
  - `POST /api/v1/items/{item_id}/comments/` — додати коментар (автор = поточний юзер)
  - `DELETE /api/v1/items/{item_id}/comments/{comment_id}` — видалити свій коментар (або будь-який якщо superuser)
- Повертати `author_name` та `author_email` разом з коментарем
- Написати Alembic міграцію

### Frontend

- Додати сторінку деталей Item: `/items/{id}` — показує title, description, created_at, owner та список коментарів
- Компонент `CommentList` — відображає коментарі зі скролом
- Компонент `CommentForm` — textarea + кнопка "Send"
- Компонент `CommentCard` — текст, автор, дата, кнопка видалення (якщо автор або superuser)
- В таблиці Items — зробити title клікабельним (посилання на деталі)

### Acceptance Criteria

- Авторизований юзер може залишити коментар
- Коментарі відображаються в хронологічному порядку
- Автор або superuser може видалити коментар
- Неавторизований юзер не може коментувати (401)
- Клік на Item в таблиці відкриває сторінку деталей

---

## Задача 4 — Dashboard зі статистикою (середня)

**Контекст:** Головна сторінка (`/`) зараз порожня. Потрібно створити Dashboard з базовою статистикою.

### Backend

- Додати ендпоінт `GET /api/v1/stats/` який повертає:
  - `total_items` — кількість Items (юзера або всіх якщо superuser)
  - `total_users` — кількість юзерів (тільки для superuser)
  - `items_last_7_days` — кількість Items створених за останні 7 днів
  - `items_by_day` — масив об'єктів `{ date: "2025-01-15", count: 3 }` за останні 30 днів
- Для звичайного юзера — статистика тільки по його Items
- Для superuser — загальна статистика по системі

### Frontend

- Створити сторінку Dashboard на `/` (замість поточної порожньої)
- Компонент `StatCard` — картка зі значенням та підписом (напр. "Total Items: 42")
- Відобразити 2–3 StatCard зверху сторінки
- Додати графік "Items created per day" (останні 30 днів) — використати `recharts` (бібліотека вже доступна)
- Графік повинен коректно показувати дні з 0 Items

### Acceptance Criteria

- Dashboard показує актуальну статистику
- Графік відображає Items по днях за 30 днів
- Звичайний юзер бачить тільки свою статистику
- Superuser бачить загальну + кількість юзерів

---

## Задача 5 — Favorites / Обране (середня)

**Контекст:** Юзер хоче мати можливість додавати Items до "Обраного" і переглядати їх окремим списком.

### Backend

- Додати link-таблицю `Favorite`: `user_id` (FK → User), `item_id` (FK → Item), `created_at`. Composite primary key: (user_id, item_id)
- Ендпоінти:
  - `POST /api/v1/items/{item_id}/favorite` — додати до обраного
  - `DELETE /api/v1/items/{item_id}/favorite` — прибрати з обраного
  - `GET /api/v1/items/favorites/` — список обраних Items поточного юзера
- В `GET /api/v1/items/` — додати поле `is_favorited: bool` для кожного Item (для поточного юзера)
- Написати Alembic міграцію

### Frontend

- Додати іконку ☆/★ (star) в кожному рядку таблиці Items
- Клік по зірочці — toggle favorite (оптимістичне оновлення UI)
- Додати вкладку "Favorites" в sidebar або фільтр "Show favorites only" на сторінці Items
- Показувати кількість обраних в sidebar: "Favorites (3)"

### Acceptance Criteria

- Клік по зірочці додає/прибирає Item з обраного
- Список Favorites показує тільки обрані Items
- Перезавантаження сторінки зберігає стан обраного
- Один юзер не бачить обране іншого

---

## Задача 6 — Експорт Items у CSV (легка)

**Контекст:** Юзер хоче мати можливість завантажити свої Items у форматі CSV.

### Backend

- Додати ендпоінт `GET /api/v1/items/export/csv` — повертає CSV-файл (StreamingResponse)
- CSV повинен містити колонки: `id`, `title`, `description`, `created_at`
- Для superuser — експорт всіх Items (з додатковою колонкою `owner_email`)
- Для звичайного юзера — тільки свої Items
- Content-Type: `text/csv`, Content-Disposition: `attachment; filename="items_export.csv"`

### Frontend

- Додати кнопку "Export CSV" поряд з "Add Item" на сторінці Items
- Клік по кнопці — скачує файл через браузер
- Під час завантаження — показувати loading-стан на кнопці
- Якщо Items = 0, кнопка неактивна (disabled) з тултіпом "No items to export"

### Acceptance Criteria

- CSV-файл завантажується з правильними даними
- Кодування UTF-8 (кирилиця не ламається)
- Superuser бачить всі Items з email власника
- Порожній список — кнопка disabled

---

## Задача 7 — Activity Log / Журнал дій (складна)

**Контекст:** Superuser хоче бачити журнал дій — хто що робив в системі.

### Backend

- Додати модель `ActivityLog`: `id` (UUID), `user_id` (FK → User), `action` (enum: CREATED, UPDATED, DELETED), `entity_type` (str: "item" | "user"), `entity_id` (UUID), `details` (JSON, optional), `created_at`
- Створити middleware або dependency який автоматично логує дії при:
  - Створенні/оновленні/видаленні Item
  - Створенні/видаленні User
- Ендпоінти (тільки для superuser):
  - `GET /api/v1/activity-log/` — список дій з пагінацією та фільтрами (по user_id, entity_type, action, date range)
- Написати Alembic міграцію

### Frontend

- Додати сторінку "Activity Log" в sidebar (видима тільки superuser)
- Створити роут `/_layout/activity.tsx`
- Компонент `ActivityTable` — таблиця з колонками: User, Action, Entity, Date
- Фільтри: dropdown по Action (Created/Updated/Deleted), dropdown по Entity Type (Item/User), date range picker
- Кольорове маркування: зелений (created), жовтий (updated), червоний (deleted)

### Acceptance Criteria

- Кожна дія (CRUD) автоматично логується
- Superuser бачить журнал з фільтрами
- Звичайний юзер не має доступу до Activity Log (403)
- Фільтри комбінуються (наприклад: "всі DELETE за останній тиждень")

---

## Загальна таблиця

| # | Задача | Складність | Бекенд | Фронтенд |
|---|---|---|---|---|
| 1 | Теги для Items | Легка | Модель + M2M + ендпоінти | Multi-select + badges + фільтр |
| 2 | Пошук та фільтрація | Легка | Query params + ILIKE | Search input + debounce + sort |
| 3 | Коментарі | Середня | Модель + CRUD + permissions | Деталі Item + коментарі |
| 4 | Dashboard | Середня | Aggregation ендпоінт | StatCards + recharts графік |
| 5 | Favorites | Середня | Link-таблиця + toggle | Star icon + optimistic update |
| 6 | CSV Export | Легка | StreamingResponse + CSV | Download button + loading |
| 7 | Activity Log | Складна | Middleware + модель + фільтри | Нова сторінка + таблиця + фільтри |
