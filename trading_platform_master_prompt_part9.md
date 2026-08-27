# TRADING PLATFORM MASTER PROMPT
# PART 9 — CLOUD INFRASTRUCTURE, DEVOPS, CONTAINERS, KUBERNETES, CI/CD, SECRETS, OBSERVABILITY, BACKUP, DISASTER RECOVERY & PRODUCTION OPERATIONS

CONTINUATION OF PARTS 1–8

This part defines the production infrastructure and operational engineering requirements for the platform.

CORE OBJECTIVE:
Build an infrastructure foundation that is:
- secure
- reproducible
- observable
- resilient
- auditable
- scalable
- cost-aware
- provider-agnostic where practical
- capable of controlled live financial operations
- capable of rapid rollback
- capable of safe degradation

Never design infrastructure around the assumption that every dependency will remain healthy.

---

# 9.0 INFRASTRUCTURE NORTH STAR

The platform must be deployable through version-controlled infrastructure and repeatable automation.

Infrastructure must not depend on:
- undocumented manual changes
- one engineer's laptop
- untracked credentials
- ad-hoc SSH
- production-only configuration
- fragile scripts
- undocumented server state

Desired principle:

CODE
→ REVIEW
→ BUILD
→ TEST
→ SIGN
→ DEPLOY
→ VERIFY
→ OBSERVE
→ ROLLBACK IF NEEDED

---

# 9.1 CLOUD STRATEGY

Select the primary cloud only after evaluating:

- geographic availability
- managed database quality
- Kubernetes/container support
- secret management
- IAM maturity
- observability
- CDN/WAF
- object storage
- queue/stream options
- AI integrations
- regional data requirements
- cost
- operational team skill
- vendor lock-in risk

Candidate providers may include:
AWS
Google Cloud
Microsoft Azure

The final selection must be documented in an Architecture Decision Record.

---

# 9.2 MULTI-CLOUD POLICY

Do not build a complex multi-cloud architecture prematurely.

Initial principle:

ONE PRIMARY CLOUD
+
CLOUD-AGNOSTIC APPLICATION LAYERS
+
PORTABLE DATA EXPORTS
+
DOCUMENTED EXIT PATH

Multi-cloud becomes justified only when:
- resilience requirements
- regulatory requirements
- commercial requirements
- provider availability
- strategic scale

justify the additional complexity.

---

# 9.3 REGION STRATEGY

Define:

PRIMARY_REGION
SECONDARY_REGION
BACKUP_REGION

For each major region document:
- services available
- data residency
- failover role
- compliance constraints
- latency
- backup location

Never assume data may legally move across every region.

---

# 9.4 AVAILABILITY ZONES

Production services should use multiple availability zones when the cloud/provider and workload justify it.

At minimum, avoid a single infrastructure failure taking down:
- API
- database
- queue
- secrets
- public website

---

# 9.5 NETWORK ARCHITECTURE

Conceptual structure:

Internet
→ CDN/WAF
→ Load Balancer
→ Public Gateway
→ Private Application Network
→ Internal Services
→ Data Layer

Do not expose:
database
cache
queue
secret manager
internal service ports

directly to the public internet.

---

# 9.6 NETWORK SEGMENTATION

Recommended logical segments:

EDGE
APPLICATION
EXECUTION
DATA
ADMIN
MONITORING

The EXECUTION segment is particularly sensitive.

---

# 9.7 PRIVATE NETWORK PRINCIPLE

Internal financial services communicate over private network paths where available.

Authentication remains required even inside private networks.

Do not equate:
“private network”
with
“trusted network.”

---

# 9.8 FIREWALL POLICY

Default deny.

Allow only:
- required inbound
- required outbound
- required service-to-service

Document every production firewall rule.

---

# 9.9 EGRESS CONTROL

Restrict outbound connectivity from sensitive services.

Execution service may communicate only with:
- approved provider endpoints
- approved observability endpoints
- approved internal services

Do not grant unrestricted internet access to:
- execution
- secrets
- admin backend
- database

unless explicitly justified.

---

# 9.10 DNS

Use:
public DNS
private DNS
service discovery

Public records:
website
API
status
docs

Private records:
internal services

Use DNSSEC where appropriate and supported.

---

