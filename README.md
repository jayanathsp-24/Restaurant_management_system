# Smart Restaurant POS System

[![CI/CD Pipeline](https://github.com/jayanathsp-24/Restaurant_management_system/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/jayanathsp-24/Restaurant_management_system/actions)
[![Next.js](https://img.shields.io/badge/Next.js-16.0%20(React%2019)-black?logo=nextdotjs)](https://nextjs.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4.0-38bdf8?logo=tailwindcss)](https://tailwindcss.com/)
[![SQL Server](https://img.shields.io/badge/SQL_Server-2022-red?logo=microsoftsqlserver)](https://www.microsoft.com/sql-server)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?logo=typescript)](https://www.typescriptlang.org/)

An enterprise-grade, real-time **Smart Restaurant POS System** designed for high-throughput restaurant environments. This system optimizes front-of-house operations, kitchen workflows, deliveries, and administrative management.

This project is developed as a group software engineering project by undergraduate students of **Batch 24, Faculty of Information Technology, University of Moratuwa**.

---

## Table of Contents

- [Subsystem Modules Overview](#subsystem-modules-overview)
  - [1. Receptionist Subsystem](#1-receptionist-subsystem)
  - [2. Waiter Management Subsystem](#2-waiter-management-subsystem)
  - [3. Kitchen Management Subsystem (KDS)](#3-kitchen-management-subsystem-kds)
  - [4. Delivery Management Subsystem](#4-delivery-management-subsystem)
  - [5. Admin Panel Subsystem](#5-admin-panel-subsystem)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Directory Structure](#directory-structure)
- [Prerequisites](#prerequisites)
- [Local Setup & Installation](#local-setup--installation)
  - [1. Database Setup (SQL Server)](#1-database-setup-sql-server)
  - [2. Next.js App Setup](#2-nextjs-app-setup)
- [Git & GitHub Collaboration Workflow](#git--github-collaboration-workflow)
  - [Branching Strategy](#branching-strategy)
  - [Pull Request (PR) Guidelines](#pull-request-pr-guidelines)
  - [Commit Message Conventions](#commit-message-conventions)
- [CI/CD Pipeline (GitHub Actions)](#cicd-pipeline-github-actions)
- [Team Members & Contributors](#team-members--contributors)

---

## Subsystem Modules Overview

The Smart Restaurant POS System is decomposed into 5 dedicated, role-based subsystems to ensure modularity, separation of concerns, and ease of scalability.

```
                  ┌─────────────────────────────────────────┐
                  │          Smart Restaurant POS           │
                  └────────────────────┬────────────────────┘
                                       │
        ┌───────────────┬──────────────┼──────────────┬──────────────┐
        ▼               ▼              ▼              ▼              ▼
 ┌──────────────┐┌──────────────┐┌──────────────┐┌──────────────┐┌─────────────┐
 │ Receptionist ││ Waiter Mgmt  ││ Kitchen KDS  ││  Delivery    ││ Admin Panel │
 └──────────────┘└──────────────┘└──────────────┘└──────────────┘└─────────────┘
```

### 1. Receptionist Subsystem
Serves as the gateway for customers entering the restaurant, facilitating walk-ins, reservations, and immediate table assignments.
* **Customer Registration:** Collects, verifies, and stores customer information for CRM purposes.
* **Waitlist & Queue Management:** Manages real-time queue tokens, estimates wait times using predictive throughput algorithms, and notifies customers when their table is ready.
* **Table Assignment:** Offers a live interactive map of dining rooms with real-time occupancy tracking to assign tables efficiently.
* **Order Creation:** Generates initial ticket headers linked to physical tables or queue numbers.

### 2. Waiter Management Subsystem
Coordinates waitstaff workflows, ensuring fast food delivery and minimizing table turnover time.
* **Table Allocation:** Dynamic load-balanced assignment of tables/zones to waitstaff based on active tables and waiter availability.
* **Real-time Order Alerts:** Instant notifications (via WebSockets/SSE) delivered to waiters the moment a dish is marked "Cooked" by the kitchen.
* **Delivery & Status Tracking:** Allows waitstaff to log delivery statuses and tracks key performance indicators (KPIs) like average delivery-to-table time.
* **Customer Service Requests:** Integrates table call buttons directly into the waiter's interface.

### 3. Kitchen Management Subsystem (KDS)
An interactive Kitchen Display System (KDS) replacing paper tickets with digital screens for kitchen staff.
* **Queue & Priority Routing:** Automatically parses incoming orders and displays them on KDS screens sorted by ticket arrival time and prep complexity.
* **Chef Self-Assignment:** Allows chefs to claim tickets or specific items (e.g., mains, starters) to prevent duplicate preparation.
* **Real-time Progress Tracker:** Tracks tickets through three main states: *Pending*, *In Preparation*, and *Completed*.
* **Ready Trigger:** Instant dispatch signal triggered upon completion, immediately updating the database and notifying the assigned waiter.

### 4. Delivery Management Subsystem
Handles all off-premise, takeaway, and delivery orders.
* **Delivery Dispatching:** Automatically pairs delivery orders with available in-house drivers based on load and distance.
* **Driver Availability Registry:** Maintains a real-time status board (e.g., *Idle*, *On Route*, *Off Duty*) for all delivery personnel.
* **Takeaway Logistics:** Separate queue tracking for pickup/takeaway customers, including SMS notifications upon preparation completion.
* **Delivery Routing:** Integrating routing maps to optimize delivery trips.

### 5. Admin Panel Subsystem
The central control hub for executives, restaurant managers, and HR personnel.
* **Employee Management:** Portal for employee onboarding, credential provisioning, role-based access control (RBAC), and shift scheduling.
* **Payroll & HR:** Tracks shift attendance, calculates monthly base wages, adds performance bonuses, and generates salary payslips.
* **Operational Reporting:** Automated end-of-day sales reporting, revenue analysis, tax breakdowns, and payment method summaries.
* **Performance Dashboard:** Visual representations of kitchen prep speeds, waiter delivery times, driver turnarounds, and food waste metrics.

---

## System Architecture

The application adopts a **Monorepo Architecture** leveraging Next.js App Router. We enforce clean-architecture boundaries between backend API endpoints (Next.js API routes / Server Actions) and client-side UI components.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          NEXT.JS FRONTEND (WEB)                         │
│   (Receptionist UI, Waiter App, Kitchen KDS, Delivery UI, Admin Panel)  │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ (REST API / Server Actions)
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      NEXT.JS BACKEND (API LAYOUT)                       │
│        Authentication, Business Logic Controllers, WebSocket Hub       │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │ (Connection Pool / Prisma ORM)
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                           MICROSOFT SQL SERVER                          │
│        Transactional Database (Tables, Spatials, Stored Procs)          │
└─────────────────────────────────────────────────────────────────────────┘
```

* **Frontend:** Modern, dynamic React 19 components using Next.js 16 Client & Server components.
* **Backend:** Secure, serverless-ready Next.js Server Actions and Route Handlers.
* **State & Real-time Sync:** Local component state, Context API, and WebSockets/Server-Sent Events (SSE) for zero-latency communication between Kitchen and Waitstaff.
* **Database Access:** Typed relational queries executed via a robust ORM layer (e.g., Prisma or Drizzle) mapping onto SQL Server.

---

## Tech Stack

* **Framework:** Next.js 16 (React 19 & React Server Components)
* **Language:** TypeScript 5.x
* **Styling:** TailwindCSS 4.0
* **Database:** Microsoft SQL Server (2022 Express / Developer Edition)
* **ORM:** Prisma ORM / Drizzle ORM
* **Version Control:** Git & GitHub
* **CI/CD:** GitHub Actions

---

## Directory Structure

We adhere to the standard Next.js App Router layout with structured separation of modules, utilities, and components:

```
├── .github/
│   └── workflows/
│       └── ci-cd.yml             # GitHub Actions CI/CD Pipeline
├── src/
│   ├── app/                      # Next.js App Router Pages & API Routes
│   │   ├── (auth)/               # Auth group (Login, Forgot Password)
│   │   ├── admin/                # Admin Subsystem Pages
│   │   ├── receptionist/         # Receptionist Subsystem Pages
│   │   ├── waiter/               # Waiter Management Subsystem Pages
│   │   ├── kitchen/              # KDS Subsystem Pages
│   │   ├── delivery/             # Delivery Management Subsystem Pages
│   │   ├── api/                  # Shared REST API Endpoints
│   │   ├── layout.tsx            # Global Layout template
│   │   └── page.tsx              # Portal Root Page
│   │
│   ├── components/               # React Components
│   │   ├── ui/                   # Reusable Primitive Components (buttons, inputs)
│   │   └── shared/               # Shared Layout Components (headers, sidebars)
│   │
│   ├── hooks/                    # Reusable Custom React Hooks
│   ├── lib/                      # Shared library initializations (db, auth, prisma)
│   ├── services/                 # Business logic service abstractions
│   ├── styles/                   # Global stylesheets
│   │   └── globals.css           # TailwindCSS Imports
│   │
│   ├── types/                    # Global TypeScript Interface definitions
│   └── utils/                    # Helper functions (formatters, validators)
│
├── prisma/                       # Prisma Schema and Migrations
│   └── schema.prisma
├── public/                       # Static Assets (Images, Icons, Fonts)
├── .env.example                  # Environment Variables Template
├── next.config.js                # Next.js Configuration
├── package.json                  # Dependencies and Scripts
├── tsconfig.json                 # TypeScript Configuration
└── tailwind.config.css           # Tailwind CSS 4 Configuration
```

---

## Prerequisites

Before setting up the repository locally, ensure you have the following installed:

* **Node.js:** `v20.x.x` or higher (LTS recommended)
* **Package Manager:** `npm` (v10+) or `pnpm` (v9+)
* **Database Engine:** Microsoft SQL Server (LocalDB, Express, Developer Edition, or running via Docker)
* **Git:** Version 2.40+ installed and configured

---

## Local Setup & Installation

Follow these steps to set up your local development environment:

### 1. Database Setup (SQL Server)

Choose one of the following methods to run MS SQL Server:

#### Option A: Docker (Recommended - Cross-platform)
If you have Docker installed, run the following command to start a SQL Server instance:
```bash
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=YourStrongPassword123!" \
   -p 1433:1433 --name sql_server_pos \
   -d mcr.microsoft.com/mssql/server:2022-latest
```

#### Option B: Native Installation (Windows/Linux)
1. Download and install **SQL Server 2022 Developer or Express Edition**.
2. During installation, enable **SQL Server Authentication** and set a password for the `sa` user.
3. Open **SQL Server Configuration Manager** and ensure **TCP/IP** is enabled (under SQL Server Network Configuration -> Protocols). Restart the SQL Server service if changes are made.
4. Verify port `1433` is listening.

### 2. Next.js App Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/jayanathsp-24/Restaurant_management_system.git
   cd Restaurant_management_system
   ```

2. **Install Node.js dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables:**
   Copy the example environment file:
   ```bash
   cp .env.example .env.local
   ```
   Open `.env.local` and configure your SQL Server connection string:
   ```env
   # SQL Server Connection String Configuration
   # Format: sqlserver://<host>:<port>;database=<db_name>;user=<username>;password=<password>;encrypt=true;trustServerCertificate=true;
   DATABASE_URL="sqlserver://localhost:1433;database=RestaurantPOS;user=sa;password=YourStrongPassword123!;encrypt=true;trustServerCertificate=true;"
   
   # App configuration
   NEXTAUTH_SECRET="your_jwt_secret_token_here_at_least_32_characters"
   NEXT_PUBLIC_APP_URL="http://localhost:3000"
   ```

4. **Initialize the Database Schema:**
   Apply migrations to generate database tables and schemas:
   ```bash
   npx prisma migrate dev --name init
   ```
   *(Optional)* Run the seed script to populate initial roles, tables, and admin account:
   ```bash
   npx prisma db seed
   ```

5. **Start the Development Server:**
   ```bash
   npm run dev
   ```
   Open [http://localhost:3000](http://localhost:3000) in your browser to verify the installation.

---

## Git & GitHub Collaboration Workflow

To ensure smooth collaboration among the 5 team members and keep the main branch stable, the project enforces a strict branching and code-review workflow.

### Branching Strategy
We use a structured branch naming convention based on the feature modules:

* `main` / `master` - Production-ready, stable releases only. Directly pushed commits are blocked.
* `develop` - Integration branch for features. Always kept buildable.
* `feature/<subsystem>-<feature-name>` - Dedicated branches for building specific module features.
  * *Example:* `feature/kitchen-ticket-assignment`, `feature/admin-payroll-calculation`
* `bugfix/<subsystem>-<bug-desc>` - Dedicated branches for fixing bugs.
  * *Example:* `bugfix/waiter-alert-latency`

### Pull Request (PR) Guidelines
1. All changes must be merged into `develop` via a Pull Request (PR).
2. **Mandatory Reviews:** A PR requires at least **one peer review and approval** from another team member before it can be merged.
3. **CI Validation:** The automated GitHub Actions CI check **must pass** (linting, type checking, and tests) before merging.
4. **Clean Git History:** Rebase/Squash merges are preferred to keep the integration timeline clean and legible.

### Commit Message Conventions
We follow the **Conventional Commits** standard to make commit messages descriptive:
* `feat(<subsystem>): <message>` - A new feature (e.g., `feat(receptionist): add walk-in ticket generator`)
* `fix(<subsystem>): <message>` - A bug fix (e.g., `fix(kds): resolve dual-assign ticket race condition`)
* `docs(<subsystem>): <message>` - Documentation changes (e.g., `docs(readme): update setup instructions`)
* `refactor(<subsystem>): <message>` - Code reorganization without behavioral changes (e.g., `refactor(waiter): optimize websocket hook`)

---

## CI/CD Pipeline (GitHub Actions)

The repository uses automated workflows configured in `.github/workflows/ci-cd.yml` to maintain code health.

### Automated Checks Triggered on PRs/Pushes to `develop` & `main`:
1. **Linter Checklist:** Run `next lint` or `eslint` to ensure standard lint compliance.
2. **Type Validation:** Run `tsc --noEmit` to verify type safety across the entire TypeScript application.
3. **Build Compilation:** Run `npm run build` to verify Next.js bundle compilation. This catches pages with missing imports or broken components.
4. **Testing Suite:** Executes component and integration tests (e.g., Vitest or Jest) to confirm no existing modules are broken.

#### Sample Workflow Configuration Preview:
```yaml
name: Next.js CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Run Linter
        run: npm run lint

      - name: TypeScript Compiler Check
        run: npm run type-check

      - name: Execute Tests
        run: npm run test

      - name: Verify Build
        run: npm run build
```

---

## Team Members & Contributors

Developed with ❤️ by Group 24 (Faculty of Information Technology, University of Moratuwa):

* **Member 1 (Tech Lead / Admin Panel)** - *[Name/GitHub]*
* **Member 2 (Receptionist)** - *[Name/GitHub]*
* **Member 3 (Waiter Management)** - *[Name/GitHub]*
* **Member 4 (Kitchen Management)** - *[Name/GitHub]*
* **Member 5 (Delivery Management)** - *[Name/GitHub]*

For any queries regarding this repository, please contact the repository administrator or raise an Issue.
