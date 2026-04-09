# Backlog — Full Stack FastAPI Template

> Project: **BMAD-DEMO**
> Board: Kanban
> Stack: FastAPI · React · TypeScript · PostgreSQL · SQLModel · Docker

---

## BMAD-1: Add Tag System for Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to be able to add tags to Items so that I can categorize and quickly filter my records.

Currently the Item model only has `title` and `description`. We need to add a Tag entity and a many-to-many relationship between Item and Tag.

### Subtasks

- [ ] **[BE]** Create `Tag` model (id: UUID, name: str unique, max 50 chars)
- [ ] **[BE]** Create `ItemTag` link table (item_id FK, tag_id FK)
- [ ] **[BE]** Write Alembic migration
- [ ] **[BE]** Add endpoint `GET /api/v1/tags/` — list tags
- [ ] **[BE]** Add endpoint `POST /api/v1/tags/` — create a tag
- [ ] **[BE]** Modify `POST /PUT /api/v1/items/` — accept `tag_ids: list[UUID]`
- [ ] **[BE]** Modify `GET /api/v1/items/` — return tags in response + add `tag_id` query parameter for filtering
- [ ] **[FE]** Create `TagBadge` component — colored badge
- [ ] **[FE]** Add tag multi-select to Item create/edit form
- [ ] **[FE]** Show tags in the Items table column
- [ ] **[FE]** Add tag filter dropdown on the Items page

### Acceptance Criteria

```gherkin
GIVEN I am an authenticated user
WHEN I create a new tag "urgent" via API
THEN the tag appears in GET /api/v1/tags/

GIVEN tag "urgent" exists
WHEN I create an Item with tag_ids: ["<urgent_id>"]
THEN the Item is returned with tags: [{id, name: "urgent"}]

GIVEN Items with different tags exist
WHEN I filter by tag "urgent" on the frontend
THEN the table shows only Items with the "urgent" tag
```

### Technical Notes

- Backend files: `backend/app/models.py`, `backend/app/api/routes/items.py`, new `backend/app/api/routes/tags.py`
- Frontend files: `frontend/src/components/Items/`, `frontend/src/routes/_layout/items.tsx`
- Register the new router in `backend/app/api/main.py`

---

## BMAD-2: Implement Search and Sorting for Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | High |
| **Story Points** | 3 |
| **Labels** | `backend`, `frontend`, `ux-improvement` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to be able to search Items by title/description and sort the list, so that I can quickly find the records I need.

Currently `GET /api/v1/items/` only supports `skip` and `limit`.

### Subtasks

- [ ] **[BE]** Add query parameter `search: str` to `GET /api/v1/items/` — ILIKE on `title` and `description`
- [ ] **[BE]** Add query parameter `sort_by: str` (options: `created_at_asc`, `created_at_desc`, `title_asc`, `title_desc`)
- [ ] **[BE]** Update count query to respect the search filter
- [ ] **[BE]** Write tests for the new parameters
- [ ] **[FE]** Add input with Search icon above the Items table
- [ ] **[FE]** Implement 300ms debounce on the search input
- [ ] **[FE]** Add sort dropdown (Newest / Oldest / A→Z / Z→A)
- [ ] **[FE]** Show "Found N items" below the search bar

### Acceptance Criteria

```gherkin
GIVEN Items with title "Test Report" and "Monthly Summary" exist
WHEN I type "test" in the search field
THEN only "Test Report" is shown in the table
AND "Found 1 item" is displayed

GIVEN I have entered a search query
WHEN I type quickly
THEN the API request is sent only 300ms after the last keystroke

GIVEN I selected sorting "A→Z"
WHEN Items are loaded
THEN they are sorted by title in alphabetical order
```

### Technical Notes

- Backend files: `backend/app/api/routes/items.py`
- Frontend files: `frontend/src/routes/_layout/items.tsx`
- For debounce use `useDeferredValue` from React or a custom hook

---

## BMAD-3: Add Comments to Items

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 8 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to leave comments on Items and see comments from other users, so that I can discuss records with my team.

We need to create a new model, API endpoints, and a new Item detail page.

### Subtasks

- [ ] **[BE]** Create `Comment` model (id: UUID, text: str max 1000, created_at, item_id: FK → Item, author_id: FK → User)
- [ ] **[BE]** Add Relationships: Item.comments, User.comments
- [ ] **[BE]** Write Alembic migration
- [ ] **[BE]** Add `GET /api/v1/items/{item_id}/comments/` — list of comments (paginated, include author name/email)
- [ ] **[BE]** Add `POST /api/v1/items/{item_id}/comments/` — create a comment (author = current_user)
- [ ] **[BE]** Add `DELETE /api/v1/items/{item_id}/comments/{comment_id}` — delete (author or superuser)
- [ ] **[BE]** Write permission tests (author can delete their own, superuser — any, others — 403)
- [ ] **[FE]** Create route `/items/$itemId` — Item detail page
- [ ] **[FE]** `CommentList` component — scrollable comment list
- [ ] **[FE]** `CommentForm` component — textarea + "Send" button
- [ ] **[FE]** `CommentCard` component — text, author, date, conditional Delete button
- [ ] **[FE]** Make Item title in the table clickable → navigate to detail page