# 9.11 CDN

Use CDN for:
static assets
images
public blog assets
documentation assets

Do not cache:
private financial responses
authorization-dependent data
order states
customer secrets

without an explicit secure design.

---

# 9.12 WAF

Protect:
public website
API gateway
authentication endpoints
webhooks

Controls may include:
rate limits
bot mitigation
request-size limits
known exploit signatures
geographic controls where lawful

Avoid overblocking legitimate clients.

---

# 9.13 LOAD BALANCING

External:
HTTPS load balancer

Internal:
service/load balancing mechanism

Health checks must be application-aware.

A process being reachable does not mean it is healthy.

---

# 9.14 TLS

Use TLS for all external communications.

Where practical:
service-to-service encryption

Certificate lifecycle:
- automated issuance
- automated renewal
- monitoring
- expiry alerting

---

# 9.15 HSTS

Enable strict transport security for production domains after confirming all required subdomains and operational dependencies.

---

# 9.16 HTTP SECURITY

Use modern security headers in the web stack.

At minimum evaluate:
HSTS
CSP
X-Content-Type-Options
Referrer-Policy
Permissions-Policy
frame protections

Configuration must match application requirements.

---

# 9.17 CONTAINERIZATION

Containerize backend services consistently.

Every production image should have:
- version
- immutable digest
- minimal base
- non-root user where feasible
- health endpoint
- graceful shutdown
- resource limits
- vulnerability scan

---

# 9.18 CONTAINER IMAGE PRINCIPLES

Avoid:
- shell-heavy images
- unnecessary packages
- compilers in runtime images
- package managers in production image
- embedded secrets

Prefer multi-stage builds.

---

# 9.19 IMAGE TAGGING

Use:
service:version

Also record immutable digest.

Never use:
latest

as a production deployment reference.

---

# 9.20 SOFTWARE BILL OF MATERIALS

Generate SBOM for production artifacts.

Track:
package
version
license
source
digest

Store SBOM with release metadata.

---

# 9.21 IMAGE SIGNING

Sign production images when supported.

Deployment policy may require:
trusted registry
trusted signer
approved provenance

Reject unknown artifacts.

---

# 9.22 RUNTIME SECURITY

Containers should:
- drop unnecessary Linux capabilities
- use read-only filesystem where feasible
- run as non-root
- restrict privilege escalation
- use seccomp/AppArmor or equivalent where appropriate

---

# 9.23 KUBERNETES STRATEGY

Kubernetes is optional.

Use it only if:
- workload complexity
- scaling
- service count
- deployment maturity
- operational team

justify it.

Do not adopt Kubernetes merely because it is fashionable.

---

# 9.24 IF KUBERNETES IS USED

Separate:
namespace
service account
secrets integration
network policy
resource policy
deployment policy

Suggested namespaces:

platform
api
trading
data
observability
admin
jobs

---

# 9.25 KUBERNETES RESOURCE LIMITS

Every workload:
requests
limits

Do not allow one service to consume the entire node.

---

# 9.26 HORIZONTAL AUTOSCALING

Autoscale stateless services based on:
CPU
memory
request rate
queue depth
custom metrics

Do not blindly autoscale execution without provider-rate-limit awareness.

---

# 9.27 EXECUTION SERVICE SCALING

Scale execution workers according to:
provider capacity
account distribution
queue depth
latency

Avoid creating thousands of simultaneous provider requests merely because CPU is available.

---

# 9.28 DATABASE ARCHITECTURE

Use managed relational database where practical.

Requirements:
HA
backup
point-in-time recovery
encryption
replication
monitoring
maintenance policy

---

# 9.29 DATABASE CONNECTION POOLING

Use controlled pools.

Do not allow every pod to open a large unbounded number of connections.

Monitor:
active
idle
wait time
timeouts

---

# 9.30 DATABASE READ REPLICAS

Use read replicas for:
analytics
heavy read workloads

Do not use stale replicas for:
financial authorization
risk
entitlement
execution state

Critical writes/read-after-write semantics require authoritative storage.

---

# 9.31 DATABASE FAILOVER

Document:
trigger
detection
failover
DNS/connection handling
application behavior
reconciliation
verification

After failover:
run integrity checks.

---

# 9.32 DATABASE MIGRATIONS

