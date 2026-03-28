---
name: Deployment Strategies
description: This skill should be used when the user asks about "deploying AI agent SaaS", "Vercel deployment", "Docker deployment", "production deployment strategies", "environment variables", or "CI/CD for agent platforms". Provides comprehensive deployment patterns for AI agent SaaS applications.
version: 0.1.0
---

# Deployment Strategies for AI Agent SaaS

Production deployment patterns for AI agent chat SaaS applications across multiple platforms and environments.

## Overview

Deploy AI agent SaaS to:
- Vercel (Next.js optimized, serverless)
- Railway (full-stack, Docker-based)
- AWS/Azure/GCP (enterprise scale)
- Self-hosted (maximum control)

## Vercel Deployment (Recommended for Next.js)

### Quick Start

```bash
# Install Vercel CLI
npm i -g vercel

# Link project
vercel link

# Pull environment variables
vercel env pull

# Deploy preview
vercel

# Deploy production
vercel --prod
```

### Project Configuration

**vercel.json:**
```json
{
  "buildCommand": "turbo run build --filter=frontend",
  "framework": "nextjs",
  "installCommand": "pnpm install",
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "https://backend.yourdomain.com/api/:path*"
    }
  ],
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        }
      ]
    }
  ],
  "crons": [
    {
      "path": "/api/cleanup",
      "schedule": "0 0 * * *"
    }
  ]
}
```

### Monorepo Deployment

For monorepos, deploy multiple apps as separate Vercel projects:

```bash
# Frontend app
cd apps/frontend
vercel --prod

# Docs site
cd apps/docs
vercel --prod
```

Or use Turborepo with Vercel:
```json
{
  "builds": [
    { "src": "apps/frontend/package.json", "use": "@vercel/next" }
  ]
}
```

### Environment Variables

**Development:**
```bash
# Pull from Vercel
vercel env pull .env.local

# Or create manually
cat > .env.local << EOF
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
DATABASE_URL=postgresql://...
ANTHROPIC_API_KEY=sk-...
EOF
```

**Production:**
```bash
# Add via CLI
vercel env add DATABASE_URL production
vercel env add ANTHROPIC_API_KEY production

# Or use dashboard
# https://vercel.com/dashboard → Project → Settings → Environment Variables
```

**Sensitive Variables:**
- Mark as "Sensitive" in dashboard (write-only)
- Never commit to git
- Use different values per environment

## Docker Deployment

### Multi-Service Architecture

**docker-compose.yml:**
```yaml
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - REDIS_HOST=redis
      - DATABASE_URL=${DATABASE_URL}
    depends_on:
      - redis
      - postgres

  frontend:
    build:
      context: .
      dockerfile: apps/frontend/Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_BACKEND_URL=http://backend:8000
    depends_on:
      - backend

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

  postgres:
    image: postgres:16-alpine
    environment:
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  redis_data:
  postgres_data:
```

### Frontend Dockerfile

```dockerfile
# Multi-stage build for Next.js
FROM node:20-alpine AS base
RUN corepack enable && corepack prepare pnpm@latest --activate

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
COPY apps/frontend/package.json ./apps/frontend/
COPY packages/shared/package.json ./packages/shared/
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/apps/frontend/.next/standalone ./
COPY --from=builder /app/apps/frontend/.next/static ./apps/frontend/.next/static
COPY --from=builder /app/apps/frontend/public ./apps/frontend/public
EXPOSE 3000
CMD ["node", "apps/frontend/server.js"]
```

### Backend Dockerfile (Python)

```dockerfile
FROM python:3.13-slim

WORKDIR /app

# Install dependencies
COPY backend/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY backend/ .

# Run with gunicorn
CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "api:app", "--bind", "0.0.0.0:8000"]
```

## Railway Deployment

### Quick Start

```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Initialize project
railway init

# Deploy
railway up

# Add database
railway add --plugin postgresql
railway add --plugin redis
```

### railway.toml

```toml
[build]
builder = "nixpacks"
buildCommand = "pnpm install && pnpm run build"

[deploy]
startCommand = "pnpm start"
healthcheckPath = "/api/health"
healthcheckTimeout = 100
restartPolicyType = "on_failure"
restartPolicyMaxRetries = 10

[[services]]
name = "backend"
source = "backend"

[[services]]
name = "frontend"
source = "apps/frontend"
```

## Environment Management

