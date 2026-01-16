# DevInterview

A community-driven hub for publishing and discovering real interview journeys. DevInterview couples a TypeScript/Express API with a modern React client so candidates can share rich, structured stories while reviewers moderate and surface the most helpful insights.

## Table of Contents
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Environment Variables](#environment-variables)
- [Getting Started](#getting-started)
  - [Backend](#backend)
  - [Frontend](#frontend)
- [Available Scripts](#available-scripts)
- [API Surface](#api-surface)
- [Development Tips](#development-tips)

## Features
- Core sharing workflow for creating, updating, and deleting detailed interview experiences with round-by-round drill downs.
- AI-powered "fuzzy" ingestion route that converts unstructured narratives into database-ready JSON using OpenAI while enforcing strict validation.
- JWT-backed authentication with protected routes, password hashing, and role-aware middleware.
- Community engagement with experience upvotes, threaded comments, and personal dashboards showing contributions.
- Admin console endpoints for reviewing pending submissions, changing approval status, and moderating community content.

## Tech Stack
| Layer | Technologies |
| --- | --- |
| Backend | Node.js, Express 5, TypeScript, Prisma ORM, PostgreSQL |
| Security | JSON Web Tokens, bcryptjs |
| AI Assistance | OpenAI API (gpt-4o-mini) for experience parsing |
| Frontend | React 18, Vite, TypeScript, Tailwind CSS, shadcn/ui, React Query, Zustand |
| Tooling | tsx, ESLint, Bun or npm, Vercel-ready build configuration |

## Project Structure
```
backend-dev-interview/
  prisma/            # Prisma schema and migration history
  src/
    config/          # Database client bootstrap
    controllers/     # Route handlers and business logic
    middleware/      # Auth and role guards
    routes/          # Express routers grouped by domain
    utils/           # Shared helpers (JWT helpers, etc.)
    generated/       # Prisma generated client (do not edit)

exp-decoder/
  src/
    components/      # UI primitives and feature components
    pages/           # React Router route views
    lib/             # API helpers, auth utilities, global stores
    hooks/           # Reusable hooks (e.g., toast, mobile layout)
```

## Prerequisites
- Node.js 18+ (or Bun 1.0+ if preferred for the frontend)
- PostgreSQL 14+ with a database provisioned for DevInterview data
- An OpenAI API key with access to gpt-4o-mini (or compatible) for fuzzy ingestion workflows

## Environment Variables
Create `.env` files in each project root with the values below.

**backend-dev-interview/.env**
```
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=public"
JWT_SECRET="super-secret-jwt-key"
OPENAI_API_KEY="sk-..."
PORT=4000                 # optional override
```

**exp-decoder/.env**
```
VITE_BACKEND_URL="http://localhost:4000"
```

## Getting Started
Clone the repository, install dependencies, run migrations, and launch both services.

### Backend
1. Install dependencies
   - `npm install`
2. Generate the Prisma client
   - `npx prisma generate`
3. Apply database schema (choose one)
   - `npx prisma migrate deploy` (production-like) or
   - `npx prisma migrate dev --name init` (local dev with drift detection)
4. Start the API server
   - `npm run dev`
5. The API will default to `http://localhost:4000`.

### Frontend
1. Install dependencies (choose a package manager)
   - `bun install` *(recommended to stay in sync with bun.lockb)*
   - or `npm install`
2. Start the Vite dev server
   - `bun run dev` or `npm run dev`
3. Access the client at `http://localhost:5173` (default Vite port). Ensure `VITE_BACKEND_URL` points to your API.

## Available Scripts
| Location | Script | Description |
| --- | --- | --- |
| backend-dev-interview | `npm run dev` | Starts Express with tsx for hot TypeScript reload |
|  | `npx prisma studio` | Visualize and edit Prisma models in your browser |
|  | `npx prisma migrate dev` | Create and apply a new migration in development |
| exp-decoder | `bun run dev` / `npm run dev` | Launches the React/Vite development server |
|  | `bun run build` / `npm run build` | Produces a production build |
|  | `bun run lint` / `npm run lint` | Runs eslint across the frontend source |

## API Surface
Base URL: `http://localhost:4000`
- `POST /auth/register` – Register a new user
- `POST /auth/login` – Authenticate and receive a JWT
- `GET /api/experiences` – Public feed of approved experiences
- `GET /api/experiences/:id` – Fetch a single experience (owners can view drafts)
- `POST /api/experiences` – Create a structured experience (auth required)
- `POST /api/experiences/fuzzy` – Submit free-form text for AI parsing (auth required)
- `GET /api/experiences/mine` – View experiences created by the logged-in user
- `POST /api/comments/:id` – Add a comment to an experience
- `POST /api/upvotes/:id` – Toggle an upvote on an experience
- `GET /api/experiences/admin/pending` – Admin queue of pending submissions
- `PUT /api/experiences/admin/:id/status` – Approve or reject experiences
