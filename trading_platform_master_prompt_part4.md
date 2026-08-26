# TRADING PLATFORM MASTER PROMPT
## PART 4 — SECURITY, TRUST, COMPLIANCE UX, ACCESSIBILITY, QA & RELEASE ENGINEERING

> Continuation of Parts 1–3. This document is an implementation-grade specification. It is intentionally strict. Do not weaken security or compliance requirements for visual convenience.


## PART 4 — SECURITY, IDENTITY, TRUST, COMPLIANCE UX, ACCESSIBILITY, QA & RELEASE ENGINEERING

This document is the continuation of the Master Product Prompt. It is normative for product design and engineering unless a later part explicitly supersedes a requirement.

PRIMARY OBJECTIVE
Build a security-first, multilingual, accessible, auditable, production-grade financial software platform for lawful jurisdictions and supported products. Never optimize for bypassing platform review, financial regulation, exchange controls, identity checks, app-store policy, or security controls.

NON-NEGOTIABLE PRINCIPLES
1. Security is a product feature, not a backend afterthought.
2. No administrator may casually reveal a customer's exchange secret.
3. No AI model is allowed to be the final authority for execution permissions.
4. Every consequential financial action must be attributable to a user, service, policy version, timestamp, and correlation ID.
5. Product availability must be controlled by jurisdiction, platform, account status, product eligibility, and compliance policy.
6. Every risky action must have an explicit state machine and an auditable result.
7. The UI must never imply guaranteed profit, guaranteed execution, guaranteed uptime, or guaranteed approval.
8. The system must degrade safely when an exchange, payment provider, AI provider, notification service, market-data provider, or internal dependency fails.
9. Persian RTL and English LTR are first-class experiences, not translations layered onto an English interface.
10. Accessibility must be designed from the component level upward.


## 4.1 SECURITY THREAT MODEL

Create and maintain a living threat model covering:
- credential theft
- API key theft
- secret leakage through logs
- malicious administrator behavior
- compromised administrator device
- session hijacking
- CSRF
- XSS
- SSRF
- SQL/NoSQL injection
- broken access control
- privilege escalation
- insecure direct object references
- webhook spoofing
- payment replay
- subscription entitlement manipulation
- exchange API replay
- duplicate order submission
- stale market data
- model prompt injection
- poisoned news/fundamental inputs
- malicious blog/editor content
- supply-chain compromise
- dependency compromise
- CI/CD credential leakage
- cloud IAM compromise
- backup leakage
- insider abuse
- denial of service
- account takeover
- fraudulent refund/chargeback behavior
- device loss
- localization/content injection

For each threat define:
THREAT_ID
ASSET
ACTOR
ENTRY_POINT
PRECONDITION
ATTACK_PATH
IMPACT
LIKELIHOOD
DETECTION
PREVENTION
MITIGATION
RECOVERY
OWNER
TEST_CASE
EVIDENCE

The threat model must be reviewed before production and after every major architectural change.


## 4.2 IDENTITY ARCHITECTURE

Support a modern identity layer with:
- email verification
- optional phone verification where legally and operationally appropriate
- passkeys/WebAuthn
- TOTP-based MFA as a fallback/secondary factor
- recovery codes
- trusted-device management
- session listing and remote session revocation
- risk-based authentication
- administrator step-up authentication
- suspicious-login alerts
- device and browser metadata
- country/region policy evaluation

Prefer passkeys for high-value accounts. WebAuthn uses public-key cryptography and can provide passwordless authentication and phishing-resistant MFA. Treat passkeys as a preferred authentication method, while retaining secure recovery paths.

Never store private passkeys on the server.
Store only the credential material required to verify assertions.

Authentication events must include:
user_id
credential_id
authentication_method
device_id
IP metadata
country/region
timestamp
result
risk_score
correlation_id

Session policy:
- short-lived access tokens
- rotating refresh tokens
- revocation
- idle timeout
- absolute session lifetime
- device-bound risk signals where appropriate
- secure, HttpOnly, SameSite cookies for browser sessions when cookie sessions are used
- CSRF protection for cookie-authenticated state-changing requests


## 4.3 AUTHORIZATION & RBAC