### Multi-Environment Strategy

**Development (.env.local):**
```bash
NODE_ENV=development
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
NEXT_PUBLIC_URL=http://localhost:3000
DATABASE_URL=postgresql://localhost/dev
REDIS_URL=redis://localhost:6379
```

**Staging (.env.staging):**
```bash
NODE_ENV=production
NEXT_PUBLIC_BACKEND_URL=https://api-staging.yourdomain.com
NEXT_PUBLIC_URL=https://staging.yourdomain.com
DATABASE_URL=${NEON_DATABASE_URL_STAGING}
REDIS_URL=${UPSTASH_REDIS_URL_STAGING}
```

**Production (.env.production):**
```bash
NODE_ENV=production
NEXT_PUBLIC_BACKEND_URL=https://api.yourdomain.com
NEXT_PUBLIC_URL=https://yourdomain.com
DATABASE_URL=${NEON_DATABASE_URL}
REDIS_URL=${UPSTASH_REDIS_URL}
```

### Secrets Management

**Never Commit:**
```gitignore
.env
.env.local
.env.*.local
.vercel
```

**Use Environment Variable Services:**
- Vercel: Built-in env vars
- Railway: Built-in secrets
- AWS: Parameter Store / Secrets Manager
- Doppler: Centralized secrets management

## CI/CD Pipelines

### GitHub Actions (Vercel)

**.github/workflows/deploy.yml:**
```yaml
name: Deploy
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Setup pnpm
        uses: pnpm/action-setup@v3

      - name: Install dependencies
        run: pnpm install

      - name: Build
        run: pnpm run build

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: ${{ github.ref == 'refs/heads/main' && '--prod' || '' }}
```

### GitHub Actions (Docker)

```yaml
name: Docker Build and Push
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: yourusername/agent-saas:latest
```

## Health Checks & Monitoring

### Health Check Endpoint

```typescript
// app/api/health/route.ts
export async function GET() {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    llm_provider: await checkLLM(),
  };

  const healthy = Object.values(checks).every(c => c.status === 'healthy');

  return Response.json({
    status: healthy ? 'healthy' : 'degraded',
    checks,
    timestamp: new Date().toISOString(),
  }, { status: healthy ? 200 : 503 });
}
```

### Monitoring Setup

**Sentry (Error Tracking):**
```typescript
// sentry.client.config.ts
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
});
```

**Vercel Analytics:**
```typescript
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

## Deployment Checklist

Before production deployment:

- [ ] Environment variables configured for all services
- [ ] Database migrations applied
- [ ] Health check endpoints responding
- [ ] Error tracking (Sentry) configured
- [ ] Analytics (Vercel Analytics, PostHog) integrated
- [ ] CORS configured correctly
- [ ] Rate limiting enabled
- [ ] Secrets not committed to git
- [ ] SSL/TLS certificates configured
- [ ] Domain DNS configured
- [ ] Backup strategy implemented
- [ ] Rollback procedure documented
- [ ] Monitoring alerts configured

## Additional Resources

### Reference Files

- **`references/vercel-advanced.md`** - Advanced Vercel configuration, ISR, edge functions
- **`references/docker-optimization.md`** - Multi-stage builds, layer caching, security
- **`references/ci-cd-patterns.md`** - Advanced CI/CD pipelines, testing strategies
- **`references/database-migrations.md`** - Migration strategies for Prisma, Drizzle, SQL

### Example Files

- **`examples/vercel.json`** - Complete Vercel configuration
- **`examples/docker-compose.production.yml`** - Production-ready Docker Compose
- **`examples/github-actions.yml`** - Complete CI/CD pipeline

## Key Principles

1. **Environment Parity**: Dev/staging/prod should be as similar as possible
2. **Immutable Deployments**: Deploy new instances, don't modify running ones
3. **Zero-Downtime**: Use blue-green or rolling deployments
4. **Automated**: CI/CD pipelines for all environments
5. **Observable**: Comprehensive logging and monitoring
6. **Reversible**: Easy rollback mechanisms
7. **Secure**: Secrets management, no hardcoded credentials

## When to Use This Skill

Use this skill when:
- Setting up initial deployment pipeline
- Migrating between hosting providers
- Implementing CI/CD automation
- Configuring multi-environment setups
- Debugging deployment issues
- Optimizing Docker builds
- Setting up monitoring and health checks
- Planning production deployment strategy
