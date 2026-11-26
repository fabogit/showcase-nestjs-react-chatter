# showcase-nestjs-react-chatter

> A modern full-stack real-time chat application foundation leveraging NestJS 11 with code-first Apollo GraphQL, MongoDB with Mongoose repository abstractions, automated schema migrations, and a Vite-powered React 19 client styled with Material-UI and Apollo Client.

**Inception (First Commit):** `2025-05-19` • **Batch / Horizon:** `Batch 2024–2025` • **Architecture Domain:** `Full-Stack Real-Time Messaging Architecture`

---

## 📌 Architecture & Overview

`Nestjs-React-Chatter` is structured as a decoupled full-stack workspace comprising a high-performance backend microservice (`chatter-backend`) and a single-page application client (`chatter-ui`). The project represents the architectural milestone and core foundation for a real-time messaging system, focusing on user domain modeling, secure credential lifecycle handling, database migration automation, and end-to-end GraphQL communication.

* **Context:** Architectural foundation and production-grade baseline for a full-stack real-time chat platform. Built to demonstrate clean enterprise TypeScript patterns, code-first GraphQL schemas, decoupled client-server state management, and declarative data persistence.
* **Key Patterns:**
  * **Code-First GraphQL Architecture:** Resolvers, ObjectTypes, and InputTypes automatically generate the GraphQL schema contract at runtime via `@nestjs/graphql` and `@nestjs/apollo`.
  * **Generic Abstract Repository Pattern:** Encapsulates Mongoose database interactions behind a type-safe `AbstractRepository<T>` base class, enforcing consistent error handling, entity casting, and query logging.
  * **Automated In-Process Database Migrations:** Programmatic database migration runner executed during NestJS application initialization (`OnModuleInit`) using `migrate-mongo` to enforce MongoDB collection schemas and unique indices.
  * **Unified Domain Models:** Single class declaration serves both as a Mongoose schema (`@Schema()`, `@Prop()`) and a GraphQL Object Type (`@ObjectType()`, `@Field()`), preventing schema duplication while explicitly shielding sensitive fields like hashed passwords from the GraphQL layer.
  * **Declarative React UI with Apollo Client:** React 19 single-page application integrating Material-UI v7 with a centralized dark theme, declarative routing via `react-router-dom` v7, and custom GraphQL mutation hooks.

### Workspace Structure

```
Nestjs-React-Chatter/
├── docker-compose.yaml               # Local MongoDB 8 infrastructure definition
├── chatter-backend/                  # NestJS 11 API service
│   ├── src/
│   │   ├── common/database/         # Generic database abstraction and migration lifecycle
│   │   │   ├── abstract.entity.ts   # Base MongoDB document entity with ObjectId
│   │   │   ├── abstract.repository.ts # Generic CRUD repository implementation
│   │   │   ├── database.module.ts   # Dynamic Mongoose registration module
│   │   │   └── db-migration.service.ts # migrate-mongo runner on module init
│   │   ├── migrations/              # Database migration scripts
│   │   │   └── user-email-index.ts  # Unique index creation on users.email
│   │   ├── users/                   # User domain module
│   │   │   ├── dto/                 # Input types with class-validator decorators
│   │   │   ├── entities/            # Hybrid Mongoose schema and GraphQL ObjectType
│   │   │   ├── users.module.ts      # Users feature module definition
│   │   │   ├── users.repository.ts  # Concrete user repository
│   │   │   ├── users.resolver.ts    # GraphQL query and mutation resolvers
│   │   │   └── users.service.ts     # Business logic and bcrypt password hashing
│   │   ├── app.module.ts            # Root module (Config, GraphQL, Pino, Database, Users)
│   │   └── main.ts                  # NestJS bootstrap, CORS, ValidationPipe, and Pino logger
│   ├── test/                        # End-to-end testing fixtures
│   ├── .env.template                # Backend environment variable template
│   └── package.json                 # Backend dependencies and scripts
└── chatter-ui/                       # React 19 + Vite 6 frontend client
    ├── src/
    │   ├── components/
    │   │   ├── auth/                # Authentication views (Login, Signup, Form component)
    │   │   └── Routes.tsx           # React Router DOM browser router configuration
    │   ├── constants/
    │   │   ├── apollo-client.ts     # Apollo Client cache and HTTP link configuration
    │   │   └── urls.ts              # API endpoint configuration
    │   ├── hooks/                   # Custom GraphQL hooks (useCreateUser)
    │   ├── models/                  # Frontend TypeScript data transfer interfaces
    │   ├── App.tsx                  # Root component: MUI ThemeProvider, ApolloProvider
    │   └── main.tsx                 # Client entry point
    ├── .env.template                # Client environment variable template
    └── package.json                 # Frontend dependencies and build scripts
```

---

## 🛠 Tech Stack

### Backend (`chatter-backend`)
* **Runtime & Framework:** Node.js `>= 18`, TypeScript 5.7, NestJS 11 (`@nestjs/core`, `@nestjs/common`, `@nestjs/platform-express`)
* **GraphQL & API:** Apollo Server 4 (`@apollo/server`), `@nestjs/graphql` 13, `@nestjs/apollo` 13, `graphql` 16
* **Database & Persistence:** MongoDB 8, Mongoose 8 (`@nestjs/mongoose`), `migrate-mongo` 12
* **Security & Validation:** `bcrypt` 6 (10 salt rounds), `class-validator` 0.14, `class-transformer` 0.5, `joi` 17
* **Logging & Telemetry:** `nestjs-pino` 4, `pino-http` 10, `pino-pretty` 13
* **Testing & Build:** Jest 29, `ts-jest`, Supertest 7, `@nestjs/cli` 11, SWC (`@swc/core`, `@swc/cli`)