Implement deny-by-default authorization.

Roles:
- SUPER_ADMIN
- SECURITY_ADMIN
- FINANCE_ADMIN
- SUPPORT_ADMIN
- CONTENT_ADMIN
- COMPLIANCE_REVIEWER
- OPERATIONS_ADMIN
- READ_ONLY_ADMIN
- CUSTOMER
- SERVICE_ACCOUNT

Use RBAC plus resource-level permissions.

Examples:
customer.read
customer.sensitive.read
subscription.read
subscription.change
payment.read
refund.request
refund.approve
exchange_connection.read
exchange_connection.revoke
exchange_secret.reveal — SHOULD NOT EXIST as a normal permission
strategy.read
strategy.publish
strategy.pause
execution.read
execution.cancel
risk_policy.read
risk_policy.change
content.publish
feature_flag.change
audit.read
security_policy.change

The system should deliberately omit dangerous permissions where possible rather than merely hiding buttons.

Every authorization denial must be logged without exposing secrets.


## 4.4 SECRET & EXCHANGE CREDENTIAL UX

Customer flow:
1. Customer selects an eligible exchange.
2. UI explains exactly which permissions are required.
3. UI explicitly recommends trade-only permissions and prohibits withdrawal permissions.
4. Customer enters API key and secret only into a secure form.
5. Browser transmits only over TLS.
6. Backend validates syntax.
7. Secret is immediately written to a dedicated secret-management boundary.
8. Application database stores a reference, not the plaintext secret.
9. Secret value is never returned to frontend after submission.
10. Admin UI shows:
   - connected
   - pending validation
   - validation failed
   - restricted
   - revoked
   - expired
   - permission mismatch
   - last health check
   - last successful API interaction
   but never the raw secret.
11. Customer can revoke and replace credentials.
12. Every credential operation generates an audit event.

If an exchange supports OAuth or delegated authorization, prefer it over manually entered secrets.

If withdrawal permission is detected, block activation by default and show a high-severity security warning.

Never print secrets in:
- logs
- analytics
- crash reports
- support tickets
- URLs
- query strings
- browser localStorage
- browser sessionStorage
- screenshots generated automatically
- email notifications
- push notifications
- audit payloads


## 4.5 SECURITY UX MICROCOPY

Use calm, precise language.

GOOD:
“Your credentials are encrypted and stored in a dedicated secure vault. We cannot display the secret again after submission.”

GOOD:
“For automated trading, enable only the permissions required for trading. Withdrawal access is not required.”

BAD:
“Your funds are 100% safe.”

BAD:
“Guaranteed profit.”

BAD:
“AI guarantees the best trade.”

BAD:
“Your API secret is visible to our administrators.”

Risk disclosures must be visible before activation, not hidden in a footer.


## 4.6 ADMIN SECURITY CONSOLE

The separate administrator Android application is a control-plane client, not a second copy of the customer database.

Admin application requirements:
- mandatory MFA/passkey
- device enrollment
- remote device revocation
- app attestation where feasible
- encrypted local storage
- no secret caching
- short session lifetime
- screenshot protection on sensitive screens where platform capabilities allow
- biometric step-up for destructive actions
- confirmation for high-risk actions
- reason capture for sensitive changes
- immutable audit trail

High-risk actions require two-person approval when configured:
- changing risk limits
- enabling a new strategy globally
- changing exchange adapters
- modifying withdrawal-related policy
- deleting customer data
- issuing large refunds
- changing entitlement rules
- changing production feature flags
- changing compliance gates


## 4.7 AUDIT LOGGING

Create an append-oriented audit system.

Each audit record:
event_id
timestamp
actor_type
actor_id
actor_role
action
resource_type
resource_id
before_state_hash
after_state_hash
policy_version
request_id
correlation_id
ip_metadata
device_id
result
reason
risk_level

Never store sensitive secrets in before/after snapshots.

Audit categories:
AUTH
AUTHZ
CUSTOMER
PAYMENT
SUBSCRIPTION
EXCHANGE
TRADING
RISK
AI
CONTENT
ADMIN
SECURITY
COMPLIANCE
SYSTEM
DEPLOYMENT

