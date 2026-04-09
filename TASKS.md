# TASKS.md — Student Tasks

> Project: `fastapi/full-stack-fastapi-template`
> Stack: FastAPI · React · TypeScript · PostgreSQL · SQLModel · Docker

A list of tasks in increasing order of complexity. Each task covers both **backend** and **frontend**. Tasks are independent — they can be completed in any order.

---

## Task 1 — Tags for Items (Easy)

**Context:** An Item currently has only `title` and `description`. Add the ability to assign tags to each Item.

### Backend

- Add a `Tag` model (SQLModel, table=True): `id` (UUID), `name` (str, unique, max 50 chars)
- Add a many-to-many relationship between `Item` and `Tag` via an `ItemTag` link table
- Add endpoint `GET /api/v1/tags/` — list all tags
- Add endpoint `POST /api/v1/tags/` — create a tag
- Modify `POST /api/v1/items/` and `PUT /api/v1/items/{id}` — accept a list of `tag_ids`
- Modify `GET /api/v1/items/` — return tags in each Item
- Write an Alembic migration

### Frontend

- Add a `TagBadge` component — displays a tag as a colored badge
- In the Item create/edit form — add a multi-select for tag selection
- In the Items table — show tags in a separate column
- Add a tag filter on the Items page

### Acceptance Criteria

- A tag can be created via the API
- 0..N tags can be assigned to an Item
- Tags are displayed in the Items table
- Tag filter works correctly

---

## Task 2 — Search and Filtering for Items (Easy)

**Context:** `GET /api/v1/items/` currently returns all Items with pagination (skip/limit), but without search or filtering.

### Backend

- Add query parameter `search` to `GET /api/v1/items/` — search by `title` and `description` (case-insensitive, ILIKE)
- Add query parameter `sort_by` — sort by `title`, `created_at` (asc/desc)
- Update the count query to respect filters

### Frontend

- Add a search field (input with Search icon) above the Items table
- Implement debounce (300ms) — to avoid firing a request on every keystroke
- Add a sort dropdown (Newest first / Oldest first / A-Z / Z-A)
- Show the number of results: "Found 5 items"

### Acceptance Criteria

- Searching "test" finds Items where title or description contain "test"
- Sorting changes the order of items in the table
- Debounce works — request fires only after a 300ms pause
- Empty search shows all Items

---

## Task 3 — Comments on Items (Medium)

**Context:** Users should be able to leave comments on Items. Comments are visible to all authenticated users.

### Backend

- Add a `Comment` model (SQLModel): `id` (UUID), `text` (str, max 1000), `created_at`, `item_id` (FK → Item), `author_id` (FK → User)
- Add relationships: Item → Comments (one-to-many), User → Comments (one-to-many)
- Endpoints:
  - `GET /api/v1/items/{item_id}/comments/` — list comments (with pagination)
  - `POST /api/v1/items/{item_id}/comments/` — add a comment (author = current user)
  - `DELETE /api/v1/items/{item_id}/comments/{comment_id}` — delete own comment (or any if superuser)
- Return `author_name` and `author_email` with each comment
- Write an Alembic migration

### Frontend

- Add an Item detail page `/items/{id}` — shows title, description, created_at, owner, and comments
- `CommentList` component — displays comments with scroll
- `CommentForm` component — textarea + "Send" button
- `CommentCard` component — text, author, date, delete button (if author or superuser)
- In the Items table — make the title clickable (link to detail page)

### Acceptance Criteria

- Authenticated user can leave a comment
- Comments are displayed in chronological order
- Author or superuser can delete a comment
- Unauthenticated user gets 401
- Clicking an Item title opens the detail page

---

## Task 4 — Dashboard with Statistics (Medium)

**Context:** The home page (`/`) is currently empty. Create a Dashboard with basic statistics.

### Backend

