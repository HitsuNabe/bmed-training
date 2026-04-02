# SETUP_AGENT.md — fastapi/full-stack-fastapi-template

## Instructions for Agent

You are an autonomous setup agent. Your goal is to clone, configure, and run the `fastapi/full-stack-fastapi-template` project using Docker Compose. Follow every step in order. Do not skip steps. After each step — verify it succeeded before proceeding.

If a step fails — diagnose the error, fix it, and retry before moving on. Report the result at the end.

---

## Step 1 — Check Prerequisites

### 1.1 Check Docker

```bash
docker --version
```

Expected: `Docker version 24.x` or higher. If not installed — direct the user to https://docs.docker.com/get-docker/ and stop.

### 1.2 Check Docker Compose

```bash
docker compose version
```

Expected: `Docker Compose version v2.x` or higher.

> Note: older systems may use `docker-compose` (with a dash). If `docker compose` fails, try `docker-compose version`.

### 1.3 Check Docker daemon is running

```bash
docker info
```

Expected: output with server info. If you see `Cannot connect to the Docker daemon` — Docker Desktop is not running. Ask the user to start it, then retry.

### 1.4 Check git

```bash
git --version
```

---

## Step 2 — Clone the Repository

```bash
git clone https://github.com/fastapi/full-stack-fastapi-template.git
cd full-stack-fastapi-template
```

Verify the folder contents:

```bash
ls -la
```

Expected files: `compose.yml`, `compose.override.yml`, `.env`, `backend/`, `frontend/`

---

## Step 3 — Configure Environment Variables

The project ships with a working `.env` file that is pre-configured for local development. For this setup, the defaults are fine. Verify the file exists:

```bash
cat .env
```

The key values for local use are already set:

| Variable | Default value |
|---|---|
| `DOMAIN` | `localhost` |
| `FIRST_SUPERUSER` | `admin@example.com` |
| `FIRST_SUPERUSER_PASSWORD` | `changethis` |
| `POSTGRES_PASSWORD` | `changethis` |
| `SECRET_KEY` | `changethis` |

> These defaults are fine for local development. Do NOT use them in production.

No changes needed — proceed to the next step.

---

## Step 4 — Build and Start All Services

```bash
docker compose up -d --build
```

This command will:
- Pull base images (Python, Node, PostgreSQL) — **may take 5–10 minutes on first run**
- Build the backend and frontend containers
- Start all services in the background

Verify all containers started:

```bash
docker compose ps
```

Expected: all services with status `running` (or `Up`):

```
NAME                            STATUS
full-stack-fastapi-project-db-1          Up
full-stack-fastapi-project-backend-1     Up
full-stack-fastapi-project-frontend-1    Up
```

If any container is not running, check its logs:

```bash
docker compose logs <service-name>
```

---

## Step 5 — Verify All Services Are Running

### 5.1 Backend API

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:8000/api/v1/utils/health-check/
```

Expected: `200`

### 5.2 API Docs (Swagger UI)

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:8000/docs
```

Expected: `200`

### 5.3 Frontend

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:5173
```

Expected: `200`

---

## Step 6 — Verify Login Works

Test the superuser login via the API:

```bash
curl -s -X POST http://localhost:8000/api/v1/login/access-token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=admin@example.com&password=changethis" \
  | python3 -m json.tool
```

Expected: JSON response containing `access_token`.

---

## Step 7 — Final Report

After all steps succeed, report the following:

```
✅ Setup complete!

Frontend:    http://localhost:5173
Backend API: http://localhost:8000
API Docs:    http://localhost:8000/docs
Admin login: admin@example.com / changethis

Stack: FastAPI · React · PostgreSQL · Docker

To stop all services:
  docker compose down

To stop and remove all data (full reset):
  docker compose down -v

Project folder: <absolute_path>/full-stack-fastapi-template
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `Cannot connect to Docker daemon` | Start Docker Desktop and retry Step 4 |
| Port 8000 already in use | `lsof -ti:8000 \| xargs kill -9` then retry |
| Port 5173 already in use | `lsof -ti:5173 \| xargs kill -9` then retry |
| Container exits immediately | Run `docker compose logs backend` to see the error |
| `changethis` password rejected | Check `.env` file — `FIRST_SUPERUSER_PASSWORD` must match what you use to login |
| Slow first build | Normal — images are being downloaded. Wait and retry `docker compose ps` |
| `docker-compose` not found | Use `docker compose` (space, not dash) — Compose v2 is built into Docker |

---

*Project: https://github.com/fastapi/full-stack-fastapi-template*
*Stack: FastAPI · React · TypeScript · PostgreSQL · SQLModel · Docker Compose*
