# Notice Board Backend

**A minimal Express + Mongoose API** for posting notices and creating tasks, with role-based access control for `admin` and `coadmin`.

A short, clean README with just what you need to run and test the project.

---

## Project structure

```
notice-board-backend/
├── controllers/
│   ├── auth.controller.js       # signup / login
│   ├── notice.controller.js     # notices CRUD
│   └── task.controller.js       # tasks CRUD
├── models/
│   ├── user.model.js           # user schema
│   ├── notice.model.js         # notice schema
│   └── task.model.js           # task schema
├── routes/
│   ├── auth.routes.js
│   ├── notice.routes.js
│   ├── task.routes.js
│   └── admin.routes.js         # admin endpoints
├── middleware/
│   ├── auth.middleware.js      # JWT auth
│   └── role.middleware.js      # role checks
├── postman/                    # Postman collection & environment
│   ├── NoticeBoard.postman_collection.json
│   └── NoticeBoard.postman_environment.json
├── server.js                   # app entrypoint
├── package.json
├── .env.example
├── .gitignore
└── README.md
```

---

## Quick Start

**Prerequisites:** Node.js v16+ and MongoDB

1. Copy `.env.example` to `.env` and set your values.
2. Install dependencies:

```bash
npm install
```

3. Run in development :

```bash
npm run dev
```

4. Run in production:

```bash
npm start
```

## Environment variables

| Variable      | Description                          |
|---------------|--------------------------------------|
| `PORT`        | Server port (default: `3000`)        |
| `MONGODB_URI` | MongoDB connection string            |
| `JWT_SECRET`  | Secret key for signing JWT tokens    |

> Tip: Generate a secure `JWT_SECRET` (e.g. `openssl rand -base64 48`) and never commit it to source control.

---

## API Endpoints



## API Endpoints

### Auth
- POST `/api/auth/register` — register a new user
  - Body (JSON):
    {
      "name": "Alice",
      "email": "alice@example.com",
      "password": "secret"
    }
  - Response (201):
    {
      "user": { "id": "...", "name": "Alice", "email": "alice@example.com", "role": "user" },
      "token": "<jwt>"
    }

- POST `/api/auth/login` — login
  - Body (JSON): { "email": "alice@example.com", "password": "secret" }
  - Response (200): { "user": { "id","name","email","role" }, "token": "<jwt>" }

### Notices
- GET `/api/notices` — list notices (public)
  - Response (200): array of notice objects

- POST `/api/notices` — create notice (requires `admin` or `coadmin`)
  - Headers: `Authorization: Bearer <token>`
  - Body (JSON): { "title": "Maintenance", "content": "Site down at midnight" }
  - Response (201): created notice object

- PUT `/api/notices/:id` — update notice (requires `admin` or `coadmin`)
  - Headers: `Authorization: Bearer <token>`
  - Body: partial fields to update
  - Response (200): updated notice object

- DELETE `/api/notices/:id` — delete notice (requires `admin` or `coadmin`)
  - Headers: `Authorization: Bearer <token>`
  - Response (200): { "message": "Deleted" }

### Tasks
- GET `/api/tasks` — list tasks
  - Response (200): array of tasks

- POST `/api/tasks` — create task (authenticated)
  - Headers: `Authorization: Bearer <token>`
  - Body (JSON): { "title": "New task", "description": "Details", "assignedTo": "<userId>" }
  - Response (201): created task object

### Admin
- GET `/api/admin/users` — list users (admin only)
  - Headers: `Authorization: Bearer <token>`
  - Response (200): array of users

- PUT `/api/admin/users/:id/role` — update a user's role (admin only)
  - Headers: `Authorization: Bearer <token>`
  - Body (JSON): { "role": "coadmin" }
  - Response (200): updated user object

- GET `/api/admin/notices` — list notices (admin/coadmin)
  - Headers: `Authorization: Bearer <token>`
  - Response (200): array of notices

## Postman

Import `postman/NoticeBoard.postman_collection.json` and `postman/NoticeBoard.postman_environment.json` into Postman.

1. Select the `Notice Board` environment and set `baseUrl` (e.g. `http://localhost:3000`).
2. Register and then login to obtain a token.
3. Set the environment `token` variable (used as `Authorization: Bearer {{token}}`).

Suggested test order (manual or using Newman):
1. Auth → `POST /api/auth/register`
2. Auth → `POST /api/auth/login` → set token
3. Tasks → `POST /api/tasks` → create task
4. Tasks → `GET /api/tasks`
5. Notices → `POST /api/notices` (requires admin/coadmin)
6. Notices → `GET /api/notices`

---

## Troubleshooting

- DB connection errors: verify `MONGODB_URI` and that MongoDB is running.
- Auth errors: ensure `JWT_SECRET` is set and tokens are passed in `Authorization` header.
- Port issues: change `PORT` in `.env`.

---