- Add endpoint `GET /api/v1/stats/` returning:
  - `total_items` — number of Items (user's own, or all if superuser)
  - `total_users` — number of users (superuser only)
  - `items_last_7_days` — Items created in the last 7 days
  - `items_by_day` — array of `{ date: "2025-01-15", count: 3 }` for the last 30 days
- Regular user sees only their own stats; superuser sees system-wide stats

### Frontend

- Create a Dashboard page at `/` (replacing the current empty page)
- `StatCard` component — card with a value and label (e.g. "Total Items: 42")
- Display 2–3 StatCards at the top of the page
- Add a "Items created per day" chart using `recharts` (already available in the project)
- Chart must correctly show days with 0 Items

### Acceptance Criteria

- Dashboard shows up-to-date statistics
- Chart displays Items per day for 30 days
- Regular user sees only their own stats
- Superuser sees overall stats + number of users

---

## Task 5 — Favorites (Medium)

**Context:** Users want to mark Items as favorites and view them in a separate list.

### Backend

- Add a `Favorite` link table: `user_id` (FK → User), `item_id` (FK → Item), `created_at`. Composite primary key: (user_id, item_id)
- Endpoints:
  - `POST /api/v1/items/{item_id}/favorite` — add to favorites
  - `DELETE /api/v1/items/{item_id}/favorite` — remove from favorites
  - `GET /api/v1/items/favorites/` — list favorited Items for current user
- In `GET /api/v1/items/` — add field `is_favorited: bool` per Item (for current user)
- Write an Alembic migration

### Frontend

- Add a ☆/★ icon in each row of the Items table
- Clicking the star toggles favorite (optimistic UI update)
- Add a "Favorites" tab in the sidebar or "Show favorites only" filter on the Items page
- Show count in sidebar: "Favorites (3)"

### Acceptance Criteria

- Clicking the star adds/removes the Item from favorites
- The Favorites list shows only favorited Items
- Page reload preserves the favorite state
- Users cannot see each other's favorites

---

## Task 6 — Export Items to CSV (Easy)

**Context:** Users want to download their Items in CSV format.

### Backend

- Add endpoint `GET /api/v1/items/export/csv` — returns a CSV file (StreamingResponse)
- CSV columns: `id`, `title`, `description`, `created_at`
- Superuser — export all Items with an extra `owner_email` column
- Regular user — only their own Items
- Content-Type: `text/csv`, Content-Disposition: `attachment; filename="items_export.csv"`

### Frontend

- Add an "Export CSV" button next to "Add Item" on the Items page
- Show loading state on the button during download
- If Items count = 0, button is disabled with tooltip "No items to export"

### Acceptance Criteria

- CSV downloads with correct data
- UTF-8 encoding (special characters render correctly)
- Superuser sees all Items with owner email
- Empty list — button is disabled

---

## Task 7 — Activity Log (Hard)

**Context:** The superuser wants to see an activity log — who did what in the system.

### Backend

- Add an `ActivityLog` model: `id` (UUID), `user_id` (FK → User), `action` (enum: CREATED, UPDATED, DELETED), `entity_type` (str: "item" | "user"), `entity_id` (UUID), `details` (JSON, optional), `created_at`
- Middleware or dependency that auto-logs actions on Item and User CRUD
- Endpoint `GET /api/v1/activity-log/` with pagination and filters (user_id, entity_type, action, date range) — superuser only
- Write an Alembic migration

### Frontend

- Add "Activity Log" page in the sidebar (visible to superuser only)
- Create route `/_layout/activity.tsx`
- `ActivityTable` component — columns: User, Action, Entity, Date
- Filters: Action dropdown, Entity Type dropdown, date range picker
- Color coding: green (created), yellow (updated), red (deleted)

### Acceptance Criteria

- Every CRUD action is automatically logged
- Superuser can view and filter the log
- Regular user gets 403
- Filters can be combined (e.g. "all DELETE actions in the past week")

---

## Summary Table

| # | Task | Difficulty | Backend | Frontend |
|---|------|------------|---------|----------|
| 1 | Tags for Items | Easy | Model + M2M + endpoints | Multi-select + badges + filter |
| 2 | Search & Filtering | Easy | Query params + ILIKE | Search input + debounce + sort |
| 3 | Comments | Medium | Model + CRUD + permissions | Item detail + comments |
| 4 | Dashboard | Medium | Aggregation endpoint | StatCards + recharts chart |
| 5 | Favorites | Medium | Link table + toggle | Star icon + optimistic update |
| 6 | CSV Export | Easy | StreamingResponse + CSV | Download button + loading |
| 7 | Activity Log | Hard | Middleware + model + filters | New page + table + filters |