### Frontend (`chatter-ui`)
* **Runtime & Bundler:** React 19, `react-dom` 19, TypeScript 5.8, Vite 6 (`@vitejs/plugin-react`)
* **Component Library & Styling:** Material-UI v7 (`@mui/material`, `@mui/icons-material`), Emotion (`@emotion/react`, `@emotion/styled`), `@fontsource/roboto`
* **Data Fetching & State:** Apollo Client 3.13 (`@apollo/client`), `graphql` 16
* **Routing & Utilities:** `react-router-dom` 7, `localforage` 1.10, `match-sorter` 8, `sort-by` 1.2

---

## ✨ Features & Endpoints

* **Code-First GraphQL API:** Fully typed queries and mutations with auto-generated schema (`autoSchemaFile: true`).
* **Abstract Generic Repository:** Decoupled data access layer extending `AbstractRepository<T>` for standardized CRUD, entity serialization, and not-found exception handling.
* **Declarative Input Validation:** Global NestJS validation pipe enforcing RFC 5322 email formatting (`@IsEmail()`) and strong password constraints (`@IsStrongPassword()`).
* **Automatic Database Migrations:** Self-executing migration lifecycle ensuring required collection indices (e.g. unique constraint on `email`) exist before handling traffic.
* **Structured High-Performance Logging:** Integrated Pino logger with pretty-printed formatted output during local development and JSON format in production.
* **Modern React 19 UI:** Single-page authentication views (`/signup` and `/login`) with Material-UI Dark Theme and Apollo Client mutation bindings.

### GraphQL Operations & API Endpoints

#### GraphQL Operations (Path: `/graphql`)

| Operation Type | Name / Signature | Description |
|---|---|---|
| `Mutation` | `createUser(createUserInput: CreateUserInput!): User` | Hashes raw password with bcrypt and persists a new user document. |
| `Mutation` | `updateUser(updateUserInput: UpdateUserInput!): User` | Updates user details; re-hashes password if an updated password is provided. |
| `Mutation` | `removeUser(_id: String!): User` | Deletes a user document by its MongoDB ObjectId. |
| `Query` | `users: [User!]!` | Retrieves all registered user entities. |
| `Query` | `user(_id: String!): User!` | Retrieves a single user entity matching the given ObjectId. |

#### REST Endpoints

| Method | Route | Description |
|---|---|---|
| `GET` | `/` | Default health verification endpoint returning `"Hello World!"`. |

---

## 🚀 Getting Started

### Prerequisites

* **Node.js:** `>= 18.0.0` (LTS recommended)
* **Package Manager:** `pnpm` (recommended) or `npm`
* **Container Engine:** Docker & Docker Compose (for MongoDB daemon)

---

### Installation

Clone the repository and inspect the decoupled packages:

```bash
# Clone the repository
git clone https://github.com/fabogit/showcase-nestjs-react-chatter.git
cd showcase-nestjs-react-chatter
```

---

### Environment Configuration

#### 1. Backend Configuration (`chatter-backend`)

Create a `.env` file in `chatter-backend/` based on `.env.template`:

```bash
cd chatter-backend
cp .env.template .env
```

Ensure the configuration matches your local environment:

```env
MONGODB_URI=mongodb://localhost:27017/chatter
DB_NAME=chatter
PORT=3001
```

#### 2. Frontend Configuration (`chatter-ui`)

Create a `.env` file in `chatter-ui/` based on `.env.template`:

```bash
cd ../chatter-ui
cp .env.template .env
```

Verify that `VITE_APP_API_URL` points to your backend instance:

```env
VITE_APP_API_URL=http://localhost:3001
```

---

### Running the Services

#### 1. Start External Infrastructure (MongoDB)

Launch MongoDB 8 container via Docker Compose from the root directory:

```bash
# From repository root
docker compose up -d
```

#### 2. Run the Backend API (`chatter-backend`)

```bash
cd chatter-backend

# Install dependencies
pnpm install

# Start development server with auto-reload
pnpm run start:dev
```

The GraphQL playground / Apollo sandbox is accessible at `http://localhost:3001/graphql`.

#### 3. Run the Frontend Client (`chatter-ui`)

In a separate terminal:

```bash
cd chatter-ui

# Install dependencies
pnpm install

# Start Vite development server
pnpm run dev
```

The frontend application will be served at `http://localhost:5173`. Navigate to `http://localhost:5173/signup` to register a new user.

---

### Running Tests

#### Backend Test Suite (`chatter-backend`)

```bash
cd chatter-backend

# Unit tests
pnpm test

# End-to-end tests
pnpm run test:e2e

# Test coverage
pnpm run test:cov
```

#### Frontend Linting & Build Verification (`chatter-ui`)

```bash
cd chatter-ui

# Lint check
pnpm run lint

# Production bundle compilation
pnpm run build
```

---

## 📄 License & Author

Developed by [@fabogit](https://github.com/fabogit).