Provide immutable retention policies according to applicable law and business requirements.


## 4.8 AI GOVERNANCE

AI agents are advisory decision-support components inside a deterministic control system.

The three conceptual agents may be:
A. STRATEGY ANALYST
B. RISK & CAPITAL ANALYST
C. FUNDAMENTAL / NEWS RESEARCH ANALYST

Their output must be structured and versioned.

Each AI result must contain:
agent_id
model_id
model_version
prompt_policy_version
input_snapshot_id
market_data_snapshot_id
knowledge_timestamp
confidence
recommendation
risk_flags
reason_codes
expiry
schema_version

AI output may recommend:
- BUY
- SELL
- HOLD
- NO_TRADE
- REDUCE_SIZE
- CLOSE
- WAIT_FOR_CONFIRMATION

But the deterministic policy engine has final authority.

AI must NEVER:
- directly hold exchange credentials
- directly execute arbitrary code
- override risk limits
- bypass entitlement
- bypass compliance
- increase leverage because of confidence
- promise outcomes
- fabricate evidence

Use independent data validation, stale-data detection, schema validation, prompt-injection filtering, source provenance, and deterministic post-processing.

If one agent is unavailable, the system must follow a predefined degraded-mode policy. Do not silently substitute invented confidence.


## 4.9 16+ ANALYST CONSENSUS LAYER

If the product markets a multi-analyst validation layer, represent it honestly as a configurable ensemble rather than a magical guarantee.

Define analyst families such as:
- trend
- momentum
- volatility
- market structure
- liquidity
- volume
- order-flow
- support/resistance
- regime detection
- sentiment
- macro
- fundamentals
- news
- correlation
- session behavior
- anomaly detection
- risk-adjusted expectancy

The exact count may be configured.

Each analyst emits:
score
direction
confidence
data_quality
timestamp
reason_codes

The ensemble computes:
weighted_consensus
disagreement_score
data_quality_score
signal_strength
risk_penalty
execution_eligibility

A high consensus does not mean a guaranteed profitable outcome.


## 4.10 RISK ENGINE

Risk must be deterministic and independent from language-model output.

Inputs:
account_equity
available_margin
current_exposure
symbol_exposure
portfolio_exposure
leverage
volatility
stop_distance
position_size
correlation
liquidity
spread
fees
slippage_estimate
strategy_risk_budget
customer_risk_profile
product_limits
jurisdiction_limits

Outputs:
allowed
rejected
recommended_size
max_size
risk_score
required_stop
max_leverage
reason_codes

Hard limits override recommendations.

Include circuit breakers:
- abnormal volatility
- abnormal spread
- exchange outage
- market-data stale
- repeated execution failures
- daily loss limit
- strategy drawdown limit
- account-level risk limit
- portfolio concentration limit


## 4.11 TRADING EXECUTION SAFETY

Use an explicit order lifecycle:
CREATED
VALIDATING
RISK_CHECK
WAITING_FOR_MARKET_DATA
APPROVED
SUBMITTING
ACKNOWLEDGED
PARTIALLY_FILLED
FILLED
CANCEL_REQUESTED
CANCELLED
REJECTED
EXPIRED
UNKNOWN
RECONCILIATION_REQUIRED

Every order receives a unique client_order_id/idempotency key.

Never retry an unknown order blindly.
When execution status is ambiguous:
1. stop duplicate submission
2. query exchange
3. reconcile
4. record outcome
5. resume only after state is known

All execution workers must be idempotent.


## 4.12 PAYMENT & ENTITLEMENT SECURITY

Payment provider state is authoritative for payment status, while the internal entitlement service determines what the customer may access.

Never grant premium access solely because a frontend success screen appeared.

Flow:
CHECKOUT_CREATED
PAYMENT_PENDING
PAYMENT_AUTHORIZED
PAYMENT_CONFIRMED
ENTITLEMENT_ACTIVATED

Failure states:
PAYMENT_FAILED
PAYMENT_CANCELLED
PAYMENT_EXPIRED
CHARGEBACK
REFUND
ENTITLEMENT_SUSPENDED

