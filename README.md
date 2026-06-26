# AWS Amplify Gen2 + Next.js Starter (Turborepo)

A production-ready starter template for building fullstack serverless applications with **AWS Amplify Gen2** and **Next.js**, organized as a Turborepo monorepo.

## What You Can Build With This Template

- Authentication flows (email login, user sessions via Amplify Auth)
- API/data-driven features backed by Amplify Data (AppSync + DynamoDB)
- Secure frontend + backend integration through generated Amplify outputs
- Extendable backend resources (Storage, Functions, custom CDK constructs via Amplify Gen2)

Clone this repo and start implementing fullstack features immediately — no manual infrastructure wiring required.

## Tech Stack

| Layer | Technology |
|---|---|
| Cloud Backend | AWS Amplify Gen2 |
| Frontend Framework | Next.js 15 (App Router, Turbopack) |
| UI Library | React 19 |
| Language | TypeScript |
| Styling | Tailwind CSS v4 + DaisyUI v5 |
| State Management | Redux Toolkit + React Redux |
| Validation | Zod |
| Monorepo | Turborepo + npm workspaces |
| Linting/Formatting | Biome |
| Testing | Vitest + Testing Library |
| Env Variables | dotenvx |
| Logging (Lambda) | AWS Lambda Powertools Logger |

## Project Structure

```text
.
├── amplify.yml                  # Amplify Hosting build/deploy config
├── turbo.json                   # Turborepo pipeline config
├── package.json                 # Root monorepo scripts and workspace definitions
├── apps/
│   └── webapp/                  # Next.js frontend application (@ui/webapp)
│       ├── app/
│       │   ├── layout.tsx
│       │   ├── page.tsx         # Home page
│       │   ├── admin/page.tsx   # Protected admin route
│       │   └── auth/login/page.tsx
│       ├── components/
│       │   ├── header/          # Header component
│       │   └── login/           # Login form component
│       ├── context/
│       │   ├── amplify.context.tsx       # Amplify client-side config provider
│       │   ├── auth-details.context.tsx  # Auth state context
│       │   └── store-provider.contex.tsx # Redux store provider
│       ├── redux/
│       │   ├── store.ts
│       │   └── auth/            # Auth slice, selectors
│       ├── utils/
│       │   └── amplify.server.ts # Amplify server-side utilities
│       ├── middleware.ts         # Next.js middleware (auth guards)
│       └── package.json
└── packages/
    └── backend/                 # Amplify Gen2 backend (@packages/backend)
        ├── package.json
        └── amplify/
            ├── backend.ts       # Backend entrypoint (composes all resources)
            ├── auth/resource.ts # Cognito auth resource definition
            └── data/resource.ts # AppSync data schema and auth rules
```

## Prerequisites

- **Node.js** >= 18 and **npm** >= 11 (`packageManager: npm@11.9.0`)
- An **AWS account** with appropriate permissions for Amplify, Cognito, AppSync, and DynamoDB
- **AWS credentials configured locally** — see [AWS credential setup](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

> If you plan to use additional AWS services (e.g., S3, SES, SNS), install the corresponding `@aws-sdk/*` client packages in the relevant workspace.

## Setup

### 1. Configure Placeholders

Before running anything, update the placeholder values in `packages/backend/package.json`:

```json
"scripts": {
  "dev": "npx dotenvx run --env-file .env -- ampx sandbox --identifier <unique-identifier>"
}
```

Replace `<unique-identifier>` with a short unique string that identifies your sandbox environment (e.g., your name or feature branch). This prevents sandbox collisions when multiple developers share the same AWS account.

You may also want to update the `name`, `author`, and `description` fields in `packages/backend/package.json` and the root `package.json` to match your project.

### 2. Configure AWS Credentials

Ensure your local machine has AWS credentials configured with sufficient permissions:

```bash
aws configure
# or use environment variables / SSO / IAM Identity Center
```

The Amplify sandbox will use these credentials to provision cloud resources in your AWS account.

### 3. Install Dependencies

From the repository root:

```bash
npm install
```

### 4. Start the Amplify Backend Sandbox

From `packages/backend`, start the local sandbox (provisions real AWS resources):

```bash
npm run dev
```

This runs `ampx sandbox --identifier <your-identifier>` via dotenvx. Once deployment completes, `amplify_outputs.json` is generated in `packages/backend/`. The root `package.json` is configured to copy this file to `apps/webapp/amplify_outputs.json` automatically.

### 5. Start the Frontend

From the repository root:

```bash
npm run dev -- --filter=@ui/webapp
```

Or from `apps/webapp` directly:

```bash
npm run dev
```

The Next.js app runs on [http://localhost:3000](http://localhost:3000) with Turbopack.

## Available Scripts

Run these from the repository root via Turbo:

| Command | Description |
|---|---|
| `npm run dev` | Start all packages in dev mode |
| `npm run build` | Build all packages |
| `npm run lint` | Lint all packages (Biome) |
| `npm run check-types` | TypeScript type-check all packages |
| `npm run format` | Format all files with Prettier |

## Environment Variables

The backend uses [dotenvx](https://dotenvx.com/) to load `.env` files. Create a `.env` file in `packages/backend/` for any secrets or config your Lambda functions or backend resources need:

```env
# packages/backend/.env
MY_SECRET=value
```

## Extending This Template

### Adding AWS Resources

Extend `packages/backend/amplify/backend.ts` with additional Amplify Gen2 resources:

```ts
import { storage } from './storage/resource';
import { myFunction } from './functions/my-function/resource';

defineBackend({ auth, data, storage, myFunction });
```

Refer to the [Amplify Gen2 docs](https://docs.amplify.aws/react/build-a-backend/) for resource patterns.

### Using Additional AWS SDK Clients

Install the relevant `@aws-sdk` client in the workspace where it's needed:

```bash
# In packages/backend
npm install @aws-sdk/client-s3

# In apps/webapp (for client-safe SDK use only)
npm install @aws-sdk/client-...
```

### Adding Frontend Pages or Components

Add new routes under `apps/webapp/app/` following the Next.js App Router conventions. Add shared components under `apps/webapp/components/`.

## Deployment to AWS Amplify Hosting

The `amplify.yml` at the repo root defines the CI/CD pipeline for Amplify Hosting:

- **Backend app** (`packages/backend`): runs `ampx pipeline-deploy` to deploy the backend branch
- **Frontend app** (`apps/webapp`): builds and deploys the Next.js app

Connect this repository to an Amplify Hosting app in the AWS Console and Amplify will handle deployments on push.

## Useful Links

- [AWS Amplify Gen2 Docs](https://docs.amplify.aws/gen2/)
- [Amplify Auth Resource](https://docs.amplify.aws/react/build-a-backend/auth/)
- [Amplify Data Resource](https://docs.amplify.aws/react/build-a-backend/data/)
- [Next.js Docs](https://nextjs.org/docs)
- [Turborepo Docs](https://turborepo.dev/docs)
- [DaisyUI Docs](https://daisyui.com/)
- [Biome Docs](https://biomejs.dev/)
