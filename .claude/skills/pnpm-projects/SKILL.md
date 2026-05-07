---
name: pnpm-projects
description: Use pnpm (not npm or yarn) for any new or existing Node.js/JavaScript/TypeScript project work. Triggers when initializing a project, adding dependencies, running scripts, or creating package.json. Examples: "create a Node project", "add express", "set up a TypeScript project", "npm install X", "init a new package".
---

# pnpm for Node.js Projects

Always use `pnpm` instead of `npm` or `yarn`. If the user types `npm <cmd>`, translate to the `pnpm` equivalent and proceed (don't ask).

## Command translation

| Instead of            | Use                  |
|-----------------------|----------------------|
| `npm init -y`         | `pnpm init`          |
| `npm install`         | `pnpm install`       |
| `npm install <pkg>`   | `pnpm add <pkg>`     |
| `npm install -D <pkg>`| `pnpm add -D <pkg>`  |
| `npm install -g <pkg>`| `pnpm add -g <pkg>`  |
| `npm uninstall <pkg>` | `pnpm remove <pkg>`  |
| `npm run <script>`    | `pnpm <script>`      |
| `npm test`            | `pnpm test`          |
| `npx <bin>`           | `pnpm dlx <bin>`     |
| `yarn <anything>`     | `pnpm <equivalent>`  |

## New project setup

```bash
pnpm init                    # creates package.json
pnpm add -D typescript @types/node   # dev deps
pnpm add <runtime-deps>
```

Commit the `pnpm-lock.yaml` (not `package-lock.json` or `yarn.lock`). If a `package-lock.json` or `yarn.lock` exists in a project we're converting, delete it after generating `pnpm-lock.yaml`.

## Workspaces / monorepos

Use `pnpm-workspace.yaml` at the repo root:

```yaml
packages:
  - "packages/*"
  - "apps/*"
```

Run a script in one workspace: `pnpm --filter <name> <script>`.

## Don't

- Don't run `npm install` or `yarn install` — even if a lockfile from another tool exists, switch to pnpm.
- Don't suggest `npx`; use `pnpm dlx` (one-off) or `pnpm exec` (project-local bin).
- Don't add `engines.npm` in `package.json`; add `packageManager: "pnpm@<version>"` instead.

## If pnpm isn't installed

Tell the user to install it once: `brew install pnpm` (macOS) or `corepack enable && corepack prepare pnpm@latest --activate`. Don't fall back to npm.
