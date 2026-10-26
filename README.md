# School Management System

A full-stack school management application for administrators, teachers, and students. The repository contains a Next.js frontend and a TypeScript/Express backend backed by MongoDB.

## What Is Implemented

### Admin

- Admin authentication and protected dashboard
- School, class, subject, teacher, and student management
- Teacher and student timetable management
- Homework, exams, marks, attendance, and leave management
- Admin profile and dashboard data
- AI chatbot integration

### Teacher

- Teacher authentication and profile management
- Assigned classes and subjects
- Student lookup and profile access
- Homework and notices
- Exams, marks, and student results
- Timetables, attendance, and leave workflows
- AI chatbot integration

### Student

- Student authentication and profile management
- Dashboard, assignments, exams, and results
- Timetable, notices, attendance, and leave workflows
- AI chatbot integration

## Architecture

The current backend is a single Express application, not a collection of independently running microservices. Role-specific modules are organized inside the server source tree and are registered on one HTTP server.

```text
Browser
  |
  v
Client (Next.js, React, Redux, React Query)
  |
  | HTTP requests with Axios and cookie credentials
  v
Server (Express, TypeScript)
  |
  +-- /api/v1/admin/*
  +-- /api/v1/auth/teacher and /api/v1/teacher/*
  +-- /api/v1/auth/student and /api/v1/student/*
  |
  v
MongoDB (Mongoose)
```

## Repository Layout

```text
Client/
  app/             Next.js App Router pages grouped by role
  api/             Shared Axios client configuration
  components/      Admin, teacher, student, and shared UI components
  hooks/           Role-specific data hooks
  interface/       TypeScript interfaces
  lib/             API and domain helpers
  redux/           Redux store and slices
  public/          Static assets
  package.json

Server/
  src/app.ts       Express middleware and route registration
  src/index.ts     Database connection and HTTP server startup
  src/config/      Database and authentication configuration
  src/middleware/  Shared middleware
  src/modules/     Domain modules grouped by feature and role
  src/route/       Admin, teacher, and student route registration
  package.json
```

## Technology Stack

- Frontend: Next.js 16, React 19, TypeScript, Tailwind CSS 4
- Client state and data: Redux Toolkit, React Redux, TanStack React Query
- UI and interaction: Framer Motion, Lucide React, React Hook Form, React Toastify
- Backend: Node.js, Express 5, TypeScript
- Database: MongoDB with Mongoose
- Authentication: JWT cookies, bcrypt, and optional Google OAuth
- AI: Google GenAI/Gemini integration
- Security and operations: Helmet, CORS, rate limiting, Morgan, cookie-parser

## Prerequisites

- Node.js 20 or newer
- npm
- MongoDB, local or hosted
- Google OAuth credentials if Google sign-in is enabled
- A Gemini/Google GenAI API key if chatbot features are enabled

## Configuration

Create `Server/.env`:

```env
PORT=5000
NODE_ENV=development
URL=mongodb://127.0.0.1:27017/school-management
JWT_SECRET=replace-with-a-long-random-secret
GOOGLE_CLIENT_ID=your-google-client-id
LLM_API=your-gemini-api-key
ALLOWED_ORIGINS=http://localhost:3000
TRUST_PROXY=false
```

Create `Client/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:5000
NEXT_PUBLIC_GOOGLE_CLIENT_ID=your-google-client-id
```

Do not commit either environment file or real credentials. The API requires `URL` and `JWT_SECRET` for normal operation. Google and AI variables are required only for the related features.

## Local Development

Install dependencies and start the backend in one terminal:

```powershell
cd Server
npm install
npm run dev
```

Start the frontend in a second terminal:

```powershell
cd Client
npm install
npm run dev
```

Open the frontend at `http://localhost:3000`. The API health response is available at `http://localhost:5000/` when the default port is used.

Useful commands:

```powershell
# Client
cd Client
npm run lint
npm run build
npm start

# Server
cd Server
npm run build
npm start
```

The server development command uses `ts-node-dev` and automatically restarts after source changes. The production server runs the compiled output from `dist`.

## API Route Groups

All feature routes use the `/api/v1` prefix.

| Area             | Base paths                                                                                                                                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Admin            | `/api/v1/admin/auth`, `/profile`, `/school`, `/class`, `/subject`, `/student`, `/homework`, `/exam`, `/marks`, `/dashboard`, `/timetable/student`, `/timetable/teacher`, `/leave`, `/attendance`, `/chatbot` |
| Teacher auth     | `/api/v1/auth/teacher`                                                                                                                                                                                       |
| Teacher features | `/api/v1/teacher/profile`, `/helper`, `/homework`, `/class`, `/subject`, `/assign-class-subject`, `/exams`, `/marks`, `/notice`, `/timetable`, `/result`, `/leave`, `/attendance`, `/chatbot`                |
| Student auth     | `/api/v1/auth/student`                                                                                                                                                                                       |
| Student features | `/api/v1/student/assignment`, `/result`, `/exams`, `/timetable`, `/notice`, `/profile`, `/leave`, `/attendance`, `/chatbot`                                                                                  |

The exact HTTP methods, request bodies, and authorization rules are defined in the route and controller files under `Server/src/modules`. The root endpoint returns a JSON health response, and unknown endpoints return a JSON 404 response.

## Docker

Both applications include multi-stage Dockerfiles:

```powershell
docker build -t school-management-server ./Server
docker build -t school-management-client ./Client
```

The client image exposes port `3000`. The server Dockerfile currently declares port `8001`, while the application defaults to port `5000`; set `PORT=8001` in the container environment, or publish the application's actual port explicitly when running it.

The server container also needs access to MongoDB and the same secret/configuration variables described above. The client needs its public API URL available at build time.

## Security Notes

- Use a strong, unique `JWT_SECRET` outside local development.
- Restrict `ALLOWED_ORIGINS` in production instead of relying on localhost development behavior.
- Keep `.env` files and API keys out of source control.
- Use HTTPS in production so authentication cookies and API traffic are protected.
- Review and configure cookie options, proxy settings, and MongoDB network access before deployment.

## Current Limitations and Maintenance Notes

- There is no root-level workspace script; client and server are started separately.
- The repository does not currently include a dedicated automated test script in either package.
- `readmeforserver.md` and `flow.md` contain earlier planning notes, including a microservice/port design that does not reflect the current single-server implementation.
- The server Dockerfile's declared port should be kept aligned with `PORT` during deployment.

## Contributing

1. Create a feature branch.
2. Make the smallest focused change that matches the existing module and role structure.
3. Run the relevant client lint/build or server build command.
4. Open a pull request with setup notes and verification details.