Use migration tooling.

Every migration:
- versioned
- reviewed
- tested
- reversible where possible
- backward-compatible during deployment

---

# 9.33 DANGEROUS MIGRATIONS

Do not combine:
destructive schema change
+
application deployment
+
large data rewrite

in a single untested release.

---

# 9.34 BACKUP STRATEGY

Back up:
database
critical object storage
configuration
audit records as appropriate
operational metadata

Backups:
encrypted
access-controlled
versioned
tested

---

# 9.35 POINT-IN-TIME RECOVERY

Enable for transactional database where supported.

Test restoration.

A backup that has never been restored is an assumption, not a proven backup.

---

# 9.36 BACKUP ISOLATION

Backups should be protected from production compromise.

Use:
separate credentials
separate access policy
immutable/locked retention where appropriate

---

# 9.37 RESTORE TEST

Periodic exercise:

select backup
restore isolated instance
run integrity checks
validate application
measure RTO

Document result.

---

# 9.38 OBJECT STORAGE

Use for:
images
blog media
exports
documents

Separate buckets/containers by sensitivity.

Examples:
public-assets
private-documents
customer-exports
audit-archive

---

# 9.39 OBJECT STORAGE SECURITY

Use:
private by default
short-lived signed URLs
encryption
lifecycle rules
access logs

Public bucket access only for intentionally public content.

---

# 9.40 EXPORT LINKS

Exports should:
expire
be scoped
require authorization

Do not create permanent public URLs for customer financial data.

---

# 9.41 CACHE

Use a managed cache only where needed.

Potential:
Redis or equivalent.

Never let cache become the source of truth for:
payment
entitlement
risk
order state

---

# 9.42 QUEUE / STREAM

Choose message infrastructure based on:
ordering
durability
throughput
replay
consumer model

Potential:
managed queues
Kafka-compatible stream
cloud pub/sub

The product must not depend on a specific vendor before the decision is documented.

---

# 9.43 JOB SYSTEM

Background jobs:
news ingestion
AI analysis
notifications
reconciliation
exports
reports

Every job:
jobId
type
attempt
status
createdAt
startedAt
finishedAt
error

---

# 9.44 JOB RETRIES

Retry only transient failures.

Each retry:
backoff
jitter
maximum attempts

Financial mutations require idempotency.

---

# 9.45 CRON JOBS

Do not rely on server-local cron in production.

Use:
managed scheduler
Kubernetes CronJob
workflow scheduler
or equivalent

Jobs must be observable.

---

# 9.46 SECRET MANAGEMENT

Use cloud secret manager or equivalent dedicated secret-management infrastructure.

Store:
application secrets
provider credentials
webhook signing secrets
database passwords
JWT signing secrets
third-party API keys

Never store in:
Git
Docker image
frontend bundle
public CI logs
ordinary DB tables

---

# 9.47 SECRET ACCESS

Secret retrieval:
identity-based
short-lived where possible
least privilege
audited

---

# 9.48 CUSTOMER EXCHANGE SECRETS

Architecture:

Customer UI
→ secure backend endpoint
→ secret manager
→ connection reference

Only the minimum service receives secret material when necessary.

Admin:
NO RAW SECRET ACCESS.

---

# 9.49 SECRET ROTATION

Infrastructure secrets:
automated where possible.

Provider/customer secrets:
support replacement workflow.

After rotation:
validate
switch
audit

---

# 9.50 SECRET LEAK RESPONSE

If a secret leak is suspected:

1 identify credential
2 block use where feasible
3 revoke/rotate
4 invalidate sessions if relevant
5 preserve forensic evidence
6 assess scope
7 notify affected parties where required
8 document

---

# 9.51 IAM

Create distinct identities for:
developer
CI/CD
runtime
operations
security
billing
database migration

No universal superuser credential.

---

# 9.52 BREAK-GLASS IAM

Emergency elevation:
- MFA
- reason
- approval where possible
- time limit
- audit
- automatic expiration

---

# 9.53 SERVICE ACCOUNTS

Each service has its own identity.

Examples:
api-service
risk-service
execution-service
reconciliation-service
notification-service
admin-service

---

# 9.54 IAM PERMISSION MATRIX

Define:

