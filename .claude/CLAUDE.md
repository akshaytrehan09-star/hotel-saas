# CLAUDE.md — Hotel Chain Management SaaS (Global)

This is the primary operating guide for all development sessions. Read it completely before writing any code. Sub-guides live at:
- **Backend:** `hotel-saas/server/CLAUDE.md`
- **Frontend:** `hotel-saas/client/CLAUDE.md`

---

## Project Overview

A **multi-tenant SaaS application** for hotel chain management. A single deployment serves multiple hotel chain organizations (tenants). Each tenant manages one or more hotel properties with staff, guests, reservations, billing, and operational workflows scoped strictly to that tenant.

**Primary goals:**
- Allow hotel chains to self-onboard and manage their entire organization from one platform
- Enforce strict tenant isolation at every layer: database, API, and UI
- Start with core operational modules and expand incrementally
- Deliver a production-grade, maintainable codebase with consistent patterns

**Target user roles:**
| Role | Scope |
|---|---|
| `super_admin` | Platform operator — cross-tenant, manages all chains |
| `chain_admin` | Manages their entire chain, all properties, all staff |
| `property_manager` | Manages one or more properties within a chain |
| `front_desk` | Handles reservations and guest check-in/out |
| `housekeeping` | Updates room status and manages tasks |
| `billing_admin` | Accesses financial data, invoices, payments |

---

## Infrastructure

| Concern | Choice |
|---|---|
| Containerization | Docker + Docker Compose (dev) |
| Process manager | PM2 (production) |

`docker-compose.yml` lives at the repo root and defines three services: `mongodb`, `server`, `client`.

---

## Agent Workflow: How You Must Behave

**This is the most important section. Read it completely before every session.**

You operate as a guided, module-by-module development agent. Your behavior follows a strict loop per module. Deviating from this loop without explicit user instruction is not permitted.

### The Module Loop (repeat for every module)

```
STEP 1  → Announce the module you are about to build
STEP 2  → State what you will build: scope, files, API routes, DB schemas
STEP 3  → Ask: "Shall I proceed with this module as described, or would you like to adjust scope?"
STEP 4  → WAIT for user approval. Do not write any code until you receive it.
STEP 5  → Build the module completely, following the Module Template below
STEP 6  → Deliver the Explanation Block (see format below)
STEP 7  → Ask: "Module complete. Review the code and explanation above.
           Reply APPROVED to continue to the next module,
           or describe any changes you'd like first."
STEP 8  → WAIT for user response:
          - APPROVED → move to next module in roadmap, return to STEP 1
          - User requests changes → make changes, re-deliver Explanation Block, return to STEP 7
          - User asks questions → answer them, then return to STEP 7
STEP 9  → Never start the next module until the current one is explicitly approved
```

### Explanation Block Format

After completing each module, deliver an explanation in this exact format:

```
## Module Explanation: [Module Name]

### What Was Built
[2-4 sentences describing what the module does and its boundaries]

### Data Flow
[Numbered steps tracing a representative request from HTTP entry to DB and back.
 Example: 1. Client sends POST /api/reservations with JWT
          2. authenticate middleware verifies token, sets req.user
          3. tenantMiddleware sets req.tenantId from req.user.tenantId
          4. ReservationController validates body via Zod schema
          5. ReservationService checks room availability in MongoDB (filtered by tenantId)
          6. MongoDB saves reservation document with tenantId field
          7. Response returns 201 with created reservation]

### Architecture Decisions
[Bullet list of key decisions and reasoning.]

### Files Created / Modified
| File | Purpose |
|---|---|
| server/src/modules/reservations/reservations.model.ts | Mongoose schema + model |
| ... | ... |

### How to Test This Module
[Specific curl commands or Jest/Vitest test steps to verify core functionality]

### What Comes Next
[One sentence about which module builds on this one]
```

### Handling User Modifications

When a user requests a change before approving a module:
1. Acknowledge the change and state what you will change
2. If there is architectural impact, flag it before proceeding
3. Make the changes
4. Re-deliver the full Explanation Block
5. Return to STEP 7

---

## Module Development Roadmap

Build modules in this order. Do not skip ahead.

### Phase 1 — Foundation

| # | Module | Status |
|---|--------|--------|
| 1.1 | Project Scaffolding | Approved |
| 1.2 | Authentication & RBAC | Not Started |
| 1.3 | Super Admin Panel | Not Started |
| 1.4 | Property Management | Not Started |
| 1.5 | Room Management | Not Started |
| 1.6 | Reservations & Booking | Not Started |
| 1.7 | Billing & Invoicing | Not Started |

### Phase 2 — Enhanced Operations

> Build Phase 2 only after all Phase 1 modules are approved AND the user explicitly confirms they are ready to begin Phase 2.

| # | Module | Status |
|---|--------|--------|
| 2.1 | Housekeeping | Not Started |
| 2.2 | Reporting & Analytics | Not Started |
| 2.3 | Rate Management | Not Started |
| 2.4 | Guest Profiles | Not Started |

---

## Module Template: File Structure

A module is not complete if any file from this template is missing.

### Backend (see `server/CLAUDE.md` for standards)

```
server/src/modules/[module-name]/
  [module-name].routes.ts
  [module-name].controller.ts
  [module-name].service.ts
  [module-name].model.ts
  [module-name].validation.ts
  [module-name].types.ts
  __tests__/
    [module-name].service.test.ts
    [module-name].routes.test.ts
```

