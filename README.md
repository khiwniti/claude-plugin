# AI Agent SaaS Expert Plugin

**Professional AI agent team specialized in building production-ready AI agent chat SaaS applications**

This Claude Code plugin provides a comprehensive expert team that audits, validates, and improves AI agent chat SaaS applications. Based on analysis of production systems like Kortix/AgentPress, it covers architecture, security, performance, sandbox integration, and deployment patterns.

---

## 🎯 What This Plugin Does

The **AI Agent SaaS Expert** plugin provides 8 specialized agents and 8 knowledge skills that help you:

✅ **Audit architecture** - Validate monorepo structure, routing patterns, database design
✅ **Ensure production readiness** - Check security, monitoring, error handling, scaling
✅ **Verify sandbox integration** - Validate Docker/Daytona setup, health checks
✅ **Optimize routing** - Analyze Next.js App Router patterns, dynamic/static routes
✅ **Harden security** - Audit auth, API security, input validation
✅ **Tune performance** - Cache strategies, database optimization, bundle size
✅ **Validate integrations** - Check LLM providers, APIs, webhooks

---

## 🚀 Quick Start

### Installation

```bash
# Install plugin globally
cc plugin install ai-agent-saas-expert

# Or use locally for a specific project
cd your-ai-agent-saas-project
cc --plugin-dir /path/to/ai-agent-saas-expert
```

### Usage

#### **Invoke Agents:**

```bash
# Architecture audit
Ask Claude: "I need an architecture review of my AI agent SaaS project"

# Production readiness check
Ask Claude: "Is my project production-ready?"

# Security audit
Ask Claude: "Review my authentication and API security"

# Performance analysis
Ask Claude: "Analyze performance and caching strategies"
```

#### **Access Skills:**

Skills are automatically loaded when relevant topics are discussed:
- Mention "deployment" → `deployment-strategies` skill loads
- Mention "sandbox" → `sandbox-integration-guide` skill loads
- Mention "routing" → `nextjs-app-router-guide` skill loads

---

## 🤖 Specialized Agents

### 1. **architecture-auditor**
Reviews monorepo structure, workspace dependencies, routing patterns, database architecture.

**When to use:**
- After setting up project structure
- Before major architectural changes
- During code reviews

**Example:**
```
User: "Review my monorepo architecture"
Agent: *Analyzes package.json, tsconfig, workspace structure, generates report with recommendations*
```

### 2. **production-readiness**
Comprehensive production checklist: security, monitoring, error handling, scaling.

**When to use:**
- Before deployment
- After major features
- Quarterly reviews

**Example:**
```
User: "Is my project ready for production?"
Agent: *Checks env vars, error boundaries, logging, rate limiting, generates checklist*
```

### 3. **sandbox-integration-validator**
Verifies sandbox integration (Daytona/Docker), health checks, resource allocation.

**When to use:**
- After sandbox setup
- When sandbox issues occur
- Before scaling sandbox infrastructure

### 4. **nextjs-routing-optimizer**
Analyzes Next.js App Router patterns, dynamic/static routing, middleware.

**When to use:**
- When adding new routes
- Performance optimization
- After Next.js upgrades

### 5. **security-hardening**
Audits authentication, API security, input validation, SQL injection prevention.

**When to use:**
- Before security reviews
- After auth changes
- Before handling sensitive data

### 6. **performance-tuning**
Analyzes caching strategies, database optimization, bundle size, Core Web Vitals.

**When to use:**
- Slow page loads
- High database latency
- Poor Core Web Vitals scores

### 7. **sandbox-validator**
Validates sandbox container health, service status, and resource utilization.

**When to use:**
- Troubleshooting sandbox issues
- Monitoring container health
- Optimizing resource allocation

### 8. **integration-validator**
Verifies LLM integrations, external APIs, webhooks, third-party services.

**When to use:**
- After adding integrations
- When webhook failures occur
- Before going live with new providers

---

## 📚 Knowledge Skills

### 1. **ai-agent-saas-patterns**
Architecture patterns for AI agent platforms, monorepo structure, agent runtime design.

### 2. **deployment-strategies**
Vercel deployment, Docker containerization, Railway/cloud deployment, env management.

### 3. **security-checklist**
Auth patterns, API security, input validation, secrets management.

### 4. **sandbox-integration-guide**
Daytona setup, Docker health checks, service orchestration, VNC integration.

### 5. **nextjs-app-router-guide**
Route groups, dynamic vs static routes, server actions, middleware patterns.

### 6. **database-architecture**
Multi-database patterns (PostgreSQL + Graph + Redis), query optimization, migrations.

### 7. **llm-integration-patterns**
LiteLLM setup, multi-provider fallbacks, streaming responses, error handling.

### 8. **production-checklist**
Pre-deployment checklist, monitoring setup, error tracking, performance optimization.

---

## ⚙️ Configuration

Create `.claude/ai-agent-saas-expert.local.md` in your project:

```markdown
# AI Agent SaaS Expert Configuration

## Deployment Target
- Primary: Vercel
- Container: Docker
- Backup: Railway

## Sandbox Provider
- Provider: Daytona
- Health Check Endpoint: /health
- VNC Enabled: true

## Databases
- PostgreSQL: Neon
- Graph: Neo4j Aura
- Cache: Upstash Redis

## LLM Providers
- Primary: Anthropic (Claude)
- Fallback: OpenAI (GPT-4)
- Router: LiteLLM

## Security Requirements
- Compliance: GDPR, SOC 2
- Auth: Supabase Auth
- Rate Limiting: Upstash Ratelimit

## Performance Targets
- Core Web Vitals: ≥90
- API Response: <200ms p95
- Database Query: <50ms p95
```

---

## 🏗️ Architecture Patterns

This plugin is designed for AI agent SaaS applications with:

- **Monorepo structure** (pnpm/npm workspaces)
- **Next.js App Router** (15+)
- **Multi-database architecture** (PostgreSQL + Graph + Redis)
- **Sandbox integration** (Daytona, E2B, Docker)
- **LLM providers** (Anthropic, OpenAI, multi-provider)
- **Real-time features** (WebSocket, streaming)

---

## 🤝 Contributing

Contributions welcome! This plugin is based on production patterns from real AI agent SaaS platforms.

### Adding Industry-Specific Skills

Create skills for specific industries:
- `healthcare-compliance` - HIPAA, patient data handling
- `finance-security` - PCI DSS, transaction security
- `education-privacy` - FERPA, student privacy

### Extending Agent Capabilities

Add specialized agents for:
- Accessibility auditing
- Multi-tenancy validation
- API versioning strategies
- Internationalization (i18n)

---

## 📄 License

MIT License - feel free to use in your projects!

---

## 🔗 Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Vercel Platform](https://vercel.com/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [LiteLLM Documentation](https://docs.litellm.ai)
- [Daytona Documentation](https://daytona.io/docs)

---

**Ready to build production-ready AI agent SaaS applications?** 🚀

Install the plugin and start getting expert guidance on architecture, security, performance, and more!