who
can
do
what
on which resource
under which condition

Example:

execution-service
READ secret
connection X
purpose EXECUTION

support-service
DENY secret

---

# 9.55 CI/CD IDENTITY

CI should use short-lived workload credentials where the platform supports them.

Avoid long-lived cloud keys in CI variables.

---

# 9.56 ENVIRONMENT ISOLATION

DEV
TEST
STAGING
PRODUCTION

Separate:
credentials
databases
queues
storage
cloud projects/accounts where practical

Never point development at production secrets.

---

# 9.57 STAGING

Staging should resemble production enough to detect deployment failures.

But:
no real customer secrets
no uncontrolled live trading
no real payment settlement unless explicitly isolated

---

# 9.58 PRODUCTION ACCESS

Production access is:
role-based
logged
limited
reviewed

Developer access to production should be minimal.

---

# 9.59 SOURCE CONTROL

Use:
protected main branch
code review
required checks
signed commits where appropriate
branch protection

No direct push to production branch by default.

---

# 9.60 CODE OWNERSHIP

Sensitive directories require designated reviewers.

Examples:
security
auth
payments
execution
risk
infrastructure
compliance

---

# 9.61 CI PIPELINE

Required stages:

format
lint
type-check
unit-test
integration-test
security-scan
dependency-scan
secret-scan
build
SBOM
artifact-sign
E2E
accessibility
localization
migration-test

---

# 9.62 SECURITY GATES

Block release on:
critical vulnerability
known exploitable high severity where policy requires
secret detection
failed authorization tests
failed dependency policy
invalid artifact signature

---

# 9.63 DEPENDENCY SCANNING

Track:
direct dependencies
transitive dependencies
license
vulnerability
fix availability

---

# 9.64 LICENSE COMPLIANCE

Maintain inventory of open-source licenses.

Legal review for:
copyleft
commercial restrictions
attribution
redistribution

---

# 9.65 SECRET SCANNING

Scan:
source
commits
artifacts
container layers

A detected secret should trigger:
block
rotate if real
investigation

---

# 9.66 STATIC ANALYSIS

Use SAST for:
backend
frontend
mobile
infrastructure code

Rules tuned to language/framework.

---

# 9.67 DYNAMIC TESTING

Use DAST for public application staging.

Include:
authentication
authorization
input validation
headers
sessions
API abuse

---

# 9.68 MOBILE BUILD PIPELINE

Android:
build
sign
test
artifact verify

iOS:
build
sign
test
archive

Admin app:
separate signing identity
separate deployment track

---

# 9.69 SIGNING KEYS

Store:
Android keystore
Apple signing credentials
desktop signing certificates

in secure signing infrastructure.

Never in Git.

---

# 9.70 RELEASE ARTIFACTS

Each artifact records:
version
commit
build ID
toolchain
dependency lock
SBOM
signature
release notes

---

# 9.71 DEPLOYMENT STRATEGIES

Possible:
rolling
blue/green
canary

For financial services:
canary is preferred for high-risk execution changes where architecture permits.

---

# 9.72 CANARY

Canary may restrict:
traffic percentage
customers
region
strategy
provider
market mode

Observe before expanding.

---

# 9.73 FEATURE FLAGS

Use feature flags for:
new UI
new signal logic
new provider
new AI model
new admin feature

High-risk flags require:
approval
audit
rollback

---

# 9.74 DATABASE DEPLOYMENT ORDER

Preferred:
1 backward-compatible migration
2 deploy application
3 enable feature
4 migrate data
5 remove legacy later

---

# 9.75 ROLLBACK

Every deployment:
has rollback plan.

For database changes:
ensure compatibility with previous app version where possible.

---

# 9.76 ROLLBACK TRIGGER

Triggers may include:
error spike
latency spike
authorization errors
order anomalies
reconciliation mismatch
payment mismatch
security alert

---

# 9.77 TRADING-SPECIFIC RELEASE FREEZE

If critical trading incident occurs:
freeze unrelated trading-core releases.

Stabilize first.

---

# 9.78 STATUS PAGE

Show service categories:

Website
API
Dashboard
Signals
Automation
Payments
Mobile
Notifications

Do not publish security-sensitive internals.

---

# 9.79 OBSERVABILITY STACK

