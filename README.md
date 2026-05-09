# DevTrack

A minimal Django backend for tracking engineering issues — bugs filed, priorities set, statuses updated.

**GitHub Repository:** https://github.com/V3669/devtrack-assignment

---

## Setup & Run

### Step 1 — Clone & install

```bash
git clone https://github.com/V3669/devtrack-assignment.git
cd devtrack-assignment

python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

### Step 2 — Start the server

```bash
python manage.py runserver
```

> **Note:** You may see a warning about unapplied migrations. This is safe to ignore — our app stores data in JSON files, not the database. The warning is from Django's built-in admin/auth apps which we don't use.

The server starts at **`http://127.0.0.1:8000/`**

### Step 3 — Verify it's running

Open `http://127.0.0.1:8000/api/issues/` in your browser. You should see `[]`.

---

## Endpoints

### Reporters

| Method | URL | Description |
|--------|-----|-------------|
| `POST` | `/api/reporters/` | Create a new reporter |
| `GET` | `/api/reporters/` | List all reporters |
| `GET` | `/api/reporters/?id=1` | Get a single reporter by ID |

**POST body example**
```json
{
  "id": 1,
  "name": "Alice Smith",
  "email": "alice@example.com",
  "team": "backend"
}
```

---

### Issues

| Method | URL | Description |
|--------|-----|-------------|
| `POST` | `/api/issues/` | Create a new issue |
| `GET` | `/api/issues/` | List all issues |
| `GET` | `/api/issues/?id=1` | Get a single issue by ID |
| `GET` | `/api/issues/?status=open` | Filter issues by status |

**POST body example**
```json
{
  "id": 1,
  "title": "Login button not working on mobile",
  "description": "Users on iOS 17 cannot tap the login button",
  "status": "open",
  "priority": "critical",
  "reporter_id": 1
}
```

**201 Created response**
```json
{
  "id": 1,
  "title": "Login button not working on mobile",
  "description": "Users on iOS 17 cannot tap the login button",
  "status": "open",
  "priority": "critical",
  "reporter_id": 1,
  "created_at": "2026-05-09 10:00:00.000000",
  "message": "[URGENT] Login button not working on mobile — needs immediate attention"
}
```

**Allowed values**

| Field | Allowed values |
|-------|---------------|
| `status` | `open`, `in_progress`, `resolved`, `closed` |
| `priority` | `low`, `medium`, `high`, `critical` |

---

## Design Decision

**OOP class hierarchy over flat dicts in views.**  
`BaseEntity` enforces a `validate()` contract on every entity. `Issue` subclasses (`CriticalIssue`, `LowPriorityIssue`) override only the `describe()` method, adding behaviour without touching validation or serialisation logic. This means adding a new priority tier (e.g. `UrgentIssue`) is a single new class — views and storage are untouched (Open/Closed Principle).

**JSON file storage** was chosen to keep the project dependency-free beyond Django itself, matching the assignment scope. `storage.py` wraps all file I/O so every view stays under 10 lines of persistence logic.

---

## Postman Screenshots

> Add screenshots of at least one success and one failure response for any endpoint here.

Example screenshots to include:
- `POST /api/issues/` — 201 Created (critical priority)
- `POST /api/issues/` — 400 Bad Request (empty title)
- `GET /api/issues/?id=99` — 404 Not Found
