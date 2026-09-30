# INFYLE Construction Platform — Backend

Backend API for the **INFYLE Construction Platform**, a Modular Monolith built with **NestJS 12**, **TypeScript**, **PostgreSQL** (via TypeORM), **Redis** (BullMQ), **AWS S3**, and **Firebase Cloud Messaging**.

It serves as the core backend infrastructure powering:
- **Job Seeker / Worker Mobile App** (Flutter — see `jobseekermobileapp/`)
- **Contractor ERP App**
- **Admin Web Panel**

> **API Prefix:** All endpoints are served under `api/v1`.

---

## 📂 Project Structure

```
construtor/
├── .github/workflows/       # GitHub Actions CI pipeline
│   └── ci.yml               # Lint → Build → Test → E2E → Security Audit
├── bruno/                   # Bruno API collection (request testing & docs)
│   ├── Auth/
│   ├── Users/
│   ├── Companies/
│   ├── Contractors/
│   ├── Projects/
│   ├── Project-Sites/
│   ├── Site-Engineers/
│   ├── Jobs/
│   ├── Applications/
│   ├── Attendance/
│   ├── Calculators/
│   ├── Materials/
│   ├── Expenses/
│   ├── Reports/
│   ├── Notifications/
│   └── environments/        # Bruno env configs (dev, prod, etc.)
├── config/
│   └── firebase-service-account.json
├── jobseekermobileapp/      # Flutter mobile app (separate codebase)
├── src/
│   ├── main.ts              # App entrypoint — bootstraps NestJS
│   ├── app.module.ts        # Root module — registers all feature modules
│   ├── app.controller.ts    # Health check / root route
│   ├── app.service.ts
│   ├── common/              # Shared utilities & infrastructure
│   │   ├── decorators/      # @Roles(), @CurrentUser()
│   │   ├── filters/         # AllExceptionsFilter (global error handling)
│   │   ├── guards/          # RolesGuard (RBAC enforcement)
│   │   ├── interceptors/    # TransformInterceptor (response envelope)
│   │   ├── pipes/           # Custom validation pipes
│   │   ├── types/           # Shared TypeScript types & interfaces
│   │   └── utils/           # Helper functions
│   ├── config/              # App configuration (reserved)
│   ├── database/
│   │   ├── database.module.ts  # TypeORM connection setup (PostgreSQL)
│   │   ├── seed.ts             # Database seeder script
│   │   └── entities/           # Shared / cross-module entities
│   ├── jobs/                # BullMQ job definitions (reserved)
│   └── modules/             # ← All business logic lives here
│       ├── applications/    # Job applications, shortlisting, moderation
│       ├── attendance/      # Daily check-ins, history, cost generation
│       ├── audit-logs/      # Security & business activity trailing
│       ├── auth/            # JWT issuance, sessions, OTP verification
│       ├── authorization/   # Permission rules & role-capability mapping
│       ├── calculators/     # Civil engineering construction calculators
│       ├── companies/       # Company registration, profiles, verification
│       ├── contractors/     # Contractor profiles, docs, verification
│       ├── documents/       # Presigned S3 uploads & secure file refs
│       ├── expenses/        # Ad-hoc project / site-level expenses
│       ├── jobs/            # Job creation, publishing, admin moderation
│       ├── materials/       # Material master, purchases, stock tracking
│       ├── notifications/   # FCM push notifications & device tokens
│       ├── project-sites/   # Site creation, engineer assignment logic
│       ├── projects/        # Project tracking, contractor ownership
│       ├── reports/         # Daily reports — labor, material, profitability
│       ├── site-engineers/  # Engineer profiles, assignment-based access
│       ├── sites/           # Site entity definitions & CRUD
│       ├── users/           # User management & profile updates (all roles)
│       └── worker-app/      # Worker-facing mobile API endpoints
├── test/
│   └── app.e2e-spec.ts     # End-to-end test suite
├── .env.example             # Environment variable template
├── nest-cli.json
├── oxlint.json              # Oxlint configuration
├── package.json
├── tsconfig.json
├── vitest.config.ts         # Unit test config
└── vitest.config.e2e.ts     # E2E test config
```

### Module Internal Layout

Each module under `src/modules/<name>/` follows a consistent structure:

```
<module>/
├── <module>.controller.ts   # HTTP route definitions
├── <module>.service.ts      # Core business logic & DB orchestration
├── <module>.module.ts       # NestJS module registration
├── dto/                     # Request validation (class-validator DTOs)
├── entities/                # TypeORM entities (database tables)
├── tests/                   # Module-specific unit / integration tests
├── validators/              # Custom validation logic
└── *.MODULE.md              # Module documentation (where present)
```

---

## 🚀 Quick Start

### Prerequisites

| Dependency | Version |
|------------|---------|
| Node.js    | ≥ 22    |
| Yarn       | ≥ 1.22  |
| PostgreSQL | ≥ 15    |
| Redis      | ≥ 7     |

### 1. Install Dependencies

```bash
yarn install
```

### 2. Environment Variables

Copy the template and fill in your values:

```bash
cp .env.example .env
```

The `.env.example` contains:

```env
# Application
NODE_ENV=development
PORT=3000
JWT_SECRET=your_jwt_secret_key_here

# Database (PostgreSQL)
DATABASE_URL=postgresql://user:password@localhost:5432/construction_db

# Redis
REDIS_URL=redis://localhost:6379

# AWS S3 (File Storage)
AWS_REGION=us-east-1
AWS_S3_BUCKET=your-bucket-name
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key

# Firebase Cloud Messaging (FCM)
FIREBASE_PROJECT_ID=your-project-id
FIREBASE_CLIENT_EMAIL=your-service-account-email
FIREBASE_PRIVATE_KEY="your-private-key"
```