Components:
logs
metrics
traces
alerts
dashboards
synthetic checks
incident tooling

---

# 9.80 LOG AGGREGATION

Use centralized structured logs.

Fields:
timestamp
service
environment
release
requestId
correlationId
severity
event
duration
safe actor/resource identifiers

---

# 9.81 LOG RETENTION

Retention varies by:
security
operations
legal
privacy

Do not retain sensitive logs forever.

---

# 9.82 LOG REDACTION

Automatically redact:
password
token
secret
authorization
private key
payment data

Test redaction with fixtures.

---

# 9.83 METRICS

Platform:
availability
latency
error rate

Trading:
signal rate
risk rejection
order submission
fill
unknown
reconciliation

Payment:
webhook lag
activation lag
refund

AI:
latency
timeout
cost
schema failures

---

# 9.84 DISTRIBUTED TRACING

Trace:
API
entitlement
policy
strategy
AI
risk
execution
provider
reconciliation

Retain enough context to reconstruct a consequential event.

---

# 9.85 ALERT SEVERITY

SEV0:
catastrophic/security-critical financial impact

SEV1:
major production impact

SEV2:
significant degradation

SEV3:
minor

SEV4:
informational

---

# 9.86 ALERT FATIGUE

Do not alert on every minor event.

Alerts should:
be actionable
have owner
have runbook
have severity

---

# 9.87 ON-CALL

Define:
owner
backup
escalation
contact
runbook

Especially for:
execution
payments
security
database

---

# 9.88 RUNBOOKS

Required runbooks:
provider outage
database failover
secret compromise
payment mismatch
unknown orders
reconciliation backlog
AI provider outage
global pause
rollback
restore
DDoS
security incident

---

# 9.89 INCIDENT COMMAND

During SEV0/SEV1:
incident commander
technical lead
communications lead
operations lead

Clear roles prevent chaotic response.

---

# 9.90 DISASTER RECOVERY

Define for each service:

RTO
RPO
dependency list
backup
restore
failover
validation
owner

---

# 9.91 RECOVERY TIERS

TIER 0:
identity / control-plane critical

TIER 1:
financial state / execution controls

TIER 2:
customer application

TIER 3:
content/blog

TIER 4:
analytics/non-critical

---

# 9.92 DR SCENARIOS

Test:
single node loss
zone loss
database failure
region impairment
queue failure
secret manager outage
provider outage
bad deployment
data corruption

---

# 9.93 FINANCIAL SAFETY DURING DISASTER

If critical control unavailable:
NO_NEW_EXECUTION

Preserve:
existing state
audit
evidence

Do not continue live execution with missing safeguards.

---

# 9.94 REGION FAILOVER

Region failover must account for:
market state
orders
positions
provider sessions
idempotency
event ordering

Never activate secondary region without reconciliation.

---

# 9.95 ACTIVE-PASSIVE VS ACTIVE-ACTIVE

Initial recommendation:
active-passive for execution control
active-active for public read layers where justified

Choose based on:
complexity
consistency
RTO
cost

---

# 9.96 DATA CONSISTENCY

For financial state:
correctness over availability.

A temporary read-only state is preferable to inconsistent execution.

---

# 9.97 CLOCK SYNCHRONIZATION

All servers/services use reliable time synchronization.

Financial events use server timestamps.

---

# 9.98 NTP MONITORING

Alert on excessive clock drift.

---

# 9.99 CLOUD COST GOVERNANCE

Track by:
service
environment
provider
customer workload
AI
market data
storage
egress

---

# 9.100 BUDGET ALERTS

Set:
monthly budget
forecast threshold
unexpected-spike alert

Do not let AI cost or market-data egress grow invisibly.

---

# 9.101 RESOURCE TAGGING

Every cloud resource:
environment
owner
service
cost center
data sensitivity
lifecycle

---

# 9.102 AUTO-CLEANUP

Non-production:
temporary databases
preview environments
test buckets
old images

should have lifecycle cleanup.

Never auto-delete production evidence.

---

# 9.103 OBSERVABILITY DATA PRIVACY

Logs/traces/metrics may contain sensitive references.

Apply:
redaction
access control
retention

---

# 9.104 CUSTOMER DATA LOCATION

