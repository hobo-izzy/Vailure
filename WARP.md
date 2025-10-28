# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Common commands

- Install deps

```powershell path=null start=null
npm ci
```

- Dev server (Next.js)

```powershell path=null start=null
npm run dev
```

- Build and run

```powershell path=null start=null
npm run build
npm start
```

- Lint (Next.js ESLint preset)

```powershell path=null start=null
npm run lint
```

- Type-check (strict TS)

```powershell path=null start=null
npx tsc -p tsconfig.json --noEmit
```

- Amplify Gen 2 backend (provision sandbox and generate amplify_outputs.json)

```powershell path=null start=null
npx amplify sandbox
```

- Deploy backend changes to AWS

```powershell path=null start=null
npx amplify push
```

Notes:
- The frontend imports configuration from `@/amplify_outputs.json`; running the Amplify sandbox or a deploy will generate/update this file.
- No test framework or test scripts are defined in `package.json`.

## Architecture overview

- Frontend (Next.js 14 App Router)
  - Entry points in `app/`:
    - `app/layout.tsx` defines global layout and metadata.
    - `app/page.tsx` is a client component that configures Amplify from `@/amplify_outputs.json`, generates an Amplify Data client, and renders/creates `Todo` items via `observeQuery()` and `create()`.
  - Styling via `app/globals.css`, `app/app.css`, and `app/page.module.css`.
  - `next.config.js` uses default config.
  - TypeScript config (`tsconfig.json`): strict mode, Next.js TS plugin, path alias `@/* -> ./*`. The `amplify` folder is excluded from the app TS build.

- Backend (AWS Amplify Gen 2, code-defined infrastructure under `amplify/`)
  - `amplify/backend.ts` is the root backend definition, composing resources:
    - Auth: `amplify/auth/resource.ts` enables email-based sign-in via `defineAuth`.
    - Data: `amplify/data/resource.ts` defines a `Todo` model with public API key access and 30-day API key expiry; exported via `defineData`.
  - Storage: `amplify/resource.ts` contains a `defineStorage` declaration, but it is not currently imported into `backend.ts` (unused in the active backend composition).
  - `amplify/package.json` sets ESM (`"type": "module"`); `amplify/tsconfig.json` targets modern ESM and maps `$amplify/*` to generated outputs.

## Key repo facts from README

- Template: Next.js (App Router) + AWS Amplify, prepped for Cognito (Auth), AppSync (GraphQL API), and DynamoDB (DB).
- Deployment docs: https://docs.amplify.aws/nextjs/start/quickstart/nextjs-app-router-client-components/#deploy-a-fullstack-app-to-aws
