# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

Repository status (autodetected on 2025-10-07)
- Found: README.md
- Not found: package.json, angular.json, nx.json/workspace.json, project.json files, lint/test configs, CI workflows

Implication: build/lint/test commands are not yet defined in this repo. The sections below describe how to detect and run them once the workspace is initialized.

Commands (PowerShell-friendly)

Package install
- Use the lockfile to choose the package manager when package.json appears:
  - If package-lock.json exists: npm ci
  - If pnpm-lock.yaml exists: pnpm install --frozen-lockfile
  - If yarn.lock exists: yarn install --frozen-lockfile

Build
- Angular CLI workspace (angular.json present):
  - npx ng build               # builds the defaultProject
  - npx ng build <project>     # builds a specific project name from angular.json
- Nx workspace (nx.json present):
  - npx nx build <project>

Serve (dev)
- Angular CLI: npx ng serve [<project>] --open
- Nx: npx nx serve <project>

Lint
- If ESLint is configured (e.g., .eslintrc.* present):
  - Angular CLI: npx ng lint [<project>]
  - Nx: npx nx lint <project>
- Or via scripts when package.json defines them: npm run lint

Unit tests
- Angular CLI with Karma/Jasmine (karma.conf.js present):
  - Run all: npx ng test [<project>]
  - Run a single test: prefer fit/fdescribe in the spec file, then npx ng test. Remove fit/fdescribe afterward.
- Jest (jest.config.* present or Nx configured with jest):
  - Run all: npx nx test <project>  (or: npx jest)
  - Filter by name: npx jest -t "<name pattern>"
  - Filter by file: npx jest path/to/file.spec.ts

E2E tests
- Cypress (cypress.config.* or cypress.json present):
  - Headed: npx cypress open
  - Headless: npx cypress run
  - Nx target: npx nx e2e <project>

Formatting
- If Prettier is configured: npx prettier --write .

High-level architecture discovery (what to read, in order)
1) Determine workspace type
   - Angular CLI: angular.json at repo root, with "projects" and possibly "defaultProject".
   - Nx: nx.json at repo root; per-project project.json under apps/* or libs/*.

2) Identify projects and targets
   - Angular CLI: in angular.json, each project defines targets (a.k.a. architect) like build/serve/test/lint/e2e with options (tsConfig, assets, styles, polyfills, etc.).
   - Nx: each project.json defines "targets" (build, serve, test, lint, e2e) and any implicit dependencies. nx.json may include default target configs.

3) TypeScript config layering
   - Root tsconfig.base.json or tsconfig.json defines path aliases and compilerOptions.
   - Project-level tsconfig.app.json / tsconfig.spec.json extend from the root and control app vs test compilation.

4) Linting/testing tools
   - ESLint: .eslintrc.* at root and/or per-project overrides.
   - Karma/Jasmine: karma.conf.js and test.ts bootstrap in Angular CLI setups.
   - Jest: jest.config.* at root and/or per-project; ts-jest or swc config for TS.
   - Cypress: cypress.config.* (modern) or cypress.json (legacy), plus cypress/ directory for specs.

5) CI workflows (if present later)
   - .github/workflows/*.yml define install/build/test steps; mirror their commands locally.

Notes from repository docs
- README.md currently only contains a project title (repo-angular); no actionable commands or architecture details.

Agent rules and provider-specific files
- Not found: CLAUDE.md, .cursor/rules/, .cursorrules, .github/copilot-instructions.md. If these are added later, incorporate their important constraints into future actions.

Operational guidance for Warp in this repo
- Before running commands, first detect workspace type by checking for angular.json (Angular CLI) or nx.json (Nx). Then prefer the corresponding npx ng or npx nx commands.
- When multiple projects exist, prefer the defaultProject in angular.json; otherwise, list projects from angular.json or apps/* and libs/* to select targets.
- Choose the package manager based on the lockfile; do not mix package managers.
- For single-test runs, prefer Jest’s -t when Jest is configured; otherwise, use fit/fdescribe with Karma/Jasmine.