Webhook handling:
- verify signature
- reject replay
- enforce timestamp tolerance
- store event ID
- process idempotently
- reconcile periodically
- never trust client-provided payment status

Subscription durations:
7 days
1 month
3 months
6 months
12 months

Product capability and duration are separate dimensions.


## 4.13 PRODUCT CONFIGURATION MODEL

The customer must first select PRODUCT, then MARKET MODE, then SUBSCRIPTION TERM.

CRYPTO:
SPOT
FUTURES/PERPETUALS
HYBRID

FOREX:
SPOT/CFD-LIKE BROKER MODEL where legally and technically applicable
MT5/EA EXECUTION
HYBRID
Use broker-specific terminology instead of pretending forex is identical to exchange futures.

BINARY-OPTIONS-LIKE PRODUCT:
OTC
INTERNATIONAL MARKETS
HYBRID

However, binary-options-like functionality must be feature-gated by platform and jurisdiction and may be unavailable entirely.

Signal service uses the same product taxonomy but does not automatically imply that every signal can be executed by every user.


## 4.14 COMPLIANCE GATING ENGINE

Define a centralized eligibility decision:
eligible(user, product, jurisdiction, platform, account_status, provider, permissions)

Returns:
ELIGIBLE
NOT_ELIGIBLE
REQUIRES_REVIEW
REQUIRES_DOCUMENTS
UNAVAILABLE_ON_PLATFORM
UNAVAILABLE_IN_REGION
PROVIDER_UNSUPPORTED

Never rely on frontend hiding alone.

The UI should explain restrictions without exposing internal policy logic or enabling circumvention.

Every eligibility decision is versioned and auditable.


## 4.15 ACCESSIBILITY

Target WCAG 2.2 AA as the baseline for the web product.

Requirements:
- keyboard navigation
- visible focus
- sufficient target size
- reduced-motion mode
- semantic HTML
- accessible labels
- accessible error messages
- form instructions
- non-color-only status indicators
- screen-reader announcements for live trading state
- logical heading hierarchy
- RTL-aware focus order
- charts with text summaries
- tables with accessible headers
- no essential information conveyed only through animation

Motion must stop or simplify under prefers-reduced-motion.

Do not use animation to communicate a financial risk that disappears before a user can inspect it.


## 4.16 RTL/LTR INTERNATIONALIZATION

Supported languages:
PERSIAN — fa-IR
ENGLISH — en

Architecture:
- no hard-coded UI strings
- locale files
- ICU message formatting
- pluralization
- number formatting
- currency formatting
- date/time formatting
- timezone-aware rendering
- Persian digits option
- Latin digits option
- bidirectional text isolation
- direction-aware icons where appropriate

The language selector uses two circular flag controls only if the flags are accompanied by accessible language names. Flags are visual identifiers, not the sole semantic label.

RTL requirements:
- layout direction flips
- margins/padding logical properties
- navigation order is intentional
- charts retain meaningful axes
- financial numbers may remain LTR inside RTL contexts
- API keys, IDs, URLs and code remain LTR
- mixed-language text uses bidi isolation
- punctuation is tested
- Persian typography is tested at every breakpoint


## 4.17 TYPOGRAPHY SYSTEM

Persian font family candidates:
- Vazirmatn
- a second Persian-compatible UI family
- a third Persian-compatible editorial family
- a fallback system stack

English:
- Inter or equivalent modern UI family
- a second display family
- a readable editorial family
- system fallback

Never force a Persian font onto Latin-heavy code or financial identifiers.

Define tokens:
font.family.display
font.family.body
font.family.mono
font.size.xs through 7xl
font.weight.400/500/600/700
line-height tokens
letter-spacing tokens

Test Persian diacritics, Arabic punctuation, Arabic numerals, Latin numerals, mixed symbols, ticker symbols, URLs and code strings.


## 4.18 ERROR & RECOVERY UX

Required states:
LOADING
SKELETON
EMPTY
PARTIAL
OFFLINE
DEGRADED
RETRYABLE_ERROR
FATAL_ERROR
PERMISSION_DENIED
AUTH_REQUIRED
COMPLIANCE_BLOCKED
PAYMENT_PENDING
EXCHANGE_DISCONNECTED
MARKET_DATA_STALE

