# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AMRIT HWC-Scheduler-UI — an Angular 16 application for managing appointment scheduling at Health and Wellness Centres within the PSMRI AMRIT platform. Provides specialist calendar views, timesheet management, staff scheduling, appointment viewing, SMS templates, and reporting. Designed to be loaded as an embedded module (entry via `/redirin` redirect) within the broader AMRIT telemedicine workflow.

## Build & Development Commands

| Command | Purpose |
|---------|---------|
| `npm start` | Dev server on **port 4206** (`ng serve`) |
| `npm run build-dev` | AOT dev build (increased heap) |
| `npm run build-prod` | AOT production build (increased heap) |
| `npm run build-ci` | CI build (generates `environment.ci.ts` from template + env vars) |
| `npm test` | Run tests (Karma + Jasmine) |
| `npm run lint` | ESLint |
| `npm run commit` | Commitizen conventional commit prompt |

## Git Submodule: Common-UI

`Common-UI/` is a git submodule from `https://github.com/PSMRI/Common-UI`. Initialize with:
```bash
git submodule update --init --recursive
```

## Architecture

### Module Structure

- **AppModule** — root module with hash-based routing (`useHash: true`)
- **CoreModule** — singleton services, guards, shared components, directives, `MaterialModule`
- **SchedulerModule** (lazy-loaded at `/telemedicine`) — main scheduling features:
  - `dashboard` — scheduler dashboard
  - `appointment-view` — viewing and managing appointments
  - `specialization-calander-view` — calendar view by specialization (uses FullCalendar)
  - `timesheet` — specialist timesheet/availability management
  - `mystaff` — staff management
  - `sms-template` — SMS notification templates
  - `reports` — scheduling reports

### Routing

Root routes: `redirin` (entry point redirect), `telemedicine` (lazy-loaded SchedulerModule)

Entry is via `RedirOpenComponent` at `/redirin` which handles authentication passthrough from the parent AMRIT application.

### State Management

Angular services with `BehaviorSubject`/`Subject`. Encrypted sessionStorage via `ng-cryptostore`.

### HTTP / Auth

- `AuthGuard` protects the telemedicine/scheduler routes
- Auth tokens passed from parent application via redirect

## Key Dependencies

- Angular 16 + Angular Material 16
- `@fullcalendar/core`, `@fullcalendar/daygrid`, `@fullcalendar/timegrid`, `@fullcalendar/list`, `@fullcalendar/interaction` — calendar views
- `ngx-mat-timepicker` — time selection
- `ng2-charts` + `chart.js` — reporting charts
- `ngx-bootstrap` — additional UI components
- Bootstrap (layout)
- `ng-cryptostore` — encrypted sessionStorage
- `exceljs` + `file-saver` — Excel report generation
- `html2canvas` — screenshot/print support
- `ngx-webcam` — webcam capture
- `ts-luxon` — date/time handling

## Code Conventions

- **License header**: All source files begin with the AMRIT GPL-3.0 license block
- **Component prefix**: `app`
- **Commit convention**: Conventional Commits enforced via commitlint + Husky
- **Pre-commit hook**: `lint-staged` runs ESLint `--fix` on staged `.ts`, `.css`, and `.html` files

## Environment Configuration

Environment files in `src/environments/`. CI build uses EJS template (`environment.ci.ts.template`) with env vars for API endpoints, encryption keys, and configuration.