### 3. Database Setup & Seeding

The project uses TypeORM `synchronize: true` in development for rapid prototyping.

```bash
# Start the dev server (auto-syncs DB schema)
yarn start:dev

# In a separate terminal — seed dummy users & roles
yarn seed
```

---

## 🛠️ Available Scripts

### Development

| Command              | Description                                      |
|----------------------|--------------------------------------------------|
| `yarn start:dev`     | Start in watch mode (daily development)          |
| `yarn start:debug`   | Start in watch mode with debugger attached       |
| `yarn start:prod`    | Run compiled app from `dist/` (production only)  |
| `yarn build`         | Compile TypeScript → JavaScript into `dist/`     |

### Database

| Command      | Description                                              |
|--------------|----------------------------------------------------------|
| `yarn seed`  | Run `src/database/seed.ts` to populate initial test data |

### Code Quality

| Command        | Description                                                     |
|----------------|-----------------------------------------------------------------|
| `yarn lint`    | Run **Oxlint** on `src/` and `test/` — catch issues instantly   |
| `yarn format`  | Run **Prettier** to auto-format all `.ts` files                 |

### Testing

| Command           | Description                                   |
|-------------------|-----------------------------------------------|
| `yarn test`       | Run **Vitest** test suite once                 |
| `yarn test:watch` | Run Vitest in watch mode                      |
| `yarn test:cov`   | Run tests and generate coverage report        |
| `yarn test:e2e`   | Run end-to-end tests (`vitest.config.e2e.ts`) |

---

## 🏗️ Architecture & Modules

The platform is divided into **20 modules** inside `src/modules/`:

| Module | Description |
|--------|-------------|
| **auth** | JWT token issuance, session management, OTP verification |
| **authorization** | Permission rules, role-capability mapping, RBAC logic |
| **users** | Central user management and profile updates for all 5 roles |
| **companies** | Company registration, profiles, and verification workflows |
| **contractors** | Contractor profiles, verification, and secure document handling |
| **projects** | Core project tracking with strict contractor ownership isolation |
| **project-sites** | Site creation and engineer assignment logic |
| **sites** | Site entity definitions and CRUD operations |
| **site-engineers** | Engineer profile management and assignment-based access rules |
| **jobs** | Job creation, publishing, and admin moderation |
| **applications** | Job applications, shortlisting, and moderation features |
| **attendance** | Daily worker/labor check-ins, history, and cost generation |
| **materials** | Material master, purchase requests, issues, consumption, stock |
| **expenses** | Ad-hoc project and site-level expenses with file attachments |
| **reports** | Daily reports — labor, material, expense costs vs. profitability |
| **calculators** | Civil engineering construction calculation tools |
| **notifications** | Device token management and FCM push notifications |
| **documents** | Presigned AWS S3 uploads and secure file references |
| **audit-logs** | Security and business activity trailing |
| **worker-app** | Worker-facing API endpoints for the mobile app |

---

## 🔐 Security & RBAC

All endpoints are globally secured via:
- **`AllExceptionsFilter`** — Catches and normalizes all errors into a consistent response shape.
- **`TransformInterceptor`** — Wraps successful responses into a standard envelope.
- **`ValidationPipe`** — Enforces DTO validation using `class-validator` (whitelist + transform).

To protect an endpoint with role-based access control:

```typescript
@UseGuards(AuthGuard('jwt'), RolesGuard)
@Roles('admin', 'contractor')
@Post()
async createProject(@CurrentUser() user) { ... }
```

Custom decorators available in `src/common/decorators/`:
- **`@Roles(...roles)`** — Declare which roles can access an endpoint.
- **`@CurrentUser()`** — Inject the authenticated user into a handler.

---

## 🧪 API Testing with Bruno

The `bruno/` directory contains a complete API collection organized by module. Use [Bruno](https://www.usebruno.com/) to run requests against the backend.

```
bruno/
├── Auth/               # Login, register, OTP, refresh
├── Users/              # User CRUD, profile updates
├── Companies/          # Company workflows
├── Contractors/        # Contractor management
├── Projects/           # Project CRUD
├── Project-Sites/      # Site management
├── Site-Engineers/     # Engineer assignments
├── Jobs/               # Job publishing
├── Applications/       # Applications & shortlisting
├── Attendance/         # Check-in management
├── Calculators/        # Engineering calculators
├── Materials/          # Material tracking
├── Expenses/           # Expense tracking
├── Reports/            # Profitability reports
├── Notifications/      # Push notification testing
├── environments/       # Environment configurations
└── Health Check.bru    # Server health check
```

---

## 🔄 CI / CD Pipeline

GitHub Actions CI runs on every push/PR to `main` (`.github/workflows/ci.yml`):

```
Lint (Oxlint + Prettier) → Build → Unit Tests (with coverage) → E2E Tests → Security Audit
```

- **Node.js 22** on `ubuntu-latest`
- **PostgreSQL 15** service container for test DB
- Coverage reports uploaded as artifacts (14-day retention)
- Dependency audit via `yarn audit`

---

## 📱 Mobile App

The **Job Seeker / Worker Mobile App** lives inside `jobseekermobileapp/` as a **Flutter** project with its own Git history. See its own `README.md` for setup instructions.

---

## 📄 License

This project is **UNLICENSED** — proprietary software of INFYLE Technologies.