Error messages must tell users:
WHAT happened
WHAT it means
WHAT they can do
WHETHER MONEY OR ORDERS ARE AFFECTED
WHETHER THE SYSTEM IS SAFE TO RETRY

Never tell users to retry an order submission when order state is unknown.


## 4.19 OBSERVABILITY

Implement:
structured logs
metrics
distributed traces
correlation IDs
error tracking
synthetic monitoring
uptime checks
exchange adapter health
market-data freshness
queue lag
order reconciliation lag
payment webhook lag
AI latency
AI failure rate
entitlement mismatch rate

Financially important metrics:
unknown_order_count
duplicate_order_prevention_count
reconciliation_failure_count
risk_rejection_count
stale_market_data_count
credential_validation_failure_count
payment_entitlement_mismatch_count

Alerts must be severity-based and routed to responsible teams.


## 4.20 BACKUP, DR & BUSINESS CONTINUITY

Define RPO and RTO per service.

Critical state:
- customer accounts
- entitlements
- payments
- exchange connection references
- risk policies
- strategy versions
- order ledger
- audit records

Backups must be encrypted and access-controlled.

Disaster recovery tests must be performed periodically.

Never restore production from an unverified backup.

Trading services must have a safe shutdown procedure:
1. stop new orders
2. preserve current state
3. reconcile open orders
4. notify operations
5. resume only after health checks pass.


## 4.21 QA TEST PYRAMID

Unit tests:
- pricing
- entitlement
- eligibility
- risk
- order state machine
- idempotency
- localization
- permission checks

Integration:
- payment webhook
- exchange adapter
- secret vault
- authentication
- notifications

End-to-end:
- signup
- onboarding
- subscription
- credential connection
- strategy activation
- signal delivery
- simulated execution
- cancellation
- renewal
- refund
- account recovery

Security:
- SAST
- dependency scanning
- secret scanning
- DAST
- API authorization tests
- rate-limit tests
- session tests
- CSRF/XSS tests
- SSRF tests
- webhook replay tests
- privilege escalation tests

Never test financial execution against real money by default.


## 4.22 RELEASE GATES

A release is BLOCKED if:
- critical security vulnerability exists
- secret leakage is detected
- entitlement bypass exists
- authorization bypass exists
- unknown order reconciliation is broken
- payment webhook verification fails
- localization corrupts financial values
- RTL layout breaks a critical flow
- compliance gating can be bypassed client-side
- audit trail is missing for a high-risk action
- accessibility regressions break core journeys
- destructive admin action lacks required authorization

Release evidence must include:
build ID
commit SHA
test report
security report
dependency report
migration status
rollback plan
feature-flag state
owner approval


## 4.23 FRONTEND PERFORMANCE

Target:
- fast first meaningful render
- minimal JavaScript for public SEO pages
- code splitting
- lazy loading
- image optimization
- responsive image sources
- font subsetting
- preconnect only where useful
- cache strategy
- service worker only where safe
- animation budget
- no blocking third-party scripts

Trading dashboard must prioritize data freshness and interaction responsiveness over decorative effects.

The luxury visual system must never degrade financial usability.


## 4.24 CONTENT & TRUST

Blog content must distinguish:
EDITORIAL
EDUCATIONAL
MARKET COMMENTARY
PRODUCT DOCUMENTATION
RISK DISCLOSURE
PROMOTIONAL CONTENT

Every market article should have:
author
reviewer when applicable
publication date
last reviewed date
sources
methodology
risk disclaimer where relevant

Never manufacture testimonials, performance history, win rates, AI success rates, or regulatory claims.

Never use fake scarcity or fake live-user counters.


## 4.25 TRUST CENTER

Create a public Trust Center containing:
- security overview
- privacy overview
- data handling
- credential handling
- uptime/status link
- incident communication policy
- responsible disclosure
- supported integrations
- risk disclosures
- jurisdiction availability
- terms
- refund policy
- subscription policy

The Trust Center is a product surface, not merely legal boilerplate.


## 4.26 FINAL IMPLEMENTATION DIRECTIVE

