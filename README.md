# ChaufHER Infrastructure Technical Design

<!-- CI/CD Status Badges -->
[![Plan/Apply](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/deploy.yml?branch=main&label=Plan%2FApply&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/deploy.yml)
[![Lint](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/lint.yml?branch=main&label=Lint&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/lint.yml)
[![Security](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/security.yml?branch=main&label=Security&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/security.yml)
[![Policy](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/policy.yml?branch=main&label=Policy&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/policy.yml)

---

## Table of Contents

### Business & Strategy
- [Product Overview](#product-overview)
- [Purpose & Value Proposition](#purpose--value-proposition)
- [Target Audience](#target-audience)
- [Expected Outcomes & KPIs](#expected-outcomes--kpis)
- [Operational Excellence](#operational-excellence)

### Architecture & Design
- [Design Philosophy](#design-philosophy)
- [Architectural Overview](#architectural-overview)
- [System Interfaces](#system-interfaces)
- [Infrastructure Patterns](#infrastructure-patterns)

### Integration & Collaboration
- [App Integration & Alignment](#app-integration--alignment)
- [Choreography with App Repos](#choreography-with-app-repos)
- [Mobile Checklist](MOBILE_CHECKLIST.md)

### Operations & Support
- [Contact / Support](#contact--support)
- [Operational Runbooks](#operational-runbooks)
- [Security & Compliance](#security--compliance)
- [Policy & Governance](#policy--governance)

### Developer Guide
- [Getting Started](#getting-started)
- [Repository Layout](#repository-layout)
- [Contributing / PR Process](#contributing--pr-process)
- [Automation Commands](#automation-commands)
- [Testing & Validation](#testing--validation)
- [Deployment Guide](#deployment-guide)

### Reference
- [Versioning & Release Notes](#versioning--release-notes)
- [Documentation Index](#documentation-index)

---

# Business & Strategy

## Product Overview

The ChaufHER Infrastructure repository is the foundational platform powering the ChaufHER ride-sharing ecosystem. It delivers enterprise-grade infrastructure as code (IaC) and DevOps automation for Azure-native environments, ensuring secure, reliable, and scalable operations across all platform services.

### Mission

Enable ChaufHER's business growth by providing:
- **Reliability**: 99.9%+ uptime for safety-critical ride-sharing operations
- **Security**: Compliance-ready infrastructure protecting driver and rider data
- **Agility**: Rapid feature deployment supporting business innovation
- **Cost Efficiency**: Predictable cloud spend with automated optimization

### Strategic Value

| Business Need | Infrastructure Solution | Business Impact |
|---------------|------------------------|-----------------|
| Launch new markets quickly | Multi-region deployment automation | 80% faster time-to-market |
| Ensure rider/driver safety | 99.9%+ uptime, <15min incident response | Trust and retention |
| Scale during peak demand | Auto-scaling, load balancing | Revenue protection during surges |
| Regulatory compliance | Automated policy enforcement, audit trails | Risk mitigation, legal readiness |
| Control operational costs | Cost monitoring, budget alerts, optimization | Predictable unit economics |

---

## Purpose & Value Proposition

### Why This Repository Exists

The ChaufHER Infra repository solves critical business and technical challenges:

#### Business Challenges Solved

1. **Market Expansion Speed**: Deploy new regional infrastructure in hours, not weeks
2. **Operational Risk**: Reduce security incidents and downtime through automation
3. **Cost Predictability**: Eliminate surprise cloud bills with proactive monitoring
4. **Regulatory Readiness**: Built-in compliance controls for data protection regulations
5. **Team Productivity**: Self-service infrastructure for development teams

#### Technical Challenges Solved

1. **Configuration Drift**: Automated detection and remediation of infrastructure changes
2. **Environment Consistency**: Identical dev/staging/prod setups eliminate "works on my machine"
3. **Security Governance**: Codified security policies prevent misconfigurations
4. **Disaster Recovery**: Tested, automated failover procedures
5. **Operational Visibility**: Real-time dashboards for health, cost, and compliance

### Example Business Scenario

**Situation**: Product team needs to launch a new "scheduled rides" feature requiring additional database capacity and caching infrastructure.

**Traditional Approach** (Manual):
- 2 weeks: Submit infrastructure request, wait for provisioning
- 3 days: Manual configuration, testing, security review
- High risk: Configuration errors, security gaps, cost overruns

**ChaufHER Infra Approach** (Automated):
- 2 hours: Developer submits PR with infrastructure requirements
- 30 minutes: Automated validation, security scan, cost estimate
- 15 minutes: Approved deployment to dev environment
- 1 day: Validated promotion to production
- Low risk: Automated compliance, tested patterns, cost controls

**Business Impact**: 90% faster delivery, 70% fewer incidents, predictable costs

---

## Target Audience

### Primary Users

#### DevOps Engineers
- **Daily Activities**: Define infrastructure, build pipelines, monitor system health
- **Key Needs**: Clear patterns, automation tools, operational visibility
- **Success Metrics**: Deployment frequency, incident response time, cost efficiency

#### Backend Developers
- **Daily Activities**: Request infrastructure for new features, troubleshoot integration issues
- **Key Needs**: Self-service provisioning, clear documentation, environment parity
- **Success Metrics**: Time to provision resources, environment reliability

### Secondary Users

#### Product Leadership
- **Key Interests**: Time-to-market, operational costs, risk exposure
- **Information Needs**: Deployment velocity, uptime metrics, cost trends
- **Decision Support**: Capacity planning, regional expansion readiness

#### Security & Compliance
- **Key Interests**: Governance posture, audit readiness, incident response
- **Information Needs**: Policy compliance, security events, access controls
- **Decision Support**: Risk assessments, compliance reporting

#### New Engineers (Onboarding)
- **Key Needs**: Clear structure, getting-started guides, best practices
- **Success Metrics**: Time to first contribution, understanding of patterns

### Market Segments

This infrastructure approach serves:
- **Ride-sharing platforms** requiring real-time, safety-critical operations
- **Multi-tenant SaaS** needing strong isolation and compliance
- **Regulated industries** requiring audit trails and policy enforcement
- **High-growth startups** balancing agility with operational maturity

---

## Expected Outcomes & KPIs

### Business Outcomes

#### Reliability & Availability
- **Target**: 99.9% uptime (43 minutes downtime/month maximum)
- **Measurement**: Azure Monitor uptime dashboards, incident logs
- **Business Impact**: Customer trust, revenue protection, brand reputation

#### Operational Agility
- **Target**: Deploy infrastructure changes in <1 hour
- **Measurement**: Pipeline execution time, PR merge-to-deploy duration
- **Business Impact**: Faster feature delivery, competitive advantage

#### Cost Predictability
- **Target**: <5% monthly cost variance from forecast
- **Measurement**: Azure Cost Management dashboards, budget alerts
- **Business Impact**: Accurate financial planning, improved unit economics

#### Security & Compliance
- **Target**: <30 minute incident response time, zero policy violations
- **Measurement**: Incident logs, Azure Policy compliance reports
- **Business Impact**: Regulatory readiness, reduced legal risk

#### Team Productivity
- **Target**: 80% reduction in manual infrastructure tasks
- **Measurement**: Time tracking, automation coverage metrics
- **Business Impact**: Engineering focus on product features

### Key Performance Indicators

| Category | KPI | Target | Measurement |
|----------|-----|--------|-------------|
| **Reliability** | System Uptime | 99.9%+ | Azure Monitor |
| **Reliability** | Mean Time to Recovery | <15 min | Incident logs |
| **Agility** | Deployment Frequency | 10+/day | Pipeline metrics |
| **Agility** | Time to Rollback | <15 min | Pipeline logs |
| **Cost** | Monthly Cost Variance | <5% | Cost Management |
| **Cost** | Cost per Transaction | Decreasing | Custom metrics |
| **Security** | Incident Response Time | <30 min | Incident logs |
| **Security** | Policy Compliance | 100% | Azure Policy |
| **Quality** | Failed Deployment Rate | <5% | Pipeline metrics |
| **Quality** | Configuration Drift | 0 instances | Drift detection |

### Success Metrics Dashboard

Live dashboards track these metrics in real-time:
- **Executive Dashboard**: Uptime, cost trends, incident summary
- **Operations Dashboard**: Deployment status, resource health, alerts
- **Cost Dashboard**: Spend by service, forecast vs. actual, optimization opportunities
- **Security Dashboard**: Policy compliance, access reviews, security events

---

## Operational Excellence

### Service Level Objectives (SLOs)

| Service | Availability SLO | Latency SLO | Error Rate SLO |
|---------|-----------------|-------------|----------------|
| API Gateway | 99.9% | p95 <200ms | <0.1% |
| Database | 99.95% | p95 <50ms | <0.01% |
| Real-time (SignalR) | 99.5% | p95 <500ms | <1% |
| Static Assets (CDN) | 99.99% | p95 <100ms | <0.1% |

### Incident Response

#### Response Time Commitments

| Severity | Description | Response Time | Resolution Target |
|----------|-------------|---------------|-------------------|
| P1 (Critical) | Complete service outage | 15 minutes | 1 hour |
| P2 (High) | Degraded service, partial outage | 30 minutes | 4 hours |
| P3 (Medium) | Minor feature impact | 4 hours | 24 hours |
| P4 (Low) | Cosmetic, no user impact | 24 hours | 1 week |

### Business Continuity

#### Disaster Recovery

- **RTO (Recovery Time Objective)**: 1 hour
- **RPO (Recovery Point Objective)**: 15 minutes
- **DR Testing**: Quarterly full failover drills
- **Geographic Redundancy**: Primary (East US) + Secondary (West US 2)

#### Backup Strategy

| Resource Type | Backup Frequency | Retention | Recovery Testing |
|---------------|------------------|-----------|------------------|
| Databases | Continuous (15min RPO) | 35 days | Monthly |
| Configuration | On every change | 90 days | Quarterly |
| Secrets | Versioned (all versions) | Indefinite | Monthly |
| Infrastructure State | Daily | 90 days | Quarterly |

---

# Architecture & Design

## Design Philosophy

### Core Principles

#### 1. Infrastructure as Code (IaC)
- **All infrastructure is version-controlled code**
- No manual changes in production environments
- Every change is reviewable, testable, and reversible
- Enables audit trails and compliance documentation

#### 2. Security by Default
- **Least-privilege access** for all resources and identities
- Secrets never in source code (Key Vault only)
- Encryption at rest and in transit (mandatory)
- Network isolation via private endpoints and NSGs

#### 3. Environment Parity
- **Dev, staging, and prod are identical** (except scale)
- Same code deploys to all environments
- Reduces "works in staging" production failures
- Parameterized for environment-specific values only

#### 4. Automation First
- **Manual processes are technical debt**
- Automated validation, deployment, and remediation
- Self-healing infrastructure where possible
- Runbooks for non-automatable procedures

#### 5. Observable & Auditable
- **Every action is logged and traceable**
- Real-time dashboards for operational visibility
- Audit logs for compliance and forensics
- Correlation IDs across all systems

### Design Patterns

#### Modular Architecture
- Reusable Bicep/Terraform modules for common resources
- Composable building blocks (networking, compute, data, monitoring)
- DRY principle: Define once, use everywhere

#### Immutable Infrastructure
- Replace rather than modify resources
- Blue-green deployments for zero-downtime updates
- Rollback = deploy previous version

#### Policy as Code
- Azure Policy definitions in source control
- Automated compliance checking in CI/CD
- Preventive controls (deny non-compliant resources)

---

## Architectural Overview

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         ChaufHER Platform                        │
└─────────────────────────────────────────────────────────────────┘

┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Mobile Apps │────▶│  API Gateway │────▶│   Backend    │
│ (iOS/Android)│     │  (App Service│     │   Services   │
└──────────────┘     │   + CDN)     │     │ (App Service)│
                     └──────────────┘     └──────┬───────┘
                                                  │
                     ┌────────────────────────────┼────────────┐
                     │                            │            │
                ┌────▼────┐              ┌───────▼──────┐ ┌──▼──────┐
                │ Database│              │ SignalR      │ │  Cache  │
                │(Postgres│              │(Real-time)   │ │ (Redis) │
                │  + CosmosDB)           └──────────────┘ └─────────┘
                └─────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    Cross-Cutting Concerns                        │
├─────────────────────────────────────────────────────────────────┤
│  Key Vault  │  Monitoring  │  Networking  │  Identity (AAD)    │
│  (Secrets)  │ (App Insights│   (VNet/NSG) │  (RBAC/B2C)        │
└─────────────────────────────────────────────────────────────────┘
```

### Infrastructure Data Flow

```
┌──────────────┐
│  Developer   │
│  Creates PR  │
└──────┬───────┘
       │
       ▼
┌──────────────────────────────────────────────────────────────┐
│                    CI/CD Pipeline                             │
├──────────────────────────────────────────────────────────────┤
│  1. Lint & Validate  │  2. Security Scan  │  3. Policy Check │
│  4. Cost Estimate    │  5. Plan (Dry Run) │  6. Peer Review  │
└──────────────────────┬───────────────────────────────────────┘
                       │ (Approval Required)
                       ▼
┌──────────────────────────────────────────────────────────────┐
│                  Deployment Pipeline                          │
├──────────────────────────────────────────────────────────────┤
│  1. Apply Changes    │  2. Update RBAC    │  3. Rotate Secrets│
│  4. Smoke Tests      │  5. Update Dashboards                 │
└──────────────────────┬───────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────┐
│                   Azure Resources                             │
├──────────────────────────────────────────────────────────────┤
│  App Services  │  Databases  │  Networking  │  Monitoring    │
└──────────────────────┬───────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────┐
│              Monitoring & Alerting                            │
├──────────────────────────────────────────────────────────────┤
│  Dashboards  │  Alerts  │  Audit Logs  │  Cost Reports      │
└──────────────────────────────────────────────────────────────┘
```

### Regional Architecture

#### Multi-Region Deployment

```
Primary Region (East US)          Secondary Region (West US 2)
┌─────────────────────┐           ┌─────────────────────┐
│   Active Services   │◄─────────▶│  Standby Services   │
│   - API Gateway     │  Geo-Repl │   - API Gateway     │
│   - App Services    │           │   - App Services    │
│   - Databases       │           │   - Databases       │
└─────────────────────┘           └─────────────────────┘
         │                                  │
         └──────────┬───────────────────────┘
                    │
                    ▼
         ┌──────────────────────┐
         │   Traffic Manager    │
         │  (Global Load Bal.)  │
         └──────────────────────┘
```

---

## System Interfaces

### Azure Services Integration

| Service | Purpose | Configuration | Monitoring |
|---------|---------|---------------|------------|
| **Azure App Service** | Host backend APIs and web apps | Auto-scaling, deployment slots | App Insights, health probes |
| **Azure Static Web Apps** | Serve client applications | CDN integration, custom domains | CDN analytics, availability |
| **Azure Key Vault** | Secrets and certificate management | RBAC, soft-delete, purge protection | Access logs, rotation alerts |
| **Azure Database for PostgreSQL** | Primary relational database | HA, geo-replication, backups | Query performance, connections |
| **Azure Cosmos DB** | NoSQL for real-time data | Multi-region writes, consistency | RU consumption, latency |
| **Azure Cache for Redis** | Session and data caching | Clustering, persistence | Hit rate, memory usage |
| **Azure SignalR Service** | Real-time WebSocket connections | Auto-scaling, sticky sessions | Connection count, latency |
| **Azure CDN** | Static asset delivery | Caching rules, compression | Cache hit ratio, bandwidth |
| **Azure Monitor** | Centralized logging and metrics | Log Analytics workspace | Alert rules, dashboards |
| **Azure Application Insights** | APM and distributed tracing | Auto-instrumentation | Performance, exceptions |

### External Integrations

| Service | Purpose | Authentication | Secrets Location |
|---------|---------|----------------|------------------|
| **Twilio** | SMS notifications | API key | Key Vault |
| **SendGrid** | Email delivery | API key | Key Vault |
| **Stripe** | Payment processing | API key + webhook secret | Key Vault |
| **Google Maps API** | Geocoding and routing | API key | Key Vault |
| **Firebase (FCM)** | Push notifications (Android) | Service account JSON | Key Vault |
| **Apple APNs** | Push notifications (iOS) | Certificate + key | Key Vault |

### API Contracts

All backend APIs follow OpenAPI 3.0 specification:
- **Base Path**: Environment-specific URLs (documented in environment configs)
- **Versioning**: Major version in URL path (v1, v2)
- **Authentication**: JWT Bearer tokens (Azure AD B2C)
- **Rate Limiting**: Configurable per environment
- **Documentation**: Auto-generated from code annotations

---

## Infrastructure Patterns

### Environment Separation

#### Resource Naming Convention

Pattern: `{product}-{environment}-{service}-{region}`

Examples:
- chaufher-prod-api-eastus
- chaufher-staging-db-westus2
- chaufher-dev-kv-eastus

#### Environment Configuration

| Environment | Purpose | Scale | Data | Access |
|-------------|---------|-------|------|--------|
| **dev** | Feature development | Minimal (1 instance) | Synthetic | All developers |
| **staging** | Pre-production testing | Production-like (2 instances) | Scrubbed prod copy | QA + DevOps |
| **prod** | Live customer traffic | Auto-scaling (3-50 instances) | Real customer data | DevOps + On-call |
| **PR-{number}** | Ephemeral PR testing | Minimal (1 instance) | Synthetic | PR author only |

### Security Patterns

#### Network Security

```
Internet
   │
   ▼
┌─────────────────┐
│  Azure Firewall │  ◄── WAF rules, DDoS protection
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  App Gateway    │  ◄── TLS termination, routing
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────┐
│         VNet (10.0.0.0/16)          │
│  ┌──────────────────────────────┐   │
│  │  App Subnet (10.0.1.0/24)    │   │
│  │  - App Services              │   │
│  └──────────────────────────────┘   │
│  ┌──────────────────────────────┐   │
│  │  Data Subnet (10.0.2.0/24)   │   │
│  │  - Databases (Private Link)  │   │
│  └──────────────────────────────┘   │
└─────────────────────────────────────┘
```

#### Identity & Access Management

```
┌──────────────────┐
│  Azure AD B2C    │  ◄── User authentication
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Service         │  ◄── Application identity
│  Principals      │      (Managed Identity preferred)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  RBAC Roles      │  ◄── Least-privilege assignments
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Azure Resources │  ◄── Fine-grained access control
└──────────────────┘
```

### Data Management Patterns

#### Database Strategy

| Data Type | Technology | Replication | Backup |
|-----------|-----------|-------------|--------|
| Transactional | PostgreSQL | Sync replica (same region) | Continuous (15min RPO) |
| User profiles | Cosmos DB | Multi-region writes | Continuous |
| Session state | Redis Cache | Persistence enabled | Daily snapshots |
| Audit logs | Log Analytics | Geo-redundant | 90 days retention |
| File storage | Blob Storage | GRS (geo-redundant) | Soft delete (30 days) |

#### Secrets Management

```
┌──────────────────────────────────────────────────────┐
│                   Key Vault Hierarchy                 │
├──────────────────────────────────────────────────────┤
│                                                       │
│  chaufher-shared-kv          ◄── Cross-env secrets   │
│  ├── ssl-certificates                                │
│  └── shared-api-keys                                 │
│                                                       │
│  chaufher-{env}-kv           ◄── Environment secrets │
│  ├── database-connection-string                      │
│  ├── jwt-signing-key                                 │
│  └── external-api-keys                               │
│                                                       │
│  chaufher-{env}-{app}-kv     ◄── App-specific        │
│  ├── app-specific-secret-1                           │
│  └── app-specific-secret-2                           │
│                                                       │
└──────────────────────────────────────────────────────┘

Access Pattern:
- Managed Identity (preferred)
- Service Principal (CI/CD only)
- RBAC: Key Vault Secrets User (read-only)
- Rotation: Automated every 90 days
```

---

# Integration & Collaboration

## App Integration & Alignment

### Integration Architecture

The infrastructure provides the foundation for mobile and web applications. Key integration points:

#### 1. API Gateway & Backend Services

**Infrastructure Provides:**
- API Gateway with TLS termination
- Load-balanced backend services
- Health check endpoints
- Rate limiting and throttling

**App Requirements:**
- Use documented base URL per environment
- Implement exponential backoff for retries
- Honor rate limit headers
- Include correlation IDs in requests

#### 2. Authentication & Authorization

**Infrastructure Provides:**
- Azure AD B2C tenant configuration
- JWT signing keys (rotated automatically)
- Token validation endpoints
- RBAC policies for backend resources

**App Requirements:**
- Implement OAuth 2.0 / OIDC flow
- Cache tokens securely (Keychain/Keystore)
- Refresh tokens before expiry
- Handle 401/403 responses gracefully

#### 3. Real-Time Communication (SignalR)

**Infrastructure Provides:**
- SignalR service endpoints
- WebSocket and long-polling fallback
- Connection scaling and load balancing
- Keepalive configuration

**App Requirements:**
- Implement automatic reconnection logic
- Use correlation IDs for message tracing
- Gracefully degrade to polling if WebSocket fails
- Queue messages during offline periods

#### 4. Push Notifications

**Infrastructure Provides:**
- FCM/APNs credentials in Key Vault
- Notification hub configuration
- Topic-based routing
- Delivery tracking

**App Requirements:**
- Register device tokens on app launch
- Handle notification permissions
- Process background notifications
- Update token on refresh

#### 5. Telemetry & Monitoring

**Infrastructure Provides:**
- Application Insights instrumentation keys
- Log Analytics workspace
- Correlation ID propagation
- Custom metrics endpoints

**App Requirements:**
- Emit structured logs with correlation IDs
- Track custom events (user actions, errors)
- Report performance metrics
- Include environment context in telemetry

#### 6. Feature Flags & Configuration

**Infrastructure Provides:**
- Azure App Configuration service
- Feature flag definitions
- Configuration refresh webhooks
- Environment-specific overrides

**App Requirements:**
- Poll for configuration updates
- Implement feature flag checks
- Cache configuration locally
- Handle missing/default values

### Environment Variables & Endpoints

Environment-specific configurations are documented in the `environments/` directory:
- `environments/dev/` - Development environment configuration
- `environments/staging/` - Staging environment configuration
- `environments/prod/` - Production environment configuration

Each environment includes:
- API base URLs
- SignalR endpoints
- Authentication tenant information
- Feature flag service URLs
- Monitoring and telemetry keys

### Integration Checklist

For mobile engineers, see **[MOBILE_CHECKLIST.md](MOBILE_CHECKLIST.md)** for detailed integration steps.

#### Quick Checklist

- [ ] Configure environment-specific API endpoints
- [ ] Implement JWT authentication with token refresh
- [ ] Set up SignalR with reconnection logic
- [ ] Register for push notifications (FCM/APNs)
- [ ] Integrate Application Insights telemetry
- [ ] Implement feature flag checks
- [ ] Add correlation IDs to all requests
- [ ] Test offline/degraded connectivity scenarios
- [ ] Verify rate limiting behavior
- [ ] Test failover to secondary region

---

## Choreography with App Repos

### Cross-Repository Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                  Development Workflow                        │
└─────────────────────────────────────────────────────────────┘

Step 1: Infrastructure Change Required
┌──────────────┐
│ Product Team │──▶ "We need Redis cache for new feature"
└──────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────────┐
│  chaufher-infra Repo                                      │
├──────────────────────────────────────────────────────────┤
│  1. DevOps creates PR: Add Redis module                  │
│  2. CI validates, estimates cost                         │
│  3. Review + approval                                    │
│  4. Deploy to dev → staging → prod                       │
│  5. Output: Connection string in Key Vault               │
└──────────────────┬───────────────────────────────────────┘
                   │
                   ▼
Step 2: App Integration
┌──────────────────────────────────────────────────────────┐
│  chaufher-app Repo                                        │
├──────────────────────────────────────────────────────────┤
│  1. Backend dev creates PR: Add Redis client             │
│  2. Fetch connection string from Key Vault               │
│  3. Implement caching logic                              │
│  4. CI tests against dev environment                     │
│  5. Deploy to staging → prod                             │
└──────────────────────────────────────────────────────────┘
```

### Triggering Infrastructure from App Repos

#### Automated Infrastructure Provisioning

Apps can declare infrastructure requirements using an `infra-requirements.json` file in their repository. When this file changes, it triggers infrastructure provisioning workflows.

The requirements file specifies:
- Required infrastructure version
- Needed services (cache, storage, etc.)
- Environment-specific configurations
- Resource specifications

### Environment Promotion Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│     Dev     │────▶│   Staging   │────▶│    Prod     │
└─────────────┘     └─────────────┘     └─────────────┘
      │                    │                    │
      │ Auto-promote       │ Manual approval    │
      │ on PR merge        │ + smoke tests      │
      │                    │                    │

Infra:  Deploy new     Validate infra     Production
        resources      + run load tests   deployment
        ↓                    ↓                  ↓
App:    Deploy app     Integration tests  Staged rollout
        to use new     with new infra     (10% → 50% → 100%)
        resources
```

### Promotion Process

Infrastructure promotion follows a controlled workflow:

1. **Development**: Automatic deployment on PR merge
2. **Staging**: Requires validation and smoke tests
3. **Production**: Requires manual approval and compliance checks

The promotion script validates:
- Environment health
- Compliance requirements
- Cost impact
- Security posture

### Deployment Gating

Infrastructure version requirements can gate app deployments to ensure compatibility. App repositories can specify minimum infrastructure versions, and deployment pipelines verify these requirements before proceeding.

---

# Operations & Support

## Contact / Support

### Maintainer

**Hans Jurgens Smit** - Platform Lead & Infrastructure Owner
- **Email**: hans@chaufher.app
- **GitHub**: @hansjsmit
- **Response Time**: Best effort (24-48 hours for non-urgent)

### Getting Help

**Infrastructure Questions:**
1. Check [Documentation Index](#documentation-index)
2. Review existing issues in GitHub
3. Create new issue with template

**Deployment Issues:**
1. Check CI/CD Status Badges
2. Review pipeline logs in GitHub Actions
3. Create issue with error details and PR link

**Feature Requests:**
1. Create issue with label `enhancement`
2. Describe business need and use case
3. Include any relevant examples or references

---

## Operational Runbooks

### Runbook Index

All runbooks are located in `docs/runbooks/` with step-by-step procedures:

| Scenario | Runbook | Automation Script | Frequency |
|----------|---------|------------------|-----------|
| **Configuration Drift** | [drift-detection.md](docs/runbooks/drift-detection.md) | `drift-detection.sh` | Daily (automated) |
| **Rollback Deployment** | [rollback.md](docs/runbooks/rollback.md) | `rollback.sh` | As needed |
| **Failover to Secondary Region** | [failover.md](docs/runbooks/failover.md) | `failover-test.sh` | Quarterly (drill) |
| **Secret Rotation** | [secret-rotation.md](docs/runbooks/secret-rotation.md) | `rotate-secrets.sh` | Every 90 days |
| **Scale Resources** | [scaling.md](docs/runbooks/scaling.md) | `scale.sh` | As needed |
| **Incident Response** | [incident-response.md](docs/runbooks/incident-response.md) | Manual | During incidents |
| **Cost Spike Investigation** | [cost-investigation.md](docs/runbooks/cost-investigation.md) | `cost-analyze.sh` | As needed |
| **Security Incident** | [security-incident.md](docs/runbooks/security-incident.md) | Manual | During incidents |
| **DR Drill** | [dr-drill.md](docs/runbooks/dr-drill.md) | `dr-drill.sh` | Quarterly |
| **Onboard New Service** | [onboard-service.md](docs/runbooks/onboard-service.md) | Semi-automated | As needed |

### Common Operations

#### Drift Detection

Automated daily checks identify configuration drift between deployed infrastructure and source code definitions. Drift detection reports show resources that differ from code definitions, specific configuration changes, and remediation recommendations. Remediation can be automated or manual based on change severity.

#### Rollback Procedures

Infrastructure rollback capabilities include listing available rollback points (tagged versions), rollback to specific version, and emergency rollback (expedited process for critical issues). All rollbacks are logged and require appropriate approvals based on environment.

#### Failover Testing

Regular failover testing validates disaster recovery readiness through non-disruptive testing to secondary region, DR readiness validation, and full DR drills (quarterly with stakeholder notification).

#### Secret Rotation

Automated secret rotation occurs every 90 days with advance notification (14 days before expiry), automated rotation for supported secret types, manual rotation procedures for complex secrets, and rotation status tracking and reporting.

#### Scaling Operations

Infrastructure scaling supports vertical scaling (tier changes), horizontal scaling (instance count), auto-scaling configuration, and scaling status monitoring.

---

## Security & Compliance

### Security Framework

ChaufHER infrastructure implements defense-in-depth security across five layers:

1. **Network Security**: Azure Firewall with WAF rules, DDoS Protection, NSGs, Private Endpoints
2. **Identity & Access**: Azure AD B2C, Managed Identities, RBAC with least-privilege, Conditional Access
3. **Data Protection**: Encryption at rest and in transit (TLS 1.2+), Key Vault secrets management, Database TDE
4. **Application Security**: Input validation, OWASP Top 10 mitigations, Security headers, Rate limiting
5. **Monitoring & Response**: Azure Defender, SIEM, Automated threat detection, Incident response procedures

### Secrets Management

**Key Vault Hierarchy:**
- `chaufher-shared-kv`: Cross-environment secrets (Platform Lead only, manual rotation)
- `chaufher-{env}-kv`: Environment-specific secrets (Environment RBAC, 90-day automated rotation)
- `chaufher-{env}-{app}-kv`: Application-specific secrets (Managed Identity access, 90-day rotation)

**Secret Lifecycle:** Creation → Storage (encrypted, versioned) → Access (RBAC enforced, audit logged) → Rotation (automated every 90 days, 14-day advance alerts) → Expiration → Deletion (90-day soft-delete)

**Secret Detection:** Pre-commit hooks, CI/CD pipeline blocks, regular repository scans using Gitleaks and TruffleHog, automated remediation for detected secrets.

### Compliance & Auditing

| Framework | Status | Evidence Location | Audit Frequency |
|-----------|--------|-------------------|-----------------|
| **SOC 2 Type II** | In progress | `docs/compliance/soc2/` | Annual |
| **GDPR** | Compliant | `docs/compliance/gdpr/` | Continuous |
| **ISO 27001** | Planned | N/A | TBD |

**Audit Logging:** All infrastructure changes logged with Who (User/Service Principal), What (action), When (UTC timestamp), Where (region/resource group), Why (PR/issue link), and How (deployment method).

**Compliance Reporting:** Automated reports include Azure Policy compliance, Security Center recommendations, access reviews, encryption status, backup validation, and incident summaries.

### Security Scanning

| Tool | Scope | Frequency | Blocking |
|------|-------|-----------|----------|
| **Checkov** | IaC security misconfigurations | Every PR | Yes |
| **Trivy** | Container vulnerabilities | Every PR | Yes (high/critical) |
| **Gitleaks** | Secret detection | Every commit | Yes |
| **Azure Defender** | Runtime threats | Continuous | Alert only |
| **Dependabot** | Dependency vulnerabilities | Daily | Alert only |

**Manual Reviews:** Architecture review before major launches, annual penetration testing, quarterly access reviews, post-mortems after P1/P2 security incidents.

---

## Policy & Governance

### Azure Policy Enforcement

Policies are organized by category and enforcement level:

| Category | Enforcement | Examples |
|----------|-------------|----------|
| **Tagging** | Deny | environment, owner, cost-center (required) |
| **Security** | Deny | HTTPS only, TLS 1.2+, no public storage |
| **Compliance** | Deny | Allowed regions, encryption required |
| **Cost Control** | Audit | Allowed SKUs, resource limits |
| **Operational** | Audit | Diagnostic settings, backup enabled |

**Policy Location:** All policies defined in `policies/` directory (tagging, security, cost, compliance, exceptions)

**Exception Process:** Create issue → Document justification → Get approval → Add to `policies/exceptions.json` with expiry → Link in PR

### Cost Management

**Framework:** Per-environment budgets with warning and critical thresholds, automated alerts, and deployment blocks at critical levels.

**Alerts:** Daily spike detection (20%+ increase), budget warnings (advance notification), critical alerts (leadership escalation + blocks)

**Optimization:** Regular analysis of cost drivers, identification of optimization opportunities (reserved instances, right-sizing), impact estimation for changes, baseline comparisons

**Allocation:** Resources tagged with environment, owner, cost-center, project, and optional feature tags for granular tracking

**Governance Dashboard:** Real-time metrics via Azure Cost Management, Policy compliance dashboard, Security posture (Defender), and Resource inventory (Resource Graph)

---

# Developer Guide

## Getting Started

### Prerequisites

| Tool | Version | Installation |
|------|---------|--------------|
| **Azure CLI** | v2.50+ | [aka.ms/installazurecli](https://aka.ms/installazurecli) |
| **Bicep CLI** | v0.20+ | `az bicep install` |
| **Terraform** (optional) | v1.5+ | [terraform.io](https://www.terraform.io/downloads) |
| **GitHub CLI** (optional) | Latest | [cli.github.com](https://cli.github.com/) |
| **Git** | v2.30+ | [git-scm.com](https://git-scm.com/) |

### Initial Setup

1. **Clone Repository**: Clone the repository and navigate to the project directory
2. **Azure Authentication**: Authenticate with Azure CLI and set your default subscription
3. **Service Principal Setup**: For CI/CD automation, create a service principal with appropriate permissions and store credentials securely in GitHub Secrets
4. **Verify Tools**: Verify all required tools are installed and up-to-date
5. **Install Pre-Commit Hooks**: Install pre-commit hooks to validate changes before committing

### Local Validation

**Quick Validation:** Validate all Bicep templates using the Azure CLI

**Comprehensive Validation:** Run the complete validation suite including Bicep linting, Terraform linting (if applicable), YAML linting, security scanning, and secret detection

### Quick Deploy to Dev

**Using Deployment Script (Recommended):** Use the provided deployment script which handles template validation, security scanning, cost estimation, deployment execution, smoke testing, and output reporting.

**Manual Deployment:** Build Bicep templates, create deployment with appropriate parameters, monitor deployment progress, and verify resource provisioning.

### Required Permissions

| Resource | Role | Scope |
|----------|------|-------|
| Subscription | Contributor | Target subscription |
| Key Vault | Key Vault Secrets Officer | Per-environment vault |
| Azure AD | Application Administrator | For service principal management |
| Resource Groups | Owner | For RBAC assignments |

---

## Repository Layout

```
chaufher-infra/
├── modules/                    # Reusable Bicep/Terraform modules
│   ├── app-service/           # Azure App Service definitions
│   ├── key-vault/             # Key Vault and secrets management
│   ├── networking/            # VNet, NSG, Private Endpoints
│   ├── monitoring/            # Log Analytics, App Insights, Alerts
│   ├── storage/               # Storage accounts, CDN
│   └── main.bicep             # Root orchestration template
├── environments/               # Environment-specific configurations
│   ├── dev/                   # Development parameters
│   ├── staging/               # Staging parameters
│   └── prod/                  # Production parameters
├── pipelines/                  # CI/CD workflow definitions
│   ├── deploy.yml             # Main deployment pipeline
│   ├── lint.yml               # Linting and static analysis
│   ├── security.yml           # Security scanning (Trivy, Checkov)
│   ├── policy.yml             # Azure Policy compliance checks
│   └── templates/             # Reusable pipeline templates
├── scripts/                    # Operational and automation scripts
│   ├── deploy.sh              # Deployment orchestration
│   ├── rollback.sh            # Rollback procedures
│   ├── rotate-secrets.sh      # Secret rotation automation
│   └── drift-detection.sh     # Configuration drift checks
├── policies/                   # Azure Policy definitions
│   ├── cost-policies.json     # Cost control policies
│   ├── security-policies.json # Security baseline policies
│   └── tagging-policies.json  # Resource tagging requirements
├── docs/                       # Documentation and diagrams
│   ├── architecture/          # Architecture diagrams (draw.io)
│   ├── runbooks/              # Operational runbooks
│   └── adr/                   # Architecture Decision Records
├── README.md                   # This file
├── MOBILE_CHECKLIST.md         # Mobile app integration checklist
└── CHANGELOG.md                # Release notes and version history
```

---

## Contributing / PR Process

### Branch Naming Convention

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feature/<ticket>-<description>` | `feature/CHAUF-123-add-redis-cache` |
| Bugfix | `bugfix/<ticket>-<description>` | `bugfix/CHAUF-456-fix-keyvault-access` |
| Hotfix | `hotfix/<description>` | `hotfix/prod-dns-resolution` |
| Release | `release/v<version>` | `release/v1.2.0` |

### PR Requirements

1. **PR Template**: Fill out all sections in `.github/PULL_REQUEST_TEMPLATE.md`
2. **Required Reviewers**: At least 1 reviewer (maintainer for prod changes)
3. **Gating Rules**:
   - [ ] All CI checks pass (lint, plan, security scan)
   - [ ] Policy checks pass (Azure Policy compliance)
   - [ ] Cost estimate within acceptable threshold
   - [ ] Smoke tests pass for environment changes
   - [ ] No secrets detected in code

### Adding Exceptions

To request a policy exception:

1. Create an issue with label `policy-exception`
2. Document the business justification
3. Get approval from maintainer
4. Add exception to `policies/exceptions.json` with expiry date
5. Link the exception issue in your PR

### Review Checklist

- [ ] Code follows naming conventions
- [ ] Parameters are environment-agnostic
- [ ] Secrets use Key Vault references (no hardcoded values)
- [ ] Resource tags include `environment`, `owner`, `cost-center`
- [ ] Breaking changes documented in PR description

---

## Automation Commands

### Plan & Apply Pipelines

Deployment scripts support multiple actions:
- **Plan**: Dry-run to preview changes
- **Apply**: Execute infrastructure changes
- **Strict mode**: Additional validations for production

### Run Linting Locally

Linting tools validate Bicep syntax and best practices, Terraform formatting and validation, YAML structure, and security misconfigurations.

### Policy Checks

Policy validation includes Azure Policy compliance checking, OPA policy evaluation, and security scanning with Checkov.

### Smoke Tests

Post-deployment validation: run complete smoke test suite, test specific modules, verify resource health and connectivity.

### CI Job Equivalents

| Local Command | CI Job |
|---------------|--------|
| Lint script | `lint.yml` workflow |
| Policy check script | `policy.yml` workflow |
| Deploy plan | `deploy.yml` (plan stage) |
| Security scan | `security.yml` workflow |

---

## Testing & Validation

### Testing Strategy

- **Policy-as-Code**: Azure Policies and OPA gate workflows; non-compliant PRs blocked automatically
- **Smoke Testing**: Synthetic probes validate newly provisioned resources; post-deployment health checks
- **Failover Drills**: Regular tabletop exercises, automated recovery scenarios, rollback validation, cross-region testing
- **Environment Parity**: Automated consistency checks across dev/staging/prod; configuration drift detection

### Testing Tools & Approaches

- **Linting**: tflint, bicep lint, checkov for code quality
- **Security Scanning**: Trivy, Azure Defender for Cloud for vulnerabilities
- **Secret Detection**: Gitleaks, TruffleHog for credential protection
- **Plan-and-Destroy**: Ensures idempotent operations
- **Cost Regression**: Prevents accidental cost spikes
- **Cross-Region Testing**: DR and failover scenario validation

### Testing Environments

- **Segregation**: Isolated resource groups and credentials per environment
- **Ephemeral**: PR-specific environments for complex tests
- **Data Handling**: Non-production uses scrubbed or synthetic data
- **DR Validation**: Scripts deployed and tested in all environments

---

## Deployment Guide

### Pipeline Flow

1. **Code Review**: PR triggers plan and compliance check
2. **Approval**: Maintainer approval required before apply
3. **Apply**: Gated pipeline applies changes to target environment
4. **Staged Rollout**: For high-impact resources, apply changes in waves
5. **Validation**: Post-apply scripts and dashboards confirm correctness
6. **Rollback Guidance**: Automated reversion using previous pipeline state
7. **Environment Promotion**: Successful changes promoted upward

### Deployment Environment

- **Azure Account Hierarchy**: Separate subscriptions/management groups per environment
- **Resource Groups**: Scoped by environment and domain
- **Key Vault**: Dedicated vault per environment
- **Access Control**: Contributor/Reader/Owner roles mapped to workflow stages
- **High Availability**: Critical infra deployed in multiple zones/regions
- **Disaster Recovery**: DR-ready environments with validated runbooks

### Deployment Tools

- **IaC Frameworks**: Bicep (primary), Terraform and ARM templates (as needed)
- **Pipeline Orchestration**: GitHub Actions
- **Shared Scripts**: Library of PowerShell/Bash scripts
- **Admin Dashboards**: Real-time oversight and validation

### Post-Deployment Verification

- Resource existence checks confirm expected resources
- Secrets validation verifies references, access policies, rotation policies
- Monitoring hooks validate resource health alerts
- Policy compliance reports generated
- Incident drills validate operational readiness

---

## Versioning & Release Notes

### Version Format

We follow [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes to infrastructure contracts
- **MINOR**: New features, backward-compatible
- **PATCH**: Bug fixes, security patches

### Release Process

Releases are tagged automatically on merge to `main`. Manual tagging available if needed. All changes documented in [CHANGELOG.md](CHANGELOG.md) with sections for Added, Fixed, Security, and Breaking Changes.

---

## Documentation Index

| Document | Location | Description |
|----------|----------|-------------|
| Architecture Diagrams | [docs/architecture/](docs/architecture/) | System and network diagrams (draw.io) |
| API Contracts | [docs/api/](docs/api/) | OpenAPI specs for infra-managed services |
| ADRs | [docs/adr/](docs/adr/) | Architecture Decision Records |
| Runbooks | [docs/runbooks/](docs/runbooks/) | Operational procedures |
| Policy Docs | [policies/](policies/) | Azure Policy definitions |
| Compliance Evidence | [docs/compliance/](docs/compliance/) | SOC 2, GDPR, ISO documentation |
| Mobile Checklist | [MOBILE_CHECKLIST.md](MOBILE_CHECKLIST.md) | Mobile app integration guide |
| Changelog | [CHANGELOG.md](CHANGELOG.md) | Release notes and version history |

### Living Documentation

- **Confluence**: ChaufHER Infra Space for collaborative documentation
- **Architecture Diagrams**: Updated on every major release
- **Runbooks**: Reviewed quarterly or after incidents
- **Compliance Docs**: Maintained continuously for audit readiness