Document where:
account data
payment records
financial state
documents
logs
analytics

are stored.

---

# 9.105 KYC STORAGE

If KYC is required:
use segregated storage and access control.

Do not replicate sensitive identity documents unnecessarily.

---

# 9.106 ADMIN NETWORK

Admin application APIs may use:
additional WAF rules
device controls
mTLS where appropriate
strict rate limits

---

# 9.107 ADMIN IP POLICY

An IP allowlist may be used for privileged operational environments where practical.

Do not make it the only security control.

---

# 9.108 PRIVILEGED SESSION MONITORING

Track:
admin session
device
actions
resource
duration
risk

---

# 9.109 PRODUCTION COMMAND LOGGING

Every production command:
actor
command
target
reason
result
timestamp

---

# 9.110 KILL SWITCH ARCHITECTURE

Controls:

GLOBAL_AUTOMATION_PAUSE
PROVIDER_PAUSE
STRATEGY_PAUSE
MARKET_PAUSE
CUSTOMER_PAUSE

Each is:
versioned
audited
server-enforced

---

# 9.111 KILL SWITCH AVAILABILITY

The kill-switch mechanism should remain operable during partial service failure.

Consider an independent control path for critical operations.

---

# 9.112 KILL SWITCH SECURITY

Require:
privileged identity
strong authentication
audit
confirmation

---

# 9.113 RECOVERY FROM KILL SWITCH

Cannot resume until:
health
policy
risk
reconciliation

checks pass.

---

# 9.114 BLUE/GREEN

For public application:
blue/green may reduce deployment risk.

For execution:
prefer:
canary
scoped rollout
pause

---

# 9.115 CANARY DATA ISOLATION

Canary must not accidentally share:
strategy state
feature flags
secrets
provider credentials

beyond intended scope.

---

# 9.116 MIGRATION BACKOUT

For irreversible migrations:
use compensating migration, not fantasy rollback.

Test migration on production-like dataset.

---

# 9.117 DEPLOYMENT APPROVAL

Production requires:
automated checks
owner approval
risk review for financial-core changes

---

# 9.118 CHANGE MANAGEMENT

Change types:
LOW
MEDIUM
HIGH
CRITICAL

Trading execution/risk changes:
HIGH or CRITICAL.

---

# 9.119 CHANGE EVIDENCE

Record:
what changed
why
who
build
tests
risk
rollback
approval

---

# 9.120 POST-DEPLOY VALIDATION

After deployment:
health
latency
errors
business metrics
security metrics
trading metrics

Run smoke tests.

---

# 9.121 SYNTHETIC MONITORING

Monitor:
homepage
login
dashboard
pricing
checkout test path where available
health endpoint
status page

Never execute real trades through synthetic monitoring.

---

# 9.122 SANDBOX TRADING MONITOR

Use provider sandbox where possible.

Test:
authentication
market data
order
cancel
reconcile

---

# 9.123 PRODUCTION READ-ONLY CHECK

A safe production diagnostic may:
read
validate
reconcile

without creating orders.

---

# 9.124 REAL MONEY TESTING

Do not use customer production accounts for system tests.

If production verification is necessary:
use controlled internal account
minimal risk
explicit approval
audit

---

# 9.125 OPERATIONS DASHBOARD

Must show:

GLOBAL STATUS
SERVICE HEALTH
PROVIDER HEALTH
QUEUE HEALTH
DATABASE HEALTH
MARKET DATA
EXECUTION
RECONCILIATION
PAYMENT
AI
SECURITY
INCIDENTS

---

# 9.126 EXECUTION OPERATIONS DASHBOARD

Show:
orders/min
success rate
rejects
unknown
latency
provider health
reconciliation backlog
risk rejects

---

# 9.127 RECONCILIATION OPERATIONS DASHBOARD

Show:
open cases
age
severity
provider
entity type
resolution status

---

# 9.128 SECURITY OPERATIONS DASHBOARD

Show:
failed auth
admin anomaly
secret-access anomaly
privilege changes
rate-limit spikes
suspicious API behavior

---

# 9.129 PAYMENT OPERATIONS DASHBOARD

Show:
successful payments
failed
pending
webhook lag
entitlement mismatch
refunds
chargebacks

---