When implementing this specification, do not invent missing business rules silently.

If a rule is unknown:
1. mark it CONFIG_REQUIRED
2. create a safe default
3. expose it to authorized configuration
4. document the assumption
5. create a test
6. request business/legal confirmation before production

Do not optimize the system around a promise that cannot be guaranteed.

The final platform must feel premium, calm, precise, transparent, secure, and technically mature.

SUCCESS CRITERIA
A first-time user understands what the product does.
A sophisticated trader understands what it does not guarantee.
An administrator can operate the platform without seeing customer secrets.
A security reviewer can trace consequential actions.
A compliance reviewer can determine why a feature is available or unavailable.
A developer can implement each requirement without guessing.
A Persian user experiences a genuinely native RTL product.
An English user experiences a genuinely native LTR product.
A disabled feature fails safely and explains why.
A market outage does not become a duplicate-order incident.
A payment race condition does not create unauthorized premium access.
An AI failure does not create uncontrolled trading.
A visually impressive page remains usable, accessible, and performant.


## 4.27 MASTER ACCEPTANCE CHECKLIST

- [ ] Identity supports secure MFA/passkeys and recovery.
- [ ] Authorization is deny-by-default.
- [ ] Admin cannot reveal exchange secrets.
- [ ] Secrets are isolated from ordinary application data.
- [ ] Withdrawal permissions are rejected by default.
- [ ] Credential lifecycle is auditable.
- [ ] Orders use idempotency.
- [ ] Unknown execution states reconcile before retry.
- [ ] Risk engine is deterministic.
- [ ] AI cannot bypass hard limits.
- [ ] Payment webhooks are verified and idempotent.
- [ ] Entitlements are server-authoritative.
- [ ] Jurisdiction/platform gating is server-enforced.
- [ ] RTL/LTR is fully implemented.
- [ ] Persian and English typography are tested.
- [ ] WCAG 2.2 AA is the accessibility target.
- [ ] Reduced motion is supported.
- [ ] Critical errors explain financial impact.
- [ ] Audit logs contain no secrets.
- [ ] Backup and disaster recovery are tested.
- [ ] Security tests run in CI.
- [ ] Production releases have rollback plans.
- [ ] Public trust and disclosure surfaces exist.
- [ ] No marketing statement implies guaranteed profit or guaranteed approval.


## 4.28 OFFICIAL REFERENCE BASELINE

This part should be implemented against current official standards and documentation, with version/date verification before production.

Key references:
- W3C Web Content Accessibility Guidelines (WCAG) 2.2: https://www.w3.org/TR/WCAG22/
- MDN Web Authentication API / WebAuthn: https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API
- MDN Passkeys: https://developer.mozilla.org/en-US/docs/Web/Security/Authentication/Passkeys

These references establish the accessibility and modern authentication baseline. The project should additionally maintain an internal dependency/version register so that standards, SDKs, app-store requirements, exchange APIs, payment APIs, and security guidance are rechecked before each release.


## 4.29 REQUIRED SCHEMAS AND CONTRACTS

Define machine-readable contracts for every critical domain.

IdentityContract:
userId
credentialId
authMethod
sessionId
riskLevel
lastAuthenticatedAt

ExchangeConnectionContract:
connectionId
userId
provider
permissions
secretReference
status
healthStatus
lastValidatedAt
lastUsedAt
createdAt
revokedAt

SubscriptionContract:
subscriptionId
userId
productId
marketMode
term
status
provider
providerReference
startsAt
endsAt
renewalAt
entitlementVersion

SignalContract:
signalId
product
marketMode
instrument
direction
entryModel
stopModel
targetModel
riskScore
confidence
dataQuality
expiry
status
strategyVersion
agentConsensusVersion

ExecutionContract:
executionId
signalId
connectionId
clientOrderId
state
requestedQuantity
approvedQuantity
submittedQuantity
filledQuantity
averagePrice
fees
slippage
exchangeOrderId
createdAt
updatedAt

AuditContract:
eventId
actor
action
resource
result
policyVersion
correlationId
timestamp

Every contract must have:
schema_version
backward_compatibility_policy
validation_rules
example
negative_examples
migration_plan