### Frontend (see `client/CLAUDE.md` for standards)

```
client/src/modules/[module-name]/
  pages/
    [ModuleName]ListPage.tsx
    [ModuleName]DetailPage.tsx
  components/
    [ModuleName]Table.tsx
    [ModuleName]Form.tsx
    [ModuleName]Card.tsx
  hooks/
    use[ModuleName].ts
  api/
    [module-name].api.ts
  types/
    [module-name].types.ts
  __tests__/
    [ModuleName]Form.test.tsx
```

### Supporting files to update per module

- `server/src/routes/index.ts` — register the new router
- `client/src/router/index.tsx` — add new routes with role guards
- `server/.env.example` — add any new environment variables

---

## Project Directory Structure

```
hotel-saas/
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
├── server/
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env.example
│   ├── Dockerfile
│   ├── jest.config.js
│   └── src/
│       ├── app.ts
│       ├── server.ts
│       ├── types/express.d.ts
│       ├── config/
│       │   ├── database.ts
│       │   ├── env.ts
│       │   └── constants.ts
│       ├── middleware/
│       │   ├── authenticate.ts
│       │   ├── authorize.ts
│       │   ├── tenant.ts
│       │   ├── errorHandler.ts
│       │   ├── rateLimiter.ts
│       │   └── requestLogger.ts
│       ├── modules/
│       │   ├── auth/
│       │   ├── superAdmin/
│       │   ├── properties/
│       │   ├── rooms/
│       │   ├── reservations/
│       │   ├── billing/
│       │   ├── housekeeping/
│       │   ├── reporting/
│       │   ├── rates/
│       │   └── guests/
│       ├── routes/index.ts
│       └── utils/
│           ├── ApiError.ts
│           ├── ApiResponse.ts
│           ├── asyncHandler.ts
│           ├── paginate.ts
│           └── logger.ts
└── client/
    ├── package.json
    ├── tsconfig.json
    ├── vite.config.ts
    ├── vitest.config.ts
    ├── index.html
    ├── Dockerfile
    └── src/
        ├── main.tsx
        ├── App.tsx
        ├── theme.ts
        ├── router/index.tsx
        ├── lib/
        │   ├── axios.ts
        │   └── queryClient.ts
        ├── store/
        │   ├── authStore.ts
        │   └── uiStore.ts
        ├── hooks/
        │   ├── useAuth.ts
        │   ├── useTenant.ts
        │   └── usePermission.ts
        ├── components/
        │   ├── layout/
        │   │   ├── AppShell.tsx
        │   │   ├── Sidebar.tsx
        │   │   ├── Header.tsx
        │   │   └── AuthLayout.tsx
        │   └── shared/
        │       ├── DataTable.tsx
        │       ├── PageHeader.tsx
        │       ├── EmptyState.tsx
        │       ├── ConfirmDialog.tsx
        │       └── StatusChip.tsx
        └── modules/
```

---

## Session Startup Checklist

At the beginning of every session, before writing any code:

1. Read this file and both sub-guides (`server/CLAUDE.md`, `client/CLAUDE.md`)
2. Run `git log --oneline -10` to understand what has been built
3. Ask the user:
   > "Which module should we work on today? Here is the current roadmap status:"
   > [list all modules with: Not Started / In Progress / Complete / Approved]
4. If continuing an in-progress module, ask the user to confirm state before proceeding
5. If starting a new module, begin the Module Loop from STEP 1

---

## Definition of Done

A module is not complete until ALL of the following are true:

- [ ] All backend files use `.ts` extension and compile without TypeScript errors (`tsc --noEmit`)
- [ ] All frontend files use `.ts`/`.tsx` extension and compile without TypeScript errors
- [ ] New router registered in `server/src/routes/index.ts`
- [ ] New pages registered in `client/src/router/index.tsx` with role guards
- [ ] All tenant-scoped Mongoose schemas have `tenantId` field with compound index
- [ ] All service functions filter by `tenantId` on every query
- [ ] Module-specific types defined in `[module-name].types.ts`
- [ ] At least 2 Jest + ts-jest unit tests exist for the service layer
- [ ] At least 2 Jest + Supertest integration tests exist for the routes
- [ ] At least 1 Vitest + RTL test exists for the main form component
- [ ] New environment variables added to `.env.example` and `config/env.ts`
- [ ] The Explanation Block has been delivered to the user
- [ ] The user has replied APPROVED

---

## What You Must Never Do

- Never start a new module before the current one is approved
- Never query MongoDB without `tenantId` in the filter on tenant-scoped collections
- Never trust `tenantId` from the request body; always derive it from the JWT
- Never put business logic in controllers; put it in services
- Never put database calls in controllers; route them through services
- Never return raw Mongoose errors to the client; normalize through `errorHandler`
- Never hardcode secrets, API keys, or URLs; always use environment variables
- Never skip the Explanation Block after completing a module
- Never proceed past the Explanation Block without waiting for user response
- Never transition from Phase 1 to Phase 2 without explicit user confirmation

---

## Resuming After a Break

If a session is interrupted or you are starting fresh mid-project:

1. Read this file and both sub-guides
2. Run `git log --oneline -10` to understand current state
3. Read the key files for the most recently completed module
4. Ask the user:
   > "Modules [X, Y, Z] appear to be complete based on git history.
   > Should I verify the last module's state before continuing, or are you ready to move to [next module]?"
5. Do not assume approval for any module you did not personally complete in this session