### Acceptance Criteria

```gherkin
GIVEN I am authenticated and on the Item detail page
WHEN I enter a comment and click "Send"
THEN the comment appears in the list with my name and the current date

GIVEN a comment was left by another user
WHEN I (not a superuser) try to delete it
THEN the Delete button is not shown
AND the API returns 403

GIVEN I am a superuser
WHEN I click Delete on any comment
THEN the comment is deleted

GIVEN I am on the Items page
WHEN I click on an Item title
THEN I am redirected to /items/{id} with details and comments
```

### Technical Notes

- New file: `backend/app/api/routes/comments.py`
- New route: `frontend/src/routes/_layout/items/$itemId.tsx`
- New components: `frontend/src/components/Comments/`
- Register the comments router in `backend/app/api/main.py`

---

## BMAD-4: Create Dashboard with Statistics

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Medium |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `new-feature`, `analytics` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to see a Dashboard with basic statistics on the home page, so that I have a general overview of the system state.

Currently the home page (`/`) is essentially empty.

### Subtasks

- [ ] **[BE]** Create endpoint `GET /api/v1/stats/` that returns:
  - `total_items: int`
  - `total_users: int` (superuser only)
  - `items_last_7_days: int`
  - `items_by_day: list[{date: str, count: int}]` — last 30 days
- [ ] **[BE]** For regular user — statistics for their Items only
- [ ] **[BE]** For superuser — system-wide statistics + total_users
- [ ] **[BE]** Write tests for both roles
- [ ] **[FE]** Create `StatCard` component — card with value, label and icon
- [ ] **[FE]** Display 2–3 StatCards at the top of the Dashboard
- [ ] **[FE]** Add a "Items per day" line chart for the last 30 days (use `recharts`)
- [ ] **[FE]** Update `frontend/src/routes/_layout/index.tsx` — replace empty page with Dashboard

### Acceptance Criteria

```gherkin
GIVEN I am authenticated as a regular user with 5 Items
WHEN I open the home page
THEN I see StatCard "Total Items: 5"
AND the chart shows my Items by day

GIVEN I am a superuser
WHEN I open the Dashboard
THEN I see total_items, total_users, and items_last_7_days
AND the chart shows all Items in the system

GIVEN there were days in the last 30 days with no new Items
WHEN the chart renders
THEN those days are shown with count = 0 (no gaps)
```

### Technical Notes

- New file: `backend/app/api/routes/stats.py`
- Frontend: `frontend/src/routes/_layout/index.tsx`
- New components: `frontend/src/components/Dashboard/`
- For the chart: `npm install recharts` (or add to `package.json`)

---

## BMAD-5: Add Favorites

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Low |
| **Story Points** | 5 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to add Items to "Favorites" and view them as a separate list, so that I can quickly return to important records.

### Subtasks

- [ ] **[BE]** Create `Favorite` link table (user_id: FK, item_id: FK, created_at). PK = (user_id, item_id)
- [ ] **[BE]** Write Alembic migration
- [ ] **[BE]** Add `POST /api/v1/items/{item_id}/favorite` — add to favorites (toggle: 409 if already exists)
- [ ] **[BE]** Add `DELETE /api/v1/items/{item_id}/favorite` — remove from favorites
- [ ] **[BE]** Add `GET /api/v1/items/favorites/` — list of favorited Items for the current user
- [ ] **[BE]** In `GET /api/v1/items/` — add `is_favorited: bool` field for the current user
- [ ] **[FE]** Add ☆/★ icon in each row of the Items table
- [ ] **[FE]** Click on the star — toggle favorite with optimistic UI update
- [ ] **[FE]** Add "Show favorites only" filter (toggle/checkbox) on the Items page
- [ ] **[FE]** Show "Favorites (N)" badge in the sidebar

### Acceptance Criteria

```gherkin
GIVEN I see an Item without a star (☆)
WHEN I click on the star
THEN it becomes filled (★) immediately (optimistically)
AND the API receives POST /api/v1/items/{id}/favorite

GIVEN I enabled the "Show favorites only" filter
WHEN I have 2 out of 10 Items favorited
THEN the table shows only those 2 Items

GIVEN User A added an Item to favorites
WHEN User B views the same Item
THEN the star for User B is empty (☆) — favorites are not shared
```

### Technical Notes