## 4.30 SAFE DEFAULTS

When configuration is missing:
- trading = DISABLED
- automated execution = DISABLED
- leverage escalation = DISABLED
- withdrawal permission = REJECT
- binary-options-like capability = UNAVAILABLE
- unknown order state = RECONCILIATION_REQUIRED
- stale market data = NO_TRADE
- AI disagreement = NO_TRADE or configured conservative mode
- payment webhook mismatch = ACCESS NOT GRANTED
- admin high-risk change without approval = REJECT
- unsupported jurisdiction = UNAVAILABLE
- unsupported platform = UNAVAILABLE
- missing risk policy = REJECT
- missing stop-loss policy where required = REJECT

The safe default must be deterministic and testable.


## 4.31.01 IMPLEMENTATION WORK PACKAGE 1

Treat Work Package 1 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.02 IMPLEMENTATION WORK PACKAGE 2

Treat Work Package 2 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.03 IMPLEMENTATION WORK PACKAGE 3

Treat Work Package 3 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.04 IMPLEMENTATION WORK PACKAGE 4

Treat Work Package 4 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.05 IMPLEMENTATION WORK PACKAGE 5

Treat Work Package 5 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.06 IMPLEMENTATION WORK PACKAGE 6

Treat Work Package 6 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.07 IMPLEMENTATION WORK PACKAGE 7

Treat Work Package 7 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.08 IMPLEMENTATION WORK PACKAGE 8

Treat Work Package 8 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.09 IMPLEMENTATION WORK PACKAGE 9

Treat Work Package 9 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.10 IMPLEMENTATION WORK PACKAGE 10

Treat Work Package 10 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.11 IMPLEMENTATION WORK PACKAGE 11

Treat Work Package 11 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.12 IMPLEMENTATION WORK PACKAGE 12

Treat Work Package 12 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.13 IMPLEMENTATION WORK PACKAGE 13

Treat Work Package 13 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.14 IMPLEMENTATION WORK PACKAGE 14

Treat Work Package 14 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.15 IMPLEMENTATION WORK PACKAGE 15

Treat Work Package 15 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.16 IMPLEMENTATION WORK PACKAGE 16

Treat Work Package 16 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.17 IMPLEMENTATION WORK PACKAGE 17

Treat Work Package 17 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.18 IMPLEMENTATION WORK PACKAGE 18

Treat Work Package 18 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.19 IMPLEMENTATION WORK PACKAGE 19

Treat Work Package 19 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.20 IMPLEMENTATION WORK PACKAGE 20

Treat Work Package 20 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.21 IMPLEMENTATION WORK PACKAGE 21

Treat Work Package 21 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.22 IMPLEMENTATION WORK PACKAGE 22

Treat Work Package 22 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.23 IMPLEMENTATION WORK PACKAGE 23

Treat Work Package 23 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.24 IMPLEMENTATION WORK PACKAGE 24

Treat Work Package 24 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.25 IMPLEMENTATION WORK PACKAGE 25

Treat Work Package 25 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.26 IMPLEMENTATION WORK PACKAGE 26

Treat Work Package 26 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.27 IMPLEMENTATION WORK PACKAGE 27

Treat Work Package 27 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.28 IMPLEMENTATION WORK PACKAGE 28

Treat Work Package 28 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.29 IMPLEMENTATION WORK PACKAGE 29

Treat Work Package 29 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.


## 4.31.30 IMPLEMENTATION WORK PACKAGE 30

Treat Work Package 30 as independently testable.

DELIVERABLES
- architecture decision record
- implementation ticket set
- API contract
- database migration if needed
- frontend states
- backend validation
- audit events
- automated tests
- security tests
- accessibility tests
- localization tests
- monitoring
- rollback procedure

DEFINITION OF DONE
The feature is implemented in both Persian RTL and English LTR, has loading/empty/error/offline/degraded states, has authorization checks, has server-side validation, has audit coverage when consequential, has automated tests, and has documented failure behavior.

QUALITY RULE
Do not mark a feature complete because the happy path works. A feature is complete only when its failure paths, security boundary, localization, accessibility, observability, and rollback behavior are also implemented.