# 9.130 AI OPERATIONS DASHBOARD

Show:
provider
model
requests
latency
timeouts
schema failures
cost
fallback rate
quality metrics

---

# 9.131 LOG CORRELATION

All operational UIs should allow drill-down by:
requestId
correlationId
orderId
signalId
connectionId
paymentId
incidentId

---

# 9.132 DATA MASKING IN OBSERVABILITY

Admin observability should show safe references.

Never show:
API secret
full card number
password
private key

---

# 9.133 RUNBOOK LINKING

Every critical alert points to a current runbook.

---

# 9.134 DISASTER RECOVERY TEST RECORD

Each DR exercise records:
date
scope
RTO observed
RPO observed
issues
remediation
owner

---

# 9.135 BACKUP TEST RECORD

Each restore exercise records:
backup version
restore target
integrity result
duration
owner

---

# 9.136 SECURITY EXERCISES

Practice:
credential leak
provider compromise
admin takeover
bad deploy
malicious dependency
DDoS

---

# 9.137 SUPPLY-CHAIN INCIDENT

If critical dependency is compromised:
- identify affected versions
- freeze deployments
- isolate
- patch/replace
- rotate secrets if exposure possible
- assess artifacts
- redeploy signed artifacts

---

# 9.138 COMPROMISED CI

If CI credential compromise suspected:
- revoke
- invalidate tokens
- inspect builds
- inspect artifacts
- rotate signing credentials if needed
- rebuild from clean environment

---

# 9.139 COMPROMISED CONTAINER

If runtime image compromise suspected:
- isolate workloads
- stop promotion
- identify digest
- redeploy trusted version
- investigate
- rotate exposed credentials

---

# 9.140 PROD CONFIG DRIFT

Detect infrastructure drift.

Preferred:
IaC comparison
policy checks
configuration snapshots

---

# 9.141 POLICY-AS-CODE

Use policy-as-code where practical for:
network
IAM
deployment
compliance gating

Policies are versioned.

---

# 9.142 INFRASTRUCTURE-AS-CODE

All major infrastructure:
network
compute
database
storage
queue
monitoring
DNS

must be represented in version-controlled IaC where practical.

---

# 9.143 DRY-RUN

Infrastructure changes require:
plan
review
apply
verify

Production apply should be controlled.

---

# 9.144 PRODUCTION SEPARATION

Separate:
developer IAM
deployment IAM
runtime IAM

A compromised developer laptop should not automatically have unrestricted production control.

---

# 9.145 ADMIN MOBILE APP BACKEND

Use a separate API audience or service boundary if justified.

Do not reuse customer token scopes.

---

# 9.146 ADMIN DEVICE REVOCATION

If admin phone lost:
remote revoke
invalidate sessions
require re-enrollment

---

# 9.147 PUSH SECURITY

Admin push notifications:
do not contain secrets
minimize sensitive context
deep-link into authenticated app

---

# 9.148 MOBILE ADMIN OFFLINE MODE

Avoid storing privileged operational state offline.

Safe default:
read-only cached non-sensitive dashboard data.

No offline production commands unless an explicit security design supports them.

---

# 9.149 PRODUCTION DEPLOYMENT CHECKLIST

[ ] Infrastructure plan approved
[ ] Security scans passed
[ ] Secrets available
[ ] Database migration tested
[ ] Rollback documented
[ ] Monitoring active
[ ] Alerts active
[ ] Status page ready
[ ] On-call ready
[ ] Release artifact signed
[ ] Feature flags configured
[ ] Canary plan ready
[ ] Trading pause control tested
[ ] Reconciliation healthy

---

# 9.150 LIVE EXECUTION RELEASE CHECKLIST

[ ] Risk service healthy
[ ] Policy service healthy
[ ] Market data fresh
[ ] Provider healthy
[ ] Credential validation healthy
[ ] Reconciliation backlog acceptable
[ ] Kill switch available
[ ] Audit service healthy
[ ] Monitoring healthy
[ ] No active critical incident
[ ] Strategy approved
[ ] Canary scope defined
[ ] Rollback tested

---

# 9.151 POST-RELEASE WATCH

Immediately after a critical release, watch:
- error rate
- latency
- order behavior
- rejection
- unknown
- reconciliation
- payment mismatches
- authentication failures

