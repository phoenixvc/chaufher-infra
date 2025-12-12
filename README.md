# ChaufHER Infra Technical Design

<!-- CI/CD Status Badges -->
[![Plan/Apply](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/deploy.yml?branch=main&label=Plan%2FApply&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/deploy.yml)
[![Lint](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/lint.yml?branch=main&label=Lint&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/lint.yml)
[![Security](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/security.yml?branch=main&label=Security&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/security.yml)
[![Policy](https://img.shields.io/github/actions/workflow/status/phoenixvc/chaufher-infra/policy.yml?branch=main&label=Policy&logo=github)](https://github.com/phoenixvc/chaufher-infra/actions/workflows/policy.yml)

---

## Table of Contents
- [Getting Started](#getting-started)
- [Repository Layout](#repository-layout)
- [Contributing / PR Process](#contributing--pr-process)
- [Automation Commands](#automation-commands)
- [Security & Secret Handling](#security--secret-handling)
- [Policy, Cost & Compliance](#policy-cost--compliance)
- [Contact / Support](#contact--support)
- [Operational Runbooks](#operational-runbooks)
- [Versioning & Release Notes](#versioning--release-notes)
- [Choreography with App Repos](#choreography-with-app-repos)
- [Accessibility & Docs](#accessibility--docs)
- [Mobile Checklist](MOBILE_CHECKLIST.md)
- [Product Overview](#product-overview)
- [Purpose](#purpose)
- [Design Details](#design-details)
- [App Integration & Alignment](#app-integration--alignment)

---

## Getting Started

### Prerequisites

1. **Azure CLI** (v2.50+): Install from [aka.ms/installazurecli](https://aka.ms/installazurecli)
2. **Bicep CLI** (v0.20+): `az bicep install` or standalone from [Bicep releases](https://github.com/Azure/bicep/releases)
3. **Terraform** (optional, v1.5+): If using Terraform modules, install from [terraform.io](https://www.terraform.io/downloads)
4. **GitHub CLI** (optional): `gh` for PR and workflow automation
5. **Service Principal**: An Azure AD service principal with Contributor role on target subscriptions

### Initial Setup

```bash
# 1. Clone the repository
git clone https://github.com/phoenixvc/chaufher-infra.git
cd chaufher-infra

# 2. Login to Azure
az login
az account set --subscription "<SUBSCRIPTION_ID>"

# 3. Verify Bicep installation
az bicep version

# 4. Set up service principal (if not already done)
az ad sp create-for-rbac --name "chaufher-infra-sp" --role Contributor \
  --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

### Local Validation (One-Liner)

Run this to validate all Bicep templates before pushing:

```bash
# Validate all Bicep files
find . -name "*.bicep" -exec az bicep build --file {} \;
```

### Quick Deploy (Crash-Step)

```bash
# Build and deploy to dev environment
az bicep build --file ./modules/main.bicep

az deployment group create \
  --resource-group chaufher-dev-rg \
  --template-file ./modules/main.bicep \
  --parameters @./environments/dev/parameters.json
```

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
2. **Required Reviewers**:
   - At least 1 DevOps engineer
   - At least 1 security review for `prod/` changes
3. **Gating Rules**:
   - [ ] All CI checks pass (lint, plan, security scan)
   - [ ] Policy checks pass (Azure Policy compliance)
   - [ ] Cost estimate within threshold (< 10% increase)
   - [ ] Smoke tests pass for environment changes
   - [ ] No secrets detected in code

### Adding Exceptions

To request a policy exception:

1. Create an issue with label `policy-exception`
2. Document the business justification
3. Get approval from Security Lead and Platform Owner
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

```bash
# Run plan locally (dry-run)
./scripts/deploy.sh --env dev --action plan

# Apply changes (requires approval in pipeline)
./scripts/deploy.sh --env dev --action apply

# Plan for production (extra validations)
./scripts/deploy.sh --env prod --action plan --strict
```

### Run Linting Locally

```bash
# Bicep linting
az bicep lint --file ./modules/main.bicep

# Terraform linting (if applicable)
cd modules/terraform && tflint --init && tflint

# All linters via script
./scripts/lint-all.sh
```

### Policy Checks

```bash
# Run Azure Policy compliance check
./scripts/policy-check.sh --env dev

# Run OPA policy evaluation
opa eval --data ./policies/ --input ./environments/dev/parameters.json "data.chaufher.allow"

# Checkov security scan
checkov -d ./modules/ --framework bicep
```

### Smoke Tests

```bash
# Run post-deployment smoke tests
./scripts/smoke-test.sh --env dev

# Test specific module
./scripts/smoke-test.sh --env dev --module app-service
```

### CI Job Equivalents

| Local Command | CI Job |
|---------------|--------|
| `./scripts/lint-all.sh` | `lint.yml` workflow |
| `./scripts/policy-check.sh` | `policy.yml` workflow |
| `./scripts/deploy.sh --action plan` | `deploy.yml` (plan stage) |
| `checkov -d ./modules/` | `security.yml` workflow |

---

## Security & Secret Handling

### Key Vault Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| Environment-scoped | Per-env secrets | `chaufher-{env}-kv` |
| Application-scoped | App-specific secrets | `chaufher-{env}-api-kv` |
| Shared services | Cross-app secrets | `chaufher-shared-kv` |

### Least-Privilege Access

```bicep
// Example: Granting minimal Key Vault access
resource keyVaultAccess 'Microsoft.Authorization/roleAssignments@2022-04-01' = {
  name: guid(keyVault.id, principalId, 'Key Vault Secrets User')
  scope: keyVault
  properties: {
    roleDefinitionId: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '4633458b-17de-408a-b874-0445c86b69e6') // Key Vault Secrets User
    principalId: principalId
    principalType: 'ServicePrincipal'
  }
}
```

### Secret Rotation Policy

- **Rotation Frequency**: Every 90 days (automated)
- **Notification**: 14 days before expiry via Azure Monitor alert
- **Automation**: `./scripts/rotate-secrets.sh` or scheduled pipeline

```bash
# Manual secret rotation
./scripts/rotate-secrets.sh --vault chaufher-dev-kv --secret api-key

# Check rotation status
az keyvault secret list --vault-name chaufher-dev-kv --query "[].{name:name,expires:attributes.expires}"
```

### Secret Detection

```bash
# Pre-commit hook runs automatically, or run manually:
./scripts/secret-scan.sh

# Using gitleaks
gitleaks detect --source . --verbose

# Using truffleHog
trufflehog filesystem . --only-verified
```

### Critical Rules

> **NEVER commit secrets to source control.**
> - Use Key Vault references in Bicep: `@Microsoft.KeyVault(SecretUri=...)`
> - Use GitHub Secrets for CI/CD credentials
> - Run `./scripts/secret-scan.sh` before every commit
> - Pre-commit hooks block pushes containing detected secrets

---

## Policy, Cost & Compliance

### Policy Definitions

Policies are defined in `policies/` and enforced via Azure Policy:

| Policy | File | Enforcement |
|--------|------|-------------|
| Required tags | `tagging-policies.json` | Deny |
| Allowed regions | `security-policies.json` | Deny |
| Allowed SKUs | `cost-policies.json` | Audit |
| Key Vault soft-delete | `security-policies.json` | Deny |
| HTTPS only | `security-policies.json` | Deny |

### Cost Checks

Run cost estimation before merging:

```bash
# Estimate cost impact
./scripts/cost-estimate.sh --env dev --template ./modules/main.bicep

# Compare against baseline
./scripts/cost-compare.sh --baseline ./costs/baseline-dev.json
```

### Cost Thresholds

| Environment | Monthly Budget | Alert at | Block at |
|-------------|---------------|----------|----------|
| dev | $500 | 80% | 100% |
| staging | $1,000 | 80% | 100% |
| prod | $5,000 | 70% | 90% |

### Alert Definitions

```json
{
  "alerts": [
    {
      "name": "cost-spike-alert",
      "threshold": "20% daily increase",
      "action": "notify-slack"
    },
    {
      "name": "budget-warning",
      "threshold": "80% of monthly budget",
      "action": "notify-email"
    },
    {
      "name": "budget-critical",
      "threshold": "95% of monthly budget",
      "action": "block-deployments"
    }
  ]
}
```

### Running Compliance Reports

```bash
# Generate compliance report
./scripts/compliance-report.sh --env prod --output ./reports/

# View Azure Policy compliance
az policy state summarize --resource-group chaufher-prod-rg
```

---

## Contact / Support

### Maintainers

| Role | Name | Contact |
|------|------|---------|
| Platform Lead | @platform-lead | platform-lead@chaufher.app |
| Security Lead | @security-lead | security@chaufher.app |
| DevOps On-Call | Rotating | devops-oncall@chaufher.app |

### Communication Channels

| Channel | Purpose | Response SLA |
|---------|---------|--------------|
| `#infra-support` (Slack) | General questions | 4 hours |
| `#infra-incidents` (Slack) | Active incidents | 15 minutes |
| devops@chaufher.app | Non-urgent requests | 24 hours |
| PagerDuty | P1/P2 incidents | 15 minutes |

### On-Call Rotation

- **Schedule**: Weekly rotation (Monday 9am UTC)
- **Coverage**: 24/7 for production
- **Escalation**: On-call → Platform Lead → Security Lead → CTO

### Escalation Path

1. **P3/P4** (Low/Medium): Post in `#infra-support`, expect response within 4 hours
2. **P2** (High): Page on-call via PagerDuty, expect response within 30 minutes
3. **P1** (Critical): Page on-call + Platform Lead, expect response within 15 minutes

### Incident Runbooks

See [docs/runbooks/](docs/runbooks/) for detailed procedures:
- [Incident Response](docs/runbooks/incident-response.md)
- [Escalation Procedures](docs/runbooks/escalation.md)
- [Communication Templates](docs/runbooks/communication.md)

---

## Operational Runbooks

### Quick Reference

| Scenario | Runbook | Command |
|----------|---------|---------|
| Drift Detection | [drift-detection.md](docs/runbooks/drift-detection.md) | `./scripts/drift-detection.sh --env prod` |
| Rollback | [rollback.md](docs/runbooks/rollback.md) | `./scripts/rollback.sh --env prod --version <tag>` |
| Failover Test | [failover.md](docs/runbooks/failover.md) | `./scripts/failover-test.sh --region eastus2` |
| Secret Rotation | [secret-rotation.md](docs/runbooks/secret-rotation.md) | `./scripts/rotate-secrets.sh --all` |
| Scale Out | [scaling.md](docs/runbooks/scaling.md) | `./scripts/scale.sh --env prod --tier P2v3` |

### Drift Detection

```bash
# Check for configuration drift
./scripts/drift-detection.sh --env prod

# Auto-remediate drift (with approval)
./scripts/drift-detection.sh --env prod --remediate

# Schedule drift check (via pipeline)
# Runs daily at 6am UTC, results posted to #infra-alerts
```

### Rollback Procedure

```bash
# List available rollback points
./scripts/rollback.sh --env prod --list

# Rollback to specific version
./scripts/rollback.sh --env prod --version v1.2.3

# Emergency rollback (skips non-critical validations)
./scripts/rollback.sh --env prod --version v1.2.3 --emergency
```

### Failover Testing

```bash
# Test failover to secondary region
./scripts/failover-test.sh --primary eastus --secondary westus2

# Validate DR readiness
./scripts/dr-validation.sh --env prod

# Full DR drill (scheduled quarterly)
./scripts/dr-drill.sh --env prod --notify-stakeholders
```

---

## Versioning & Release Notes

### Version Format

We follow [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes to infrastructure contracts
- **MINOR**: New features, backward-compatible
- **PATCH**: Bug fixes, security patches

### Bumping Versions

```bash
# Bump patch version (e.g., 1.2.3 → 1.2.4)
./scripts/bump-version.sh patch

# Bump minor version (e.g., 1.2.3 → 1.3.0)
./scripts/bump-version.sh minor

# Bump major version (e.g., 1.2.3 → 2.0.0)
./scripts/bump-version.sh major
```

### Release Tagging

Releases are tagged automatically on merge to `main`:

```bash
# Manual tagging (if needed)
git tag -a v1.2.3 -m "Release v1.2.3: Add Redis cache support"
git push origin v1.2.3
```

### Changelog

All changes are documented in [CHANGELOG.md](CHANGELOG.md):

```markdown
## [1.2.3] - 2024-01-15
### Added
- Redis cache module for session storage
### Fixed
- Key Vault access policy for staging environment
### Security
- Updated base images for container deployments
```

---

## Choreography with App Repos

### Cross-Repo Promotion Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ chaufher-app│     │chaufher-api │     │chaufher-infra│
│   (Mobile)  │     │  (Backend)  │     │   (Infra)    │
└──────┬──────┘     └──────┬──────┘     └──────┬───────┘
       │                   │                   │
       │ 1. PR merged      │                   │
       ├───────────────────┼──────────────────►│
       │                   │   2. Trigger      │
       │                   │   infra workflow  │
       │                   │                   │
       │                   │◄──────────────────┤
       │                   │ 3. Deploy new     │
       │                   │    resources      │
       │◄──────────────────┼───────────────────┤
       │ 4. Update env     │                   │
       │    variables      │                   │
```

### Triggering Infra from App PRs

Add to your app repo's workflow:

```yaml
# .github/workflows/trigger-infra.yml
on:
  push:
    branches: [main]
    paths:
      - 'infra-requirements.json'

jobs:
  trigger-infra:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger infra deployment
        uses: actions/github-script@v6
        with:
          github-token: ${{ secrets.CROSS_REPO_TOKEN }}
          script: |
            await github.rest.actions.createWorkflowDispatch({
              owner: 'phoenixvc',
              repo: 'chaufher-infra',
              workflow_id: 'deploy.yml',
              ref: 'main',
              inputs: {
                triggered_by: 'chaufher-app',
                environment: 'staging'
              }
            })
```

### CD Gating

Infra changes can gate app deployments:

```yaml
# In app repo deployment workflow
jobs:
  check-infra:
    runs-on: ubuntu-latest
    steps:
      - name: Verify infra version
        run: |
          REQUIRED_INFRA_VERSION=$(cat infra-requirements.json | jq -r '.version')
          CURRENT_INFRA_VERSION=$(curl -s https://api.github.com/repos/phoenixvc/chaufher-infra/releases/latest | jq -r '.tag_name')
          if [ "$REQUIRED_INFRA_VERSION" != "$CURRENT_INFRA_VERSION" ]; then
            echo "Infra version mismatch. Required: $REQUIRED_INFRA_VERSION, Current: $CURRENT_INFRA_VERSION"
            exit 1
          fi
```

### Environment Promotion

```bash
# Promote dev → staging
./scripts/promote.sh --from dev --to staging

# Promote staging → prod (requires approval)
./scripts/promote.sh --from staging --to prod --approve
```

---

## Accessibility & Docs

### Documentation Index

| Document | Location | Description |
|----------|----------|-------------|
| Architecture Diagrams | [docs/architecture/](docs/architecture/) | System and network diagrams (draw.io) |
| API Contracts | [docs/api/](docs/api/) | OpenAPI specs for infra-managed services |
| ADRs | [docs/adr/](docs/adr/) | Architecture Decision Records |
| Runbooks | [docs/runbooks/](docs/runbooks/) | Operational procedures |
| Policy Docs | [policies/](policies/) | Azure Policy definitions |

### Architecture Diagrams

Diagrams are maintained in draw.io format:
- `docs/architecture/network-topology.drawio`
- `docs/architecture/data-flow.drawio`
- `docs/architecture/dr-architecture.drawio`

To edit: Open in [draw.io](https://app.diagrams.net/) or VS Code with draw.io extension.

### Living Documentation

- **Confluence**: [ChaufHER Infra Space](https://chaufher.atlassian.net/wiki/spaces/INFRA)
- **Architecture Diagrams**: Updated on every major release
- **Runbooks**: Reviewed quarterly or after incidents

---

## Link to App Checklist

### Mobile Integration Checklist

See **[MOBILE_CHECKLIST.md](MOBILE_CHECKLIST.md)** for the complete mobile app integration guide.

#### Applicable Apps

| App | Repository | Mandatory Sections |
|-----|------------|-------------------|
| ChaufHER Driver | `chaufher-app` (driver module) | All sections |
| ChaufHER Rider | `chaufher-app` (rider module) | All sections |
| ChaufHER Admin | `chaufher-admin` | API Contract, Auth, Telemetry |

#### Mandatory Sections

- [ ] **API Contract**: Verify endpoint URLs and versioning
- [ ] **Authentication**: Configure Azure AD/B2C integration
- [ ] **SignalR**: Set up real-time connections with fallbacks
- [ ] **Telemetry**: Wire App Insights with correlation IDs
- [ ] **Feature Flags**: Integrate Azure App Configuration

#### Optional Sections

- Push Notifications (if applicable)
- Offline Mode (for mobile apps)
- Rate Limiting Configuration

---

## Product Overview

The ChaufHER Infra repository is a foundational component for the ChaufHER platform, delivering infrastructure as code (IaC) and DevOps automation tailored for Azure-native environments. Its primary mission is to ensure that all environments—development, staging, production—are managed securely, reliably, and with operational transparency. The infrastructure code codifies ChaufHER's cloud resources, governance, policies, and monitoring, enabling platform scalability and consistent engineering workflows. The repository supports reliable deployments, disaster recovery, and fast incident response, with a focus on risk reduction and regulatory compliance.

## Purpose

The main purpose of the ChaufHER Infra repository is to provide a centralized, version-controlled platform for the definition, deployment, and governance of the platform's Azure infrastructure and supporting DevOps automation. It solves several key challenges:

- Consistency and Repeatability: Ensures all environments are provisioned with the same standards, reducing drift and configuration errors.
- Security and Governance: Codifies secure defaults, resource policies, role-based access, and secrets management.
- Automation: Facilitates continuous integration and delivery, rapid rollback, and business continuity scripting.
- Operational Excellence: Dashboards and on-call tooling provide live insight into cost, health, and risk.

Example Scenario: When a new feature is developed, infrastructure for it is defined in the repo. Automated pipelines then provision and validate these resources, ensuring consistency and auditability.

## Target Audience

Primary:

- DevOps Engineers — utilize the repo daily to define, update, and manage infrastructure, build pipelines, and monitor system health.

Secondary:

- Product and Security Leadership — consult documentation for understanding risk exposure, governance posture, and compliance (e.g., DR setup, controls).
- New Engineers — rely on clear structure and documentation to onboard quickly, understand environment layouts, and adopt best practices.

Market Segments: Companies requiring reliable, scalable, secure Azure-native infrastructure, with strong operational compliance.

## Expected Outcomes

A well-structured infrastructure repo delivers:

- Reliability: High-uptime, low-incident environments, measured by system uptime and successful deployment rate.
- Predictable Costs: Accurate forecasting and control over cloud spend, tracked through cost dashboards and alerting.
- Security and Compliance: Fewer security events, policy violations, and faster remediation times.
- Operational Agility: Rapid rollback and environment promotion for new releases, enhancing delivery velocity.
- Incident Response Readiness: Incident runbooks and on-call automations that meet agreed response KPIs.

### Key KPIs

| KPI | Target Value | Measurement Method |
|---|---:|---|
| Uptime | 99.9%+ | Azure Monitor, status dashboards |
| Time to Rollback | < 15 minutes | Pipeline logs, incident reports |
| Cost Overruns | < 5% deviation/month | Azure Cost Management dashboards |
| Security Incident SLA | < 30 min response | Incident/alerting logs |

## Design Details

The repo is organized by logical infrastructure domains, environment, and reusable modules. Major components include:

- IaC Modules: Written in Bicep, Terraform, and ARM templates; organized for key resources (App Services, Key Vault, networking, etc.).
- Reusable Deployment Modules: Parameterized for environment, region, and scale; standardized for repeatable application.
- Deployment Scripts: PowerShell/Bash scripts for orchestration and automation beyond declarative IaC.
- Shared YAML Pipelines: Reusable pipeline templates supporting PR validation, plan/apply, smoke testing, cost checks.
- Dashboards: Azure dashboards for monitoring health, costs, deployments, and audit events.

### Environment Separation Pattern

Repository structure enforces strict separation between dev, staging, and prod. Each environment has dedicated resource groups, key vaults, and identity resources, managed via parameterized modules to ensure configuration drift detection and rapid rollback.

## Architectural Overview

### Infra Data Flow Overview

1. Pull Request triggers IaC validation pipeline (static lint, policy checks).
2. Upon approval, CI Pipeline stages "plan" outputs for code reviewers and leads.
3. Gated Approval invokes deployment pipeline to "apply" changes to Azure.
4. Pipeline updates role assignments, secrets, and triggers notification webhooks for application teams.
5. Dashboards updated post-deployment; cost and policy reporting feed into central audit logs.
6. Incident Runbooks: On detection of incident or drift, auto-escalation scripts execute rollback or remediation.

### Key Modules

- Azure App Service
- Azure Key Vault (secrets rotation)
- Azure CDN and Static Web Apps
- Azure Monitoring (alerts, health checks)
- Azure DevOps (pipeline orchestration)

### Boundaries

PR → Pipeline → Azure Resource Manager → Azure Services → Notification/Monitoring Layers

## Data Structures and Algorithms

- IaC Constructs: Modules (Bicep/Terraform), parameters/variables (region, SKU, secrets refs), and outputs (resource URIs, connection strings).
- Naming Conventions: All resources and modules follow a strict {project}-{env}-{service}-{region} format for easy tracking and reporting.
- Drift Detection: Scheduled pipeline jobs and Azure Policy audits detect configuration drift; changes flagged for review or automatic correction.
- Auto-Promotion Scripts: Successful deployments in lower environments can trigger safe promotion to higher environments, reducing human error.
- Script Abstractions: Shared scripts encapsulate recurring operational tasks (e.g., credential rotation, staged rollout, post-deployment cleanup).

## System Interfaces

The infra repository defines and manages:

- Azure App Service — hosts chaufher.api and related backend services.
- Azure Static Web Apps — delivers client-facing web applications.
- Azure Key Vault — centralized secrets management and policy enforcement.
- Azure CDN — distributes static assets with low latency.
- Azure Monitor/Log Analytics — aggregates logs, events, and exposures for operations.

All interfaces leverage Azure-native protocols, REST APIs, and RBAC.

## User Interfaces

- Engineering Ops Dashboards: Real-time views of deployment status, resource health, and current incidents.
- Status Boards: Live service status, environment uptime, policy compliance.
- Cost and Utilization Dashboards: Tracks forecasts, actuals, and overages.
- Audit Logs: Detailed, immutable logs of resource changes and deployments.
- Incident Management Tooling: On-call notification surfaces (Slack/MS Teams), runbook invocation panels, escalation hotlinks.

## Hardware Interfaces

- Cloud-Only Model: No on-premises; all resources reside in Microsoft Azure.
- Zones/Regions: Resources are geo-distributed across primary/secondary Azure regions for high availability and DR.
- Cloud Network Boundaries: Strict API-based communication between chaufher product assets and supporting infrastructure (firewall, private endpoints).
- Geo-Redundancy: Core services deployed across zones to ensure resiliency; storage/data replication policies enforced via IaC.

## Testing Plan

Cadence: Every PR invokes static infrastructure validation, policy checks, and environment dry runs.

### Policy-as-Code & Smoke Testing

- Azure Policies and OPA gate key workflows; non-compliant PRs are blocked.
- After deployment, synthetic probes validate newly provisioned resources.

### Incident/Failover Drills

- Regular tabletop and automated run-throughs of recovery scenarios and rollback mechanisms.

### Environment Parity Validation

- Automated checks to ensure dev/staging/prod are consistent.

## Test Strategies and Tools

- Plan-and-Destroy: Ensures all create/destroy operations are idempotent and reversible.
- Policy-Gated PRs: Infrastructure changes must pass compliance, cost, and secret/storage-policy checks.
- Environment Diffing: Automated diffs alert the team to divergence between intended state and reality.
- Cost/Secret Regression Tests: Prevent accidental cost spikes or credential leaks.
- Cross-AZ/Region Testing: DR and failover scenarios are validated across all supported regions.

### Testing Tools

- Linting: tflint, bicep lint, checkov — enforce code quality and security standards.
- Security Scanning: Trivy, Azure Defender for Cloud — scan images/templates for vulnerabilities.
- Pipeline Integration: GitHub Actions, Azure DevOps Pipelines — automate test execution, status notifications.

## Testing Environments

- Segregation: Each environment (dev/staging/prod) utilizes isolated resource groups, separate credentials, and unique Key Vaults.
- Ephemeral Environments: PR-specific, spun up for complex tests and destroyed on merge/close.
- Secure Data Handling: Non-production environments use scrubbed or synthetic data; secrets are never shared across environments.
- DR Scripts: Deployed and validated in all environments; periodic failover and recovery runs ensure readiness.

## Test Cases

| Test Case | Trigger | Expected Outcome |
|---|---|---|
| PR triggers correct pipeline | PR creation | Linting, policy check, plan jobs run |
| Drift detection | Scheduled/Manual | Drift identified/flagged; correction suggested |
| Cost regression blocked | Pipeline run | Unsafe cost delta halts merge/pipeline |
| Incident runbook escalation | Fake incident/test event | On-call paged; rollback/runbook triggered |
| Secrets rotation test | Pipeline/manual | New secrets issued, audit log updated |
| Cross-region DR drill | Scheduled/Manual | Environment promoted/failover to secondary |

## Reporting and Metrics

- Cost Dashboards: Real-time cloud spend monitoring with variance alerts and deep dives into service consumption.
- Incident Reporting: Integrated with ticketing and alert systems; mean time to detection/resolution tracked.
- Policy Drift/Violation Logs: Comprehensive logs of non-compliant resources and policy exceptions.
- Pipeline Health: Success/failure rates for deployments, test coverage stats.
- Uptime and Audit Data: Live and historical reporting of system availability and critical changes.

Reporting is surfaced via Azure Dashboards, Power BI, and custom notification channels.

## Deployment Plan and Environment

### Pipeline Flow

1. Code Review: PR triggers plan and compliance check.
2. Approval: Lead/peer approval required before apply.
3. Apply: Gated pipeline applies changes to target environment.
4. Staged Rollout: For high-impact resources, apply changes in waves.
5. Validation: Post-apply scripts and dashboards confirm resource/state correctness.
6. Rollback Guidance: Automated reversion using previous pipeline state or manual approval for destructive rollbacks.
7. Environment Promotion: Successful lower-environment changes can be promoted upward with minimal manual intervention.

### Deployment Environment

- Azure Account Hierarchy: Production, staging, and development use separate subscriptions or management groups for strong least-privilege and cost controls.
- Resource Groups: Each domain (networking, compute, data) is scoped to environment-label resource groups.
- Key Vault: Dedicated vault per environment; access restricted by environment and role.
- Access Control Tiers: Contributor/Reader/Owner roles mapped to repo workflow stages.
- High Availability: Critical infra deployed in multiple zones/regions; failover resources provisioned and tested.
- Disaster Recovery: Environments are DR-ready, with DR scripts and runbooks validated regularly.

## Deployment Tools

- IaC Frameworks: Bicep (primary), with Terraform and ARM templates as needed for existing assets.
- Pipeline Orchestration: GitHub Actions as the single orchestrator for build, test, environment promotion, and deploy.
- Shared Scripts: Library of PowerShell/Bash scripts for custom orchestration and admin tasks.
- Admin Dashboards: For real-time oversight, validation, and incident management.

### Deployment Steps

1. Developer opens PR for infra.
2. Plan/lint/test pipeline runs; reports to reviewers.
3. Reviewers approve or request changes.
4. Merge on approval; gated "apply" pipeline runs in target environment.
5. For high-risk resources, changes staged with hold points and health checks.
6. Upon success, dashboards and stakeholders notified.
7. If error detected, hooks trigger auto-rollback or guided manual intervention.
8. DR and post-deploy validation runbooks invoked where applicable.

## Post-Deployment Verification

- Resource Existence: Automated checks confirm all expected resources exist and are properly labeled/tagged.
- Secrets Validation: Pipeline verifies references, access policies, and rotation policies are set.
- Monitoring Hooks: Resource health alerts, deployment success/failure are checked and logged.
- Policy Compliance: Reports generated for policy compliance and role assignments.
- Incident Drills: Major post-apply events trigger simulated incident responses to validate operational readiness.

## Continuous Deployment

ChaufHER utilizes true continuous deployment via:

- Shared Pipelines: Modular, reusable YAML pipelines support multi-repo and multi-environment triggers.
- Drift Detection: Automated jobs keep infrastructure aligned with code; remediations scheduled or executed with approval.
- Incident Response Automation: Key runbooks auto-trigger on drift, failed rollouts, or detected incidents.
- Rapid Rollback & Promotion: Automated tools for rollback/redeploy and safe promotion across all stages, reducing downtime and risk.

Benefits: Accelerated delivery, reduced human error, continuous compliance, and seamless operational scaling. Environments adapt fluidly to code changes, ensuring alignment between business needs and technical reality.

## App Integration & Alignment

This repo defines the Azure infrastructure and DevOps workflows that the ChaufHER mobile apps (chaufher-app) depend on. Below are the key integration points and alignment requirements to ensure the mobile app and infra remain compatible:

- API Contract & Versioning: The backend exposes an OpenAPI/REST surface (and SignalR endpoints for real-time events). App teams must use the documented base path, supported version, and contract; infra publishes/hosts the spec and supports backward-compatible changes with major-version policies.
- Authentication: App uses JWT-based auth (or Azure AD/B2C). Infra provides per-environment identity resources (service principals, app registrations), Key Vault secrets for signing keys, and rotation policies.
- SignalR / WebSockets: The infra provides SignalR endpoints and TLS termination. App must use the configured domain, ping/keepalive settings, and fallback paths (HTTP polling or offline queuing) where network reliability is a concern.
- Push Notifications: FCM/APNs credentials and routing are stored in Key Vault; infra provides notification topics and AC/roles for apps to register device tokens securely.
- Secrets & Config: App sensitive secrets (API keys, webhook tokens) are stored in Key Vaults per environment, and repos and CI pipelines fetch them securely; never commit secrets to source.
- Key Vault Access: The app's backend service principal must have least-privilege access to Key Vault secrets; the infra enforces RBAC policies and scoped access via managed identities.
- Telemetry & Monitoring: App instrumentation expects App Insights/Sentry plus Log Analytics. Infra provides the telemetry resources and access policies; apps should emit trace IDs and correlation metadata to link logs across infra and app layers.
- Feature Flags & Config Store: Shared config (Azure App Configuration/Feature Flags) supported by infra; apps honor these runtime toggles for staged rollouts.
- Rate Limits & Fallbacks: Safety event handling requires robust offline and fallback behavior (SMS, local cache); infra houses SMS providers (e.g., Twilio numbers) and telemetry for fallback success/failure rates.
- Environment Parity: Infra provides per-environment sandboxes (dev/staging/prod) for the app; apps should default to dev/staging endpoints and use ephemeral PR environments when available.
- CI/CD Integration: Mobile pipeline workflows should use infra-managed artifacts (service principal credentials, vault secrets, API endpoints) with gated approvals for production. Cross-repo triggers for safe promotions must be aligned and documented.
- Naming & Tagging: Apps must adhere to resource naming conventions and tagging for costs and auditability; infra enforces naming conventions and resource policies per environment.
- DR & Incident Workflows: App safety and panic flows must map to infra incident runbooks; apps surface minimal data while the backend and infra handle escalation, dispatching, and logging.

For mobile engineers, see [MOBILE_CHECKLIST.md](MOBILE_CHECKLIST.md) for a dedicated checklist, environment variables, and example snippets.