- Modify: `backend/app/models.py`, `backend/app/api/routes/items.py`
- Frontend: `frontend/src/components/Items/columns.tsx`, `frontend/src/routes/_layout/items.tsx`
- For optimistic update use `useMutation` with `onMutate` (TanStack Query)

---

## BMAD-6: Export Items to CSV

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | Low |
| **Story Points** | 3 |
| **Labels** | `backend`, `frontend`, `export` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a user, I want to export my Items to a CSV file, so that I can use the data in Excel or other tools.

### Subtasks

- [ ] **[BE]** Add endpoint `GET /api/v1/items/export/csv`
- [ ] **[BE]** Use `StreamingResponse` with `text/csv` Content-Type
- [ ] **[BE]** Columns: id, title, description, created_at
- [ ] **[BE]** For superuser: add owner_email column, export all Items
- [ ] **[BE]** For regular user: only their own Items
- [ ] **[BE]** Header: `Content-Disposition: attachment; filename="items_export.csv"`
- [ ] **[BE]** Ensure UTF-8 encoding (BOM for Excel)
- [ ] **[FE]** Add "Export CSV" button next to "Add Item"
- [ ] **[FE]** Show loading state while downloading
- [ ] **[FE]** Disabled + tooltip "No items to export" when Items = 0

### Acceptance Criteria

```gherkin
GIVEN I have 3 Items
WHEN I click "Export CSV"
THEN the browser downloads items_export.csv
AND the file contains 4 lines (header + 3 Items)
AND special characters display correctly in Excel

GIVEN I am a superuser
WHEN I export CSV
THEN the file contains all Items in the system with an owner_email column

GIVEN I have 0 Items
WHEN I look at the "Export CSV" button
THEN it is disabled with a tooltip
```

### Technical Notes

- Backend file: `backend/app/api/routes/items.py`
- For CSV: `csv` module + `io.StringIO` from Python stdlib
- For download on the frontend: `window.open()` or `fetch` + `Blob` + `URL.createObjectURL`

---

## BMAD-7: Activity Log

| Field | Value |
|---|---|
| **Type** | Story |
| **Priority** | High |
| **Story Points** | 13 |
| **Labels** | `backend`, `frontend`, `database`, `new-feature`, `admin` |
| **Sprint** | Backlog |
| **Assignee** | Unassigned |

### Description

As a superuser, I want to see an activity log of all actions in the system (create, update, delete) so that I can track user activity and perform audits.

### Subtasks

- [ ] **[BE]** Create `ActivityLog` model:
  - id: UUID
  - user_id: FK → User (nullable, for deleted users)
  - action: Enum (CREATED, UPDATED, DELETED)
  - entity_type: str ("item" | "user")
  - entity_id: UUID
  - details: JSON (optional, e.g. changed fields)
  - created_at: datetime
- [ ] **[BE]** Write Alembic migration
- [ ] **[BE]** Create helper function `log_activity(session, user_id, action, entity_type, entity_id, details=None)`
- [ ] **[BE]** Add `log_activity` calls to:
  - Items: create, update, delete
  - Users: create, delete
- [ ] **[BE]** Add endpoint `GET /api/v1/activity-log/` (superuser only):
  - Pagination: skip, limit
  - Filters: user_id, entity_type, action, date_from, date_to
  - Response includes user email/name
- [ ] **[BE]** Write tests: permissions (403 for non-superuser), filtering
- [ ] **[FE]** Add "Activity Log" item in the sidebar (superuser only)
- [ ] **[FE]** Create route `/_layout/activity.tsx`
- [ ] **[FE]** `ActivityTable` component — table with columns: User, Action, Entity Type, Entity ID, Date
- [ ] **[FE]** Color-coded action badges: green (CREATED), yellow (UPDATED), red (DELETED)
- [ ] **[FE]** Filters: Action dropdown, Entity Type dropdown, date range

### Acceptance Criteria

```gherkin
GIVEN I created a new Item
WHEN the superuser opens Activity Log
THEN they see a record: user=my_email, action=CREATED, entity=item, date=now

GIVEN the superuser applies filter action=DELETED + entity_type=item
WHEN the list loads
THEN only records about deleted Items are shown

GIVEN I am a regular user
WHEN I try to open /activity
THEN I receive 403 or a redirect

GIVEN the superuser deleted a user
WHEN they check Activity Log
THEN there is a record with action=DELETED, entity_type=user
```

### Technical Notes

- New backend files: `backend/app/api/routes/activity.py`, possibly `backend/app/activity.py` for the helper
- New route: `frontend/src/routes/_layout/activity.tsx`
- New components: `frontend/src/components/Activity/`
- Register the router in `backend/app/api/main.py`
- Sidebar: `frontend/src/components/Sidebar/Main.tsx`
- For date range: use two `input[type="date"]` elements or a library