---

# 9.152 SAFE ROLLBACK

If a critical anomaly appears:
1 pause affected capability
2 preserve state
3 rollback application if safe
4 reconcile
5 verify
6 resume gradually

Never rollback blindly across financial state changes.

---

# 9.153 DOCUMENTATION OF INFRASTRUCTURE

Maintain:
architecture diagram
network diagram
service catalog
dependency map
IAM matrix
secret map
backup map
DR plan
runbooks
release process

---

# 9.154 ARCHITECTURE DIAGRAMS

Required diagrams:
- global architecture
- request flow
- trading flow
- payment flow
- admin flow
- secret flow
- data flow
- DR flow

---

# 9.155 SECURITY DATA FLOW

Document:

Customer
→ Web
→ API
→ Auth
→ Entitlement
→ Policy
→ Risk
→ Execution
→ Provider

Secret path is separate and restricted.

---

# 9.156 OPERATIONAL DATA FLOW

Logs:
services
→ collector
→ secure storage
→ dashboards/alerts

Analytics:
safe event stream
→ warehouse

Never route secrets into analytics.

---

# 9.157 AUDIT DATA FLOW

Critical event
→ audit service
→ immutable/restricted storage

Audit availability is treated as a critical control.

---

# 9.158 HIGH-AVAILABILITY TRADEOFF

Do not pursue maximum uptime at any cost.

For financial execution:
CONSISTENCY + SAFETY > BLIND AVAILABILITY.

A brief NO_EXECUTION state is preferable to uncontrolled duplicate orders.

---

# 9.159 PLATFORM ENGINEERING RULE

Every critical component must answer:

What happens if it fails?

Expected response:
safe fallback
retry
degraded mode
pause
or abstain

Not:
unknown behavior.

---

# 9.160 FINAL INFRASTRUCTURE ACCEPTANCE GATE

[ ] IaC implemented
[ ] Network segmented
[ ] Production secrets externalized
[ ] IAM least privilege
[ ] Environment isolation
[ ] Containers hardened
[ ] Images scanned
[ ] SBOM generated
[ ] Artifacts signed
[ ] Database HA configured
[ ] Backups configured
[ ] Restore tested
[ ] DR tested
[ ] Logs centralized
[ ] Logs redacted
[ ] Metrics live
[ ] Tracing live
[ ] Alerts routed
[ ] Runbooks available
[ ] Incident process defined
[ ] Kill switches tested
[ ] Release process controlled
[ ] Rollback documented
[ ] Cost monitoring enabled
[ ] Admin access audited
[ ] Customer secrets inaccessible to admins
[ ] Live execution has multiple independent safety gates

---

# 9.161 MASTER PRODUCTION PRINCIPLE

Production is not:

“the code is deployed.”

Production is:

“the software, infrastructure, security controls, operational controls, monitoring, backups, recovery process, policy gates, financial safeguards, documentation and responsible owners are all ready.”

---

# 9.162 MASTER SAFETY PRINCIPLE

When any critical dependency becomes uncertain:

STOP NEW HIGH-RISK ACTIONS
PRESERVE STATE
RECONCILE
INVESTIGATE
RESUME ONLY AFTER VALIDATION

---

# 9.163 MASTER RESILIENCE PRINCIPLE

The platform must be designed under the assumption that:

servers fail
networks fail
providers fail
databases fail
queues fail
models fail
people make mistakes
dependencies become vulnerable
policies change
deployments can be wrong

The architecture must make these failures survivable.

---

# 9.164 MASTER OPERATIONS PRINCIPLE

A mature platform is one where:

- engineers can deploy safely
- operators can understand health
- security can investigate
- finance can reconcile
- support can help without seeing secrets
- customers can understand service state
- trading can stop safely
- recovery can be rehearsed
- every consequential change is attributable

---

# 9.165 PART 9 FINAL DIRECTIVE

Implement infrastructure as a controlled system, not as a collection of servers.

Use:
Infrastructure as Code
least privilege
secure secrets
immutable artifacts
automated testing
observability
backup
restore
disaster recovery
controlled deployment
canary
feature flags
kill switches
reconciliation
audit

Never make production live-trading availability depend on optimism.

END OF PART 9
