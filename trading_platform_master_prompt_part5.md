# TRADING PLATFORM MASTER PROMPT
# PART 5 — PRODUCT ENGINEERING, API CONTRACTS, DATA MODEL, INTEGRATIONS, DEVOPS & PLATFORM RELEASE ARCHITECTURE

This is a direct continuation of Parts 1–4.

This part converts the previous product, UX, security, compliance, and governance requirements into an implementation-oriented engineering blueprint.

IMPORTANT:
- Do not treat this document as legal advice.
- Do not assume regulatory authorization exists.
- Do not assume any app store will approve a financial product.
- Do not implement prohibited functionality merely because a UI specification mentions it.
- The backend is the final enforcement layer for product eligibility, jurisdiction, permissions, risk, entitlement, and execution.
- The frontend is never a security boundary.
- AI is never an unrestricted execution authority.

---

## 5.0 ENGINEERING NORTH STAR

The system must be:

- secure by design
- modular
- observable
- horizontally scalable
- fault tolerant
- multilingual
- accessible
- SEO-friendly
- PWA-capable
- mobile-capable
- desktop-capable
- audit-ready
- compliance-aware
- exchange-agnostic
- payment-provider-agnostic
- AI-provider-agnostic
- deployable through controlled environments
- capable of safely disabling unsupported products

The platform consists of six major planes:

1. EXPERIENCE PLANE
2. APPLICATION PLANE
3. DECISION PLANE
4. EXECUTION PLANE
5. CONTROL PLANE
6. DATA / OBSERVABILITY PLANE

Never collapse all six into one monolithic responsibility.

---

# 5.1 SYSTEM PLANES

## 5.1.1 EXPERIENCE PLANE

Contains:

- public website
- landing pages
- pricing
- checkout
- blog
- documentation
- legal pages
- customer dashboard
- signal interface
- trading configuration
- connection center
- account center
- notifications
- PWA shell
- mobile application
- desktop application

The experience plane may request actions, but may not independently authorize sensitive actions.

---

## 5.1.2 APPLICATION PLANE

Responsibilities:

- identity
- accounts
- subscriptions
- entitlements
- customer profile
- product catalog
- content
- notifications
- support
- onboarding
- billing integration
- feature flags

This plane translates customer intent into validated domain commands.

---

## 5.1.3 DECISION PLANE

Responsibilities:

- strategy evaluation
- market regime classification
- risk calculation
- eligibility
- AI ensemble
- signal construction
- policy validation
- capital allocation recommendation

The decision plane does not directly expose raw provider credentials to AI services.

---

## 5.1.4 EXECUTION PLANE

Responsibilities:

- broker/exchange adapters
- order creation
- order validation
- order submission
- order state tracking
- reconciliation
- position synchronization
- account balance synchronization
- execution telemetry

Execution must be deterministic wherever possible.

---

## 5.1.5 CONTROL PLANE

The control plane is primarily used by authorized operators.

Functions:

- user support
- feature flags
- product configuration
- strategy lifecycle
- risk policy management
- exchange integration health
- AI provider health
- incident management
- payment reconciliation
- entitlement correction
- audit investigation
- content publishing

The control plane must be treated as a privileged security boundary.

---

## 5.1.6 DATA / OBSERVABILITY PLANE

Contains:

- transactional database
- analytics warehouse
- time-series data
- event stream
- audit store
- log aggregation
- metrics
- traces
- model evaluation records
- market-data snapshots
- operational dashboards

Do not mix raw secrets into any analytics pipeline.

---

# 5.2 RECOMMENDED REPOSITORY STRATEGY

Use a monorepo unless organizational scale requires otherwise.

Suggested structure:

apps/
  web/
  mobile/
  desktop/
  admin-mobile/

services/
  api/
  auth/
  billing/
  entitlement/
  signal/
  risk/
  execution/
  market-data/
  notification/
  content/
  audit/
  reconciliation/

packages/
  ui/
  design-tokens/
  i18n/
  schemas/
  api-client/
  auth-client/
  analytics/
  observability/
  domain-types/
  validation/

infra/
  environments/
  terraform-or-equivalent/
  kubernetes-or-equivalent/
  monitoring/
  secrets/
  policies/

docs/
  architecture/
  api/
  security/
  compliance/
  runbooks/
  adr/

Do not allow arbitrary cross-service imports.

Define domain ownership explicitly.

---

# 5.3 DOMAIN BOUNDARIES

Core bounded contexts:

IDENTITY
ACCOUNT
CATALOG
SUBSCRIPTION
ENTITLEMENT
PAYMENT
COMPLIANCE
EXCHANGE_CONNECTION
MARKET_DATA
STRATEGY
SIGNAL
RISK
EXECUTION
PORTFOLIO
NOTIFICATION
CONTENT
SUPPORT
AUDIT
ADMIN
ANALYTICS

Each context owns its state.

Avoid shared mutable database tables across domains.

---

# 5.4 DATABASE PRINCIPLES

Use a relational transactional database for core business state unless a specific workload justifies another technology.

Candidate:
PostgreSQL or equivalent enterprise relational database.

Use separate systems where appropriate for:

- time-series market data
- search
- caching
- object storage
- event streaming
- analytics

Never use the analytics warehouse as the transactional source of truth.

---

# 5.5 CORE DATABASE ENTITIES

## users

Fields:

id
email
email_verified_at
phone
phone_verified_at
status
locale
timezone
country
created_at
updated_at
last_login_at

Do not store unnecessary personal information.

---

## user_profiles

id
user_id
display_name
avatar_reference
preferred_currency
risk_profile
communication_preferences
marketing_preferences
created_at
updated_at

---

## sessions

id
user_id
device_id
created_at
expires_at
revoked_at
last_seen_at
risk_level

---

## devices

id
user_id
platform
os_version
app_version
device_label
attestation_status
last_seen_at
revoked_at

---

## subscriptions

id
user_id
product_id
market_mode
term
provider
provider_subscription_id
status
start_at
end_at
renew_at
created_at
updated_at

---

## entitlements

id
user_id
entitlement_key
status
source_subscription_id
starts_at
expires_at
policy_version
created_at
updated_at

---

## exchange_connections

id
user_id
provider
account_reference
secret_reference
status
permission_profile
last_health_check_at
last_successful_call_at
created_at
revoked_at

Never store secret material in this table.

---

## strategies

id
name
version
status
product
market_mode
risk_profile
configuration_reference
created_at
published_at
retired_at

---

## signals

id
strategy_id
instrument
product
market_mode
direction
signal_strength
confidence
risk_score
data_quality
created_at
expires_at
status

---

## signal_evidence

id
signal_id
source_type
source_reference
snapshot_reference
weight
score
timestamp

---

## orders

id
signal_id
connection_id
client_order_id
provider_order_id
state
side
order_type
quantity
approved_quantity
filled_quantity
limit_price
stop_price
average_fill_price
fees
created_at
updated_at

---

## positions

id
connection_id
instrument
side
quantity
entry_price
mark_price
unrealized_pnl
realized_pnl
margin
leverage
updated_at

---

## risk_decisions

id
request_id
user_id
strategy_id
instrument
decision
risk_score
max_quantity
recommended_quantity
reason_codes
policy_version
created_at

---

## payments

id
user_id
provider
provider_payment_id
amount
currency
status
risk_state
created_at
updated_at

---

## payment_events

id
provider
provider_event_id
event_type
payload_reference
signature_verified
processed_at
processing_status

---

## audit_events

id
event_type
actor_type
actor_id
resource_type
resource_id
action
result
policy_version
correlation_id
timestamp

---

# 5.6 API DESIGN

Use versioned APIs.

Example:

/api/v1/auth/*
/api/v1/users/*
/api/v1/catalog/*
/api/v1/subscriptions/*
/api/v1/entitlements/*
/api/v1/exchanges/*
/api/v1/signals/*
/api/v1/risk/*
/api/v1/orders/*
/api/v1/positions/*
/api/v1/payments/*
/api/v1/content/*
/api/v1/admin/*

Never expose internal service endpoints directly to public clients.

Use an API gateway or equivalent edge layer.

---

# 5.7 API SECURITY CONTRACT

Every authenticated request must have:

authentication
authorization
input validation
rate-limit evaluation
request ID
correlation ID
structured logging

Sensitive endpoints additionally require:

step-up authentication
idempotency key
CSRF protection where relevant
resource ownership validation
risk evaluation
audit event

Follow current OWASP API security guidance, including controls for broken object-level authorization, broken authentication, broken function-level authorization, unrestricted resource consumption, SSRF, security misconfiguration, and unsafe consumption of APIs.

Reference:
https://owasp.org/API-Security/

---

# 5.8 API RESPONSE STANDARD

Success:

{
  "data": {},
  "meta": {
    "requestId": "...",
    "schemaVersion": "1.0"
  }
}

Error:

{
  "error": {
    "code": "EXCHANGE_CONNECTION_INVALID",
    "message": "The exchange connection could not be validated.",
    "userMessage": "We could not validate this connection. No trade was submitted.",
    "retryable": true,
    "requestId": "...",
    "schemaVersion": "1.0"
  }
}

Never return stack traces to clients.

Never return internal infrastructure details.

Never return secrets.

---

# 5.9 IDEMPOTENCY

Every financial mutation must support idempotency.

Examples:

POST /orders
POST /payments
POST /subscriptions
POST /exchange-connections
POST /refunds

Client sends:

Idempotency-Key: <unique value>

Server stores:

key
actor
operation
request_hash
response_reference
created_at
expires_at

If the same key is reused with a different request body, reject it.

---

# 5.10 EVENT-DRIVEN ARCHITECTURE

Use domain events for asynchronous processes.

Examples:

UserRegistered
EmailVerified
SubscriptionCreated
PaymentAuthorized
PaymentConfirmed
EntitlementActivated
EntitlementExpired
ExchangeConnected
ExchangeDisconnected
MarketDataStale
SignalCreated
RiskApproved
RiskRejected
OrderSubmitted
OrderAcknowledged
OrderFilled
OrderRejected
OrderUnknown
ReconciliationCompleted
StrategyPublished
StrategyPaused
AIAnalysisCompleted
IncidentOpened

Events must be versioned.

Example:

SignalCreated.v1
SignalCreated.v2

Never silently change the meaning of an existing event.

---

# 5.11 EVENT DELIVERY

At-least-once delivery is acceptable if consumers are idempotent.

Use:

event_id
aggregate_id
aggregate_version
occurred_at
producer
schema_version
correlation_id

Consumers must record processed event IDs where duplicate processing could cause financial or entitlement harm.

---

# 5.12 MARKET DATA ARCHITECTURE

Separate:

REAL-TIME MARKET DATA
HISTORICAL MARKET DATA
REFERENCE DATA
NEWS
FUNDAMENTAL DATA
DERIVED INDICATORS

Every market-data object must contain:

instrument
provider
timestamp
received_at
source_timestamp
quality
latency
sequence where available

Stale-data detection is mandatory.

Define product-specific freshness thresholds.

If data becomes stale:
- signal generation may be downgraded
- automated execution must be blocked where stale data could create material risk

---

# 5.13 MARKET-DATA NORMALIZATION

Build a canonical internal representation.

Canonical fields:

symbol
base_asset
quote_asset
asset_class
venue
bid
ask
last
volume
timestamp
precision
tick_size
lot_size
contract_size
margin_model
trading_status

Provider-specific fields remain inside adapter metadata.

Never assume that BTC/USDT on one venue has identical semantics to BTC/USDT on another.

---

# 5.14 EXCHANGE ADAPTER CONTRACT

Every adapter implements:

connect()
validateCredentials()
getAccount()
getBalances()
getPositions()
getOpenOrders()
getInstrument()
getMarketData()
createOrder()
cancelOrder()
getOrder()
reconcile()
healthCheck()
disconnect()

Each adapter must declare capabilities:

supportsSpot
supportsFutures
supportsPerpetuals
supportsHedgeMode
supportsReduceOnly
supportsStopOrders
supportsTakeProfit
supportsPostOnly
supportsClientOrderId
supportsWebSocket
supportsOAuth

Do not expose unsupported controls in UI.

---

# 5.15 BROKER / MT5 ADAPTER

Forex terminology must remain technically accurate.

Use:

Broker
Account
Symbol
Lot
Contract Size
Margin
Leverage
Expert Advisor (EA)
MetaTrader 5 (MT5)

Do not describe forex lots as identical to crypto futures quantities.

MT5 connectivity should use a controlled bridge/adapter.

Never ask customers for their broker password unless a specific, legally and technically justified integration requires it.

Prefer official authentication/integration mechanisms.

---

# 5.16 CRYPTO PRODUCT CONFIGURATION

Crypto product selection:

MODE A — SPOT
MODE B — FUTURES / PERPETUALS
MODE C — HYBRID

UI must explain:

SPOT:
You acquire the underlying asset through the supported venue.

FUTURES / PERPETUALS:
You trade a derivative contract whose mechanics, margin, liquidation, funding and leverage differ from spot.

HYBRID:
The system can operate across approved spot and derivative strategies according to configured rules.

Do not market leverage as an advantage without equally prominent risk explanation.

---

# 5.17 FOREX PRODUCT CONFIGURATION

Possible modes:

BROKER / MT5
HYBRID

Where a provider exposes different execution models, label them using the provider's actual terminology.

The interface must explain:
- lot size
- margin
- leverage
- spread
- swap/financing where applicable
- stop-out
- broker execution
- market hours
- slippage

Do not equate every broker product with futures.

---

# 5.18 BINARY-OPTIONS-LIKE PRODUCT

This product is subject to the strongest availability gating.

Google Play currently states that apps providing the ability to trade binary options are not allowed. Apple states that apps facilitating binary options trading are not permitted on the App Store and directs consideration of a web app; Apple also requires proper licensing for CFDs/FOREX/derivatives where applicable.

Therefore:

BINARY_OPTIONS_MOBILE_APP = DISABLED_BY_POLICY unless a future official policy change explicitly permits it.

Do not attempt:
- obfuscation
- hidden webviews
- alternate labels intended to evade review
- region spoofing
- misleading store metadata
- disguised functionality

Instead:
- isolate the capability
- implement server-side availability
- provide compliant educational/content experiences where appropriate
- use a lawful web distribution strategy if legally permissible
- maintain a policy review record

Official Google Play policy:
https://support.google.com/googleplay/android-developer/answer/9876821

Official Apple App Review Guidelines:
https://developer.apple.com/app-store/review/guidelines/

---

# 5.19 SIGNAL ENGINE

Signal pipeline:

1. ingest market data
2. validate freshness
3. normalize
4. calculate technical features
5. evaluate strategy
6. ingest fundamental/news evidence
7. evaluate AI research
8. evaluate risk
9. run ensemble validation
10. apply product policy
11. apply user entitlement
12. generate signal
13. publish
14. monitor expiry
15. invalidate when necessary

A signal has a TTL.

Expired signals must not be presented as current.

---

# 5.20 SIGNAL STRENGTH

Do not display a single number without definition.

Recommended display:

Signal Strength: 0–100
Confidence: 0–100
Data Quality: 0–100
Risk Level: LOW / MODERATE / HIGH
Consensus: percentage or normalized score
Signal Age: time since generation

Explain what each score means.

Never state:
“95% chance of profit.”

Prefer:
“Model consensus score: 95/100 under the configured methodology. This is not a probability of profit.”

---

# 5.21 AI ORCHESTRATION

The AI subsystem must have:

model registry
provider registry
prompt policy
tool policy
input validator
output schema
timeout
retry policy
fallback
evaluation
cost tracking
latency tracking
quality scoring
provenance

Every AI call gets:

agent_id
provider
model
model_version
request_id
input_snapshot
output_schema_version
timestamp

AI agents must not receive unrestricted internet access or unrestricted tool execution.

---

# 5.22 AI PROMPT-INJECTION DEFENSE

Treat all external text as untrusted data.

Sources include:
- news
- articles
- social media
- RSS
- user-generated content
- exchange metadata
- uploaded documents

Never allow external text to redefine:
- system instructions
- risk limits
- tool permissions
- execution policy
- administrator identity
- customer entitlement

Use data/content boundaries.

Example:

SYSTEM POLICY
<system-controlled rules>

UNTRUSTED MARKET CONTENT
<external data>

The model must never interpret external market content as instructions.

---

# 5.23 AI EVALUATION

Before deploying a new model:

- benchmark against historical datasets
- test adversarial inputs
- test stale data
- test missing data
- test conflicting sources
- test hallucination
- test numerical errors
- test extreme market conditions
- test prompt injection
- test output schema violations

Do not evaluate AI only on profitable trades.

Also evaluate:
- false positives
- false negatives
- risk-adjusted behavior
- stability
- calibration
- disagreement
- abstention behavior
- data-quality sensitivity

---

# 5.24 STRATEGY LIFECYCLE

Strategy states:

DRAFT
BACKTESTING
PAPER_TRADING
INTERNAL_REVIEW
RISK_REVIEW
APPROVED
CANARY
ACTIVE
PAUSED
DEGRADED
RETIRED

No strategy moves directly from DRAFT to ACTIVE.

Production strategy versions are immutable.

Create a new version rather than silently editing an active version.

---

# 5.25 BACKTESTING

Backtests must disclose:

- dataset period
- source
- fees
- slippage assumptions
- spread assumptions
- funding assumptions
- latency assumptions
- leverage
- position sizing
- stop rules
- take-profit rules
- survivorship assumptions
- look-ahead bias controls

Avoid cherry-picked periods.

Store the exact configuration used.

Backtest results are not guarantees.

---

# 5.26 PAPER TRADING

Before live activation:

- simulated account
- realistic fees
- realistic latency
- realistic slippage
- order rejection simulation
- partial fill simulation
- exchange downtime simulation
- stale data simulation

Paper trading results must be visually distinguished from live performance.

---

# 5.27 CANARY DEPLOYMENT

New strategies should be eligible for:

- internal users
- controlled percentage
- limited instruments
- limited capital/risk budget
- time-limited activation

Automatically pause if predefined guardrails trigger.

---

# 5.28 RISK POLICY VERSIONING

Every risk decision references:

risk_policy_id
risk_policy_version

A policy change does not rewrite historical decisions.

Historical decisions remain explainable.

Example:

RISK_POLICY_V12:
max_account_risk = configured
max_position_risk = configured
max_daily_loss = configured
max_leverage = configured
max_concentration = configured

All values are configuration, not hard-coded magic numbers.

---

# 5.29 POSITION SIZING

Position sizing must be a deterministic function.

Inputs:
account equity
risk budget
stop distance
instrument specification
volatility
fees
slippage
leverage
margin requirements
existing exposure

Output:
recommended quantity
maximum permitted quantity

If the calculated quantity is below exchange minimum:
NO_TRADE.

If margin requirement exceeds available margin:
NO_TRADE.

If risk cannot be calculated:
NO_TRADE.

---

# 5.30 CUSTOMER RISK PROFILE

Potential profiles:

CONSERVATIVE
BALANCED
AGGRESSIVE

The profile must not be used to promise performance.

It may be used to configure permitted risk boundaries.

Users should understand:
“This profile changes the system's risk constraints. It does not predict returns.”

---

# 5.31 CUSTOMER ONBOARDING

Flow:

WELCOME
LANGUAGE
COUNTRY
LEGAL AGE / ELIGIBILITY
ACCOUNT
EMAIL VERIFICATION
SECURITY
RISK DISCLOSURE
PRODUCT EDUCATION
PRODUCT SELECTION
SUBSCRIPTION
PAYMENT
CONNECTION
VALIDATION
ACTIVATION

Do not collect information that is not needed.

Where regulated onboarding requires identity verification, route through the appropriate KYC provider/process.

---

# 5.32 SUBSCRIPTION UX

Terms:

7 DAYS
1 MONTH
3 MONTHS
6 MONTHS
12 MONTHS

Do not artificially make longer plans appear risk-free.

Display:

duration
price
effective periodic price where lawful
renewal behavior
cancellation
refund policy
included capability
excluded capability
market mode
region restrictions

If all plans have identical capability and differ only by duration, make that explicit.

---

# 5.33 CHECKOUT

Checkout must show:

product
market mode
term
price
currency
tax where applicable
renewal
payment method
legal terms
risk disclosure
refund policy
jurisdiction availability

The purchase button must state the action clearly.

Example:
“Start 3-Month Subscription — €X”

Not:
“Let's Go!”

---

# 5.34 ENTITLEMENT ENGINE

Entitlement is a server-side calculation.

Inputs:
subscription status
payment status
product
market mode
term
jurisdiction
platform
account status
compliance status

Output:
capability set

Example:

signals.crypto.spot = ENABLED
signals.crypto.futures = ENABLED
automation.crypto.spot = ENABLED
automation.crypto.futures = DISABLED
binary_options = DISABLED

Frontend receives capability flags but backend rechecks them.

---

# 5.35 FEATURE FLAGS

Flags must have:

key
description
owner
default
environment
targeting
start
expiry
audit
rollback

Never use feature flags as permanent hidden architecture.

Every temporary flag must have an expiry date.

Sensitive flags require privileged access.

---

# 5.36 ADMIN DASHBOARD INFORMATION ARCHITECTURE

HOME
- system health
- active incidents
- subscriptions
- payment alerts
- exchange health
- execution health
- AI health
- content status

CUSTOMERS
SUBSCRIPTIONS
PAYMENTS
ENTITLEMENTS
EXCHANGE CONNECTIONS
SIGNALS
STRATEGIES
RISK
EXECUTION
AI
CONTENT
NOTIFICATIONS
AUDIT
COMPLIANCE
FEATURE FLAGS
SYSTEM SETTINGS
SUPPORT
INCIDENTS

Sensitive information is minimized by default.

---

# 5.37 ADMIN MOBILE APP

The Android admin application must be a privileged operations console.

It should support:
- secure login
- biometric step-up
- dashboards
- customer search
- subscription status
- payment state
- entitlement state
- exchange connection health
- strategy pause
- signal pause
- incident response
- notifications
- audit review

It should NOT:
- display exchange secrets
- allow arbitrary SQL
- allow arbitrary code execution
- allow unreviewed production configuration
- provide a “master override” that bypasses risk controls

---

# 5.38 CUSTOMER DASHBOARD

Top-level cards:

ACCOUNT STATUS
SUBSCRIPTION
MARKET ACCESS
CONNECTION HEALTH
RISK STATUS
SIGNAL STATUS
AUTOMATION STATUS

Use calm visual hierarchy.

Do not turn a financial dashboard into a casino-style interface.

Avoid:
- flashing green/red
- excessive confetti
- fake urgency
- profit gamification
- countdowns that pressure decisions

---

# 5.39 CONNECTION CENTER

Each connection card shows:

Provider
Account reference
Status
Permissions
Last health check
Last successful sync
Supported modes
Security recommendations

Actions:

CONNECT
REVALIDATE
DISCONNECT
REPLACE CREDENTIAL
VIEW PERMISSIONS

Secret values remain masked permanently.

---

# 5.40 ORDER HISTORY

Columns:

timestamp
instrument
direction
quantity
entry
exit
status
fees
slippage
strategy
signal ID
execution ID

Users must be able to inspect why an order was rejected.

Example:

“Rejected — risk limit exceeded.”

Not:

“Something went wrong.”

---

# 5.41 SIGNAL DETAIL

Show:

instrument
direction
signal age
signal strength
confidence
data quality
risk level
strategy
entry framework
stop framework
target framework
consensus
evidence
expiration

Avoid revealing proprietary strategy implementation details unless intentionally configured.

---

# 5.42 TRANSPARENCY MODE

Every signal should optionally provide:

WHY THIS SIGNAL EXISTS
WHAT DATA WAS USED
WHAT COULD INVALIDATE IT
WHAT RISK CHECKS PASSED
WHAT RISK CHECKS FAILED
WHEN IT EXPIRES

This is a trust-building feature.

---

# 5.43 NOTIFICATION SYSTEM

Channels:

in-app
push
email
optional SMS where justified

Notification priorities:

SECURITY
TRADING
PAYMENT
SUBSCRIPTION
SYSTEM
MARKET
CONTENT

Security notifications should not contain sensitive credentials.

Trading notifications should clearly distinguish:
signal generated
order submitted
order acknowledged
order filled
order rejected
order unknown
reconciliation completed

---

# 5.44 NOTIFICATION DEDUPLICATION

Every notification has:

notification_id
event_id
channel
recipient
template_version
sent_at
delivery_status

Do not send repeated alerts because of duplicate events.

---

# 5.45 BLOG ENGINE

Content types:

ARTICLE
GUIDE
MARKET EXPLAINER
PRODUCT GUIDE
RISK GUIDE
NEWS ANALYSIS
GLOSSARY
FAQ
CASE STUDY — only if genuine and verifiable

Every article:
slug
title
summary
content
author
reviewer
status
locale
canonical
published_at
updated_at
sources
tags

---

# 5.46 SEO ARCHITECTURE

Public content must support:

canonical URLs
hreflang
sitemap
robots directives
structured data where appropriate
Open Graph
Twitter/X metadata where applicable
fast server-rendered content
semantic headings
internal links
breadcrumb data where appropriate

Persian and English URLs should be deliberately designed.

Avoid automatic machine-generated thousands of low-value pages.

---

# 5.47 INTERNATIONALIZATION DATA MODEL

Every translatable content item has:

content_id
locale
title
summary
body
seo_title
seo_description
status
translation_version
updated_at

Do not treat translation as string substitution only.

Persian editorial content should be reviewed for natural Persian.

English content should be written naturally in English.

---

# 5.48 DESIGN TOKEN ARCHITECTURE

Define:

color.brand.*
color.background.*
color.surface.*
color.text.*
color.border.*
color.status.*
color.market.*
spacing.*
radius.*
shadow.*
blur.*
motion.*
duration.*
easing.*
typography.*

Tokens must have semantic names.

Avoid components using raw hex values.

---

# 5.49 MOTION SYSTEM

Animation categories:

MICRO
SECTION
PAGE
HERO
DATA

Every animation has:

duration
easing
trigger
reduced-motion behavior

Luxury animation must never block interaction.

For 360° product visuals:
- use optimized frame sequences
- lazy-load
- preload only near viewport
- provide poster image
- provide reduced-motion alternative
- preserve touch and scroll control
- do not hijack scrolling indefinitely

---

# 5.50 PERFORMANCE BUDGET

Define budgets before implementation.

Public landing:
- strict JavaScript budget
- optimized images
- minimal third-party scripts
- fast content render

Dashboard:
- prioritize data interaction
- virtualize large tables
- debounce filters
- stream only required data

Admin:
- prioritize reliability and security over decoration

---

# 5.51 OFFLINE / PWA

Offline support must be conservative.

Safe offline capabilities:
- static shell
- cached documentation
- previously viewed non-sensitive content
- settings UI

Do NOT cache:
- exchange secrets
- sensitive financial state unless explicitly designed and encrypted
- privileged admin data
- mutable trading commands

If market connectivity is lost:
show OFFLINE / STALE.

Never imply live trading is active while offline.

---

# 5.52 MOBILE APP ARCHITECTURE

Use a mature cross-platform stack capable of targeting:
Android
iOS
Web
Windows
macOS

The implementation must still respect platform-specific behavior.

Do not assume:
- identical permissions
- identical notification behavior
- identical background execution
- identical store billing
- identical cryptography APIs

Use platform adapters.

---

# 5.53 DESKTOP APPLICATION

Desktop should provide:

- dashboard
- signals
- account
- connections
- settings
- notifications
- blog/documentation

If automated trading requires a broker bridge or local MT5 runtime, document the dependency explicitly.

Do not pretend the desktop client itself is the execution engine.

---

# 5.54 MOBILE STORE STRATEGY

Create a platform capability matrix.

Columns:

feature
web
PWA
Android
iOS
Windows
macOS

Rows:
signals
crypto spot
crypto derivatives
forex
automation
binary-options-like
subscriptions
payments
admin
blog
support

Each cell:
AVAILABLE
LIMITED
UNAVAILABLE
REQUIRES_REVIEW
REGION_DEPENDENT

This matrix is generated from policy configuration, not hard-coded UI assumptions.

---

# 5.55 APP STORE SUBMISSION PACKAGE

Maintain:

app description
privacy policy
terms
support URL
marketing assets
screenshots
demo account where required
review notes
financial licensing documents where applicable
feature declarations
data-safety information
permission justification
age rating
contact information

Never submit a binary-options trading feature to a store that explicitly prohibits it.

---

# 5.56 RELEASE CHANNELS

Environments:

LOCAL
DEV
TEST
STAGING
PREPROD
PRODUCTION

Production access requires controlled approval.

Use:
feature flags
canary releases
rollback
database migration strategy
observability gates

---

# 5.57 CI/CD

Pipeline:

lint
type-check
unit tests
integration tests
security scan
dependency scan
secret scan
build
artifact signing
E2E
accessibility tests
localization tests
migration validation
deploy staging
smoke tests
approval
production deployment
post-deploy verification

No secrets in source control.

---

# 5.58 INFRASTRUCTURE AS CODE

All production infrastructure must be reproducible.

Define:
network
IAM
databases
queues
storage
secrets integration
monitoring
alerts
DNS
CDN
WAF
autoscaling

Manual production changes must be exceptional and audited.

---

# 5.59 CLOUD IAM

Use least privilege.

Separate:
developer
CI
runtime
operations
security
billing

Runtime services should have only the permissions required.

Do not use one universal cloud credential.

Rotate credentials.

Use workload identity where supported.

---

# 5.60 WAF / EDGE SECURITY

Protect public endpoints with:

rate limiting
bot controls where appropriate
WAF rules
request-size limits
TLS
DDoS protections
API authentication
abuse detection

Do not block legitimate financial data clients through overly aggressive rules.

---

# 5.61 RATE LIMITING

Apply limits per:

IP
user
device
API key
endpoint
operation
risk level

High-cost endpoints get stricter limits.

Sensitive operations may require step-up verification instead of simply returning 429.

---

# 5.62 WEBHOOK SECURITY

For every provider webhook:

verify signature
verify timestamp
verify event ID
reject replay
validate schema
store raw payload in controlled storage if retention is justified
process idempotently
emit internal event
audit result

Never trust:
status
amount
currency
user ID

until the signature and provider event are verified.

---

# 5.63 PAYMENT RECONCILIATION

Run periodic reconciliation:

provider records
internal payments
subscriptions
entitlements

Detect:
paid_without_entitlement
entitlement_without_payment
duplicate_payment
duplicate_refund
late_webhook
chargeback
currency_mismatch
amount_mismatch

Never silently fix financial mismatches.

Create a case for review.

---

# 5.64 SUPPORT SYSTEM

Customer support agents should see:

account status
subscription
entitlement
connection health
recent errors
safe audit summary

They should not see:
exchange secrets
authentication secrets
private keys

Support actions:
reset MFA workflow
resend verification
cancel subscription
initiate approved refund workflow
disconnect exchange
create support case

High-risk actions require privileged approval.

---

# 5.65 INCIDENT RESPONSE

Severity:

SEV0 — catastrophic
SEV1 — critical
SEV2 — major
SEV3 — minor
SEV4 — informational

Trading-specific incident examples:

wrong orders
duplicate orders
incorrect risk calculation
stale data execution
exchange reconciliation failure
credential compromise
entitlement bypass
payment fraud
AI safety failure

Immediate trading safety action:
pause new execution where necessary.

Then:
preserve evidence
identify scope
reconcile state
communicate
remediate
test
resume gradually

---

# 5.66 INCIDENT COMMUNICATION

Never hide material incidents.

Customer messaging must distinguish:

SERVICE INTERRUPTION
SECURITY INCIDENT
TRADING EXECUTION ISSUE
PAYMENT ISSUE
MARKET DATA ISSUE

Do not speculate before facts are known.

Use:
“What we know”
“What we are investigating”
“What users should do”
“Current system state”
“Next update”

---

# 5.67 SECURITY INCIDENT DATA

Potentially sensitive incident details should not be placed into public logs.

Public communication should not expose:
attack paths
credentials
security bypass details
customer PII
internal infrastructure

Maintain internal forensic records separately.

---

# 5.68 DATA RETENTION

Define retention by category:

account data
financial records
audit data
market data
AI inputs
AI outputs
support tickets
logs
analytics

Retention must be based on:
legal requirement
business need
security risk
privacy principle

Do not retain everything forever.

---

# 5.69 PRIVACY BY DESIGN

Collect only necessary data.

Document:
purpose
legal basis where applicable
retention
sharing
processor
storage region
user rights
deletion workflow

Customer should be able to understand how exchange credentials and financial information are handled.

---

# 5.70 DATA DELETION

Deletion must be state-aware.

A user deletion request may interact with:
- financial record retention
- legal retention
- audit retention
- fraud prevention
- support records

Do not simply cascade-delete all records.

Use:
DELETE
ANONYMIZE
RETAIN_FOR_LEGAL_REASON
RESTRICT_ACCESS

as appropriate.

---

# 5.71 ANALYTICS

Track product behavior without collecting unnecessary sensitive data.

Events:

page_view
pricing_view
checkout_started
checkout_completed
connection_started
connection_validated
signal_viewed
strategy_enabled
order_created
order_filled
support_opened

Never send:
API secrets
full payment details
authentication tokens
private financial credentials

---

# 5.72 EXPERIMENTATION

A/B testing must not manipulate users into risky financial behavior.

Do not experiment on:
risk disclosure visibility
critical warning visibility
stop-loss controls
consent
withdrawal warnings
legal acceptance

without appropriate governance.

---

# 5.73 ANTI-DARK-PATTERN POLICY

Prohibited UX patterns:

hidden renewal
misleading cancellation
fake countdown
fake user activity
fake profitability
preselected high-risk leverage
hidden risk disclosure
confusing unsubscribe
forced social sharing
misleading “safe” labels
fake AI certainty

Premium design should increase clarity, not pressure.

---

# 5.74 ACCESSIBILITY ACCEPTANCE

Test:

keyboard-only
screen reader
200% zoom
400% zoom where feasible
reduced motion
high contrast
touch target size
focus order
RTL
LTR
mobile landscape
desktop
tablet

Critical flows:
signup
subscription
connection
signal inspection
risk disclosure
account settings
support

must be accessible.

---

# 5.75 SECURITY TEST MATRIX

Create automated tests for:

BOLA
broken authentication
broken function authorization
mass assignment
rate-limit bypass
SSRF
XSS
CSRF
SQL injection
NoSQL injection
path traversal
file upload
webhook replay
payment replay
idempotency collision
session fixation
privilege escalation
secret leakage
log injection
prompt injection
unsafe deserialization
dependency vulnerabilities

Map each test to a risk ID.

---

# 5.76 PENETRATION TESTING

Before public launch:

- external penetration test
- API assessment
- mobile assessment
- web assessment
- cloud configuration review
- authentication review
- authorization review
- secrets review

Remediate critical/high findings before production.

Maintain evidence.

---

# 5.77 MOBILE SECURITY

Android/iOS applications must:

- use secure storage
- minimize permissions
- use TLS
- validate certificates appropriately
- avoid secrets in logs
- prevent sensitive screenshots where appropriate
- protect deep links
- validate universal/app links
- verify remote configuration
- protect local cache
- handle device compromise gracefully

Never assume mobile application binaries are secret.

Anything in the client can be reverse-engineered.

---

# 5.78 ADMIN APP HARDENING

Because the admin app is a high-value target:

- separate application identity
- separate API audience
- strict device enrollment
- short sessions
- privileged audit
- biometric confirmation
- remote revocation
- jailbreak/root risk signal where appropriate
- minimum supported OS
- forced updates for critical security fixes

The admin API must independently enforce authorization.

---

# 5.79 REMOTE CONFIGURATION

Remote configuration must be:

- signed/authenticated
- versioned
- validated
- auditable
- rollbackable

Never allow an unsigned remote JSON file to change trading risk rules.

Risk policies require stronger controls than visual configuration.

---

# 5.80 CONFIGURATION HIERARCHY

GLOBAL
REGION
PLATFORM
PRODUCT
PROVIDER
STRATEGY
CUSTOMER
ACCOUNT

The most restrictive applicable rule wins.

Example:

Global automation = ON
Region automation = ON
Platform automation = OFF
Result = OFF

Never allow a lower-level configuration to bypass a higher-level prohibition.

---

# 5.81 POLICY ENGINE

Policy decisions should be explainable.

Return:

decision
policy_version
matched_rules
reason_codes
effective_at

Example:

decision = NOT_ELIGIBLE
reason = PLATFORM_RESTRICTION
policy_version = FIN-2026-08
feature = BINARY_OPTIONS

Do not expose sensitive internal rules to end users beyond what is useful.

---

# 5.82 FINANCIAL CALCULATION ENGINE

Never perform critical financial calculations using binary floating point where precision matters.

Use:
decimal arithmetic
fixed-point where appropriate
provider-defined precision

Test:
rounding
fees
taxes
minimum quantities
tick sizes
lot sizes
currency conversion
negative values
zero values
extreme values

Every monetary calculation must specify currency and precision.

---

# 5.83 CURRENCY HANDLING

Never assume USD.

Support:
USD
EUR
GBP
and configured currencies where business requirements justify them.

Every amount:
value
currency
scale
timestamp where FX conversion is involved
rate source if converted

Display conversion source/time where meaningful.

---

# 5.84 TIME & TIMEZONE

Store timestamps in UTC.

Render according to user preference.

Market sessions may use provider/exchange timezone.

Never compare local strings.

Use explicit timezone identifiers.

Persian calendar display may be offered as a presentation option, but financial event ordering remains based on canonical timestamps.

---

# 5.85 ORDER RECONCILIATION

Reconciliation compares:

internal order
provider order
provider trade/fill
position
balance

Detect:
missing order
unknown provider order
quantity mismatch
price mismatch
fill mismatch
fee mismatch
position mismatch

Any mismatch becomes:

RECONCILIATION_REQUIRED

and cannot be silently ignored.

---

# 5.86 BALANCE RECONCILIATION

Compare:
available balance
locked balance
equity
margin
provider balance

Never present stale balances as live.

Show:
LAST UPDATED
DATA AGE

---

# 5.87 EXCHANGE OUTAGE MODE

When provider health fails:

- stop new orders if configured
- retain existing state
- attempt read-only reconciliation
- notify operations
- show customer degraded status
- do not fabricate balances
- do not fabricate fills

Recovery:
health restored
authentication validated
market data current
orders reconciled
positions reconciled
risk recalculated
then resume.

---

# 5.88 DATA QUALITY SCORE

Every market-dependent decision receives a data quality score based on:

freshness
completeness
provider health
cross-source consistency
sequence integrity
latency
missing values

If quality falls below threshold:
NO_TRADE or reduced service according to policy.

---

# 5.89 NEWS & FUNDAMENTAL PIPELINE

Stages:

INGEST
NORMALIZE
DEDUPLICATE
SOURCE-QUALITY
TIMESTAMP
LANGUAGE
ENTITY RESOLUTION
SENTIMENT/FACT EXTRACTION
EVENT CLASSIFICATION
CONFLICT DETECTION
MODEL ANALYSIS
HUMAN/QUALITY REVIEW where required
PUBLISH TO DECISION LAYER

Never treat a headline as verified fact merely because an AI summarized it.

---

# 5.90 SOURCE PROVENANCE

For every fundamental/news input retain:

source
publisher
URL/reference
published_at
retrieved_at
language
content_hash
classification
confidence

The AI can cite source references internally.

---

# 5.91 MARKET EVENT CALENDAR

Support:

economic events
earnings where applicable
central bank events
macro releases
exchange maintenance
provider maintenance

The system may use events as risk context.

Do not claim the event calendar predicts price direction.

---

# 5.92 TRADING JOURNAL

For each decision:

signal
risk decision
execution
result
market state
strategy version
AI evidence
data quality

Users can see a human-readable journal.

Example:

“Signal created at 10:32 UTC. Risk engine approved a quantity below the strategy maximum. Execution completed at 10:32:04 UTC.”

This builds trust.

---

# 5.93 AI JOURNAL

Record:
agent
model
input snapshot
output
confidence
data quality
decision impact
final policy result

Do not expose chain-of-thought or private internal reasoning.

Expose concise reasons and evidence instead.

---

# 5.94 PERFORMANCE REPORTING

If performance is shown:

distinguish:
realized
unrealized
gross
net
fees
funding
slippage

Define period:
day
week
month
year
since inception

Never cherry-pick.

Provide methodology.

---

# 5.95 RISK REPORTING

Show:

maximum drawdown
volatility
exposure
concentration
largest loss
largest win
loss streak
position count
leverage where applicable

Avoid presenting only wins.

---

# 5.96 USER EXPORT

Users may export appropriate data:

account
subscriptions
payments
signals
orders
positions
journal

Formats:
CSV
JSON
PDF where useful

Sensitive exports require re-authentication.

---

# 5.97 ADMIN EXPORT

Admin exports require:

purpose
permission
audit
time range
minimum necessary fields

No unrestricted “export all customer data” button for ordinary administrators.

---

# 5.98 SEARCH

Global search must respect authorization.

Search index must not become an authorization bypass.

If a customer searches:
only their data.

If admin searches:
only data permitted by role.

---

# 5.99 CACHE POLICY

Cache:
public content
non-sensitive metadata
static assets

Be careful caching:
financial state
entitlements
permissions
signals
orders

Never cache secrets.

Use explicit cache invalidation for entitlement changes.

---

# 5.100 QUEUE POLICY

Use queues for:
notifications
news processing
AI analysis
analytics
reconciliation
non-blocking sync

Financial execution queues require stronger guarantees and observability.

Do not let a generic queue silently retry an order creation indefinitely.

---

# 5.101 DEAD-LETTER QUEUES

Every asynchronous domain needs a dead-letter strategy.

DLQ records:
event ID
attempt count
error
first failure
last failure
service version

Financial DLQs require operational alerts.

---

# 5.102 RETRY POLICY

Retry only when:
error is transient
operation is idempotent
provider semantics are understood

Do not retry:
invalid credentials
permission denied
invalid order
risk rejection
compliance rejection

unless the underlying condition changes.

---

# 5.103 CIRCUIT BREAKERS

Use circuit breakers for:
exchange APIs
payment APIs
AI APIs
news providers
notification providers

States:
CLOSED
OPEN
HALF_OPEN

When open:
fail safely.

---

# 5.104 AI PROVIDER FAILOVER

Provider A fails:
- use provider B only if approved
- preserve policy
- preserve schema
- preserve risk controls
- record provider change

If no provider meets minimum quality:
NO_TRADE or degraded signal mode.

Do not automatically switch to an untested model.

---

# 5.105 MODEL VERSIONING

Store:

provider
model
version
deployment ID
prompt policy
tool policy
evaluation score
release date
rollback version

A signal must be reproducible enough to identify which model configuration participated.

---

# 5.106 COST CONTROL

Track AI:
tokens
requests
latency
cost
failure
cache hit
provider

Do not allow an AI loop to create uncontrolled spending.

Set:
per-user budget
per-agent budget
global budget
alert thresholds

---

# 5.107 OBSERVABILITY DASHBOARDS

Dashboard 1:
PLATFORM HEALTH

Dashboard 2:
TRADING HEALTH

Dashboard 3:
EXCHANGE HEALTH

Dashboard 4:
PAYMENT HEALTH

Dashboard 5:
AI HEALTH

Dashboard 6:
SECURITY

Dashboard 7:
CONTENT / SEO

Dashboard 8:
PWA / MOBILE

Dashboard 9:
ADMIN OPERATIONS

---

# 5.108 SLOs

Define service-level objectives per domain.

Examples:

API availability
signal latency
market-data freshness
payment webhook processing
notification delivery
order reconciliation

Do not promise 100% uptime publicly unless a contractual SLA and operational architecture actually support the claim.

---

# 5.109 STATUS PAGE

Public status page categories:

Website
Dashboard
Signals
Trading connectivity
Payments
Mobile
Notifications

Avoid revealing sensitive provider architecture.

---

# 5.110 FEATURE DEPRECATION

Every deprecated feature has:

announcement
replacement
migration
deadline
support path
final removal date

Never remove financial history because a feature was deprecated.

---

# 5.111 API DEPRECATION

Version APIs.

Support a defined deprecation window.

Return:
Deprecation
Sunset

headers where appropriate.

Publish migration documentation.

---

# 5.112 MOBILE VERSION SUPPORT

Maintain a supported OS matrix.

Security-critical minimum versions may be increased.

Communicate:
current version
minimum supported version
recommended version

Force update only when justified.

---

# 5.113 WEB BROWSER SUPPORT

Define:
supported browsers
minimum versions
degraded behavior

Do not block users merely because a decorative animation is unsupported.

---

# 5.114 ADMIN APPROVAL WORKFLOW

For sensitive configuration:

REQUEST
REVIEW
APPROVE
APPLY
VERIFY

Record:
requester
reviewer
reason
before
after
policy
timestamp

For dual-control operations:
requester cannot approve their own request.

---

# 5.115 STRATEGY APPROVAL WORKFLOW

DRAFT
TECHNICAL REVIEW
BACKTEST REVIEW
RISK REVIEW
COMPLIANCE REVIEW
PAPER TRADING
CANARY
PRODUCTION

Every transition is auditable.

---

# 5.116 INCIDENT-DRIVEN AUTOMATIC PAUSE

Automatic pause conditions may include:

extreme data staleness
abnormal spread
repeated exchange rejects
reconciliation mismatch
risk calculation failure
unexpected leverage
strategy drawdown breach
provider outage
security incident

Automatic pause should be conservative.

---

# 5.117 RESUME WORKFLOW

Never resume blindly.

Require:

health check
data freshness
provider authentication
reconciliation
risk validation
operator or policy approval where configured

---

# 5.118 LEGAL / COMPLIANCE DOCUMENT ENGINE

Documents:

Terms of Service
Privacy Policy
Risk Disclosure
Subscription Terms
Refund Policy
Cookie Policy
Trading Disclaimer
AI Disclosure
Data Processing Information
Supported Jurisdictions
Restricted Countries
Complaint Procedure

Version each document.

Store acceptance:
document_id
version
user_id
timestamp
locale

---

# 5.119 RISK DISCLOSURE UX

Before activation, explain:

capital loss is possible
market volatility
leverage risk
liquidation risk where applicable
provider risk
technology risk
connectivity risk
slippage
fees
funding
model risk
AI error
past performance limitations

Use plain language.

Do not hide the disclosure behind a tiny link.

---

# 5.120 AI DISCLOSURE

Example:

“Our platform uses automated analytical systems and AI-assisted components. AI outputs can be inaccurate, incomplete, delayed, or wrong. AI does not guarantee trading outcomes. Risk and policy controls remain independent of AI recommendations.”

Do not say:
“Our AI knows where the market is going.”

---

# 5.121 MARKETING CLAIM GOVERNANCE

Claims requiring evidence:

AI uptime
signal accuracy
win rate
returns
drawdown
number of analysts
number of users
assets managed
execution speed
security certifications
licenses
partnerships

Every claim has:
claim ID
owner
evidence
date verified
expiry
approved wording

---

# 5.122 BRAND VOICE

Tone:
confident
premium
calm
technical
transparent
human

Avoid:
aggressive hype
fear
gambling language
guarantees
false urgency

The product should communicate:
precision
control
clarity
discipline
technology
trust

---

# 5.123 UI COPY RULE

Never translate English word-for-word when Persian idiomatic language is better.

For every critical flow maintain:
English source
Persian reviewed translation
context
character constraints
UI screenshot review

---

# 5.124 DESIGN QA

Before release inspect:

desktop
tablet
mobile
RTL
LTR
dark
light
high contrast
reduced motion
slow network
offline
long text
short text
large numbers
negative values
zero values
missing data
error states

---

# 5.125 VISUAL REGRESSION

Use screenshot tests for:

landing
pricing
checkout
dashboard
signals
connection center
settings
blog
admin

Test:
English
Persian
light
dark
mobile
desktop

---

# 5.126 API CONTRACT TESTING

Consumer-driven contracts must verify:

schema
authorization
error behavior
idempotency
pagination
sorting
localization
versioning

Breaking API changes require explicit migration.

---

# 5.127 DATABASE MIGRATIONS

Rules:
backward-compatible first
deploy application
migrate data
switch reads
switch writes
remove old schema later

Never combine irreversible destructive migrations with an untested release.

---

# 5.128 SEED DATA

Seed only non-production test data.

Production must never contain:
fake customers
fake payments
fake trades
fake testimonials

unless clearly marked synthetic in a dedicated test environment.

---

# 5.129 DEMO MODE

If a demo environment is provided:

- clearly labeled
- synthetic funds
- synthetic execution
- no real exchange credentials
- no real financial transactions

Never confuse demo with live.

---

# 5.130 SUPPORT FOR MULTIPLE PROVIDERS

Provider abstraction must prevent vendor lock-in.

Every provider implements:
capabilities
health
authentication
limits
error mapping
rate limits
market mapping

Normalize provider errors into internal codes.

Preserve provider-specific details internally.

---

# 5.131 PROVIDER ERROR TAXONOMY

Examples:

AUTH_INVALID
AUTH_EXPIRED
PERMISSION_DENIED
RATE_LIMITED
TEMPORARY_UNAVAILABLE
MARKET_CLOSED
INVALID_SYMBOL
INVALID_QUANTITY
INSUFFICIENT_MARGIN
ORDER_REJECTED
ORDER_UNKNOWN
NETWORK_ERROR
DATA_STALE

Map to user-safe messages.

---

# 5.132 CUSTOMER-FACING ERROR EXAMPLES

AUTH_INVALID:
“Your connection could not be authenticated. No order was submitted.”

INSUFFICIENT_MARGIN:
“The requested position exceeds the available margin.”

DATA_STALE:
“Market data is currently delayed. Automated execution is paused.”

ORDER_UNKNOWN:
“We could not yet confirm the exchange's order status. We will reconcile it before allowing a duplicate submission.”

---

# 5.133 CUSTOMER SAFETY CENTER

Provide:

connected exchanges
permission audit
recent security events
sessions
MFA
passkeys
notifications
API connection status
risk profile
subscription status

One place to understand account safety.

---

# 5.134 SECURITY NOTIFICATION CENTER

Events:
new login
new device
MFA change
passkey added
password reset
exchange connected
exchange disconnected
API permission changed
subscription changed
payment changed
admin support action

High-risk notifications cannot be silently disabled.

---

# 5.135 ACCOUNT RECOVERY

Recovery must balance:
security
availability
fraud prevention

Do not make recovery weaker than login.

High-risk accounts may require manual review.

---

# 5.136 FRAUD SIGNALS

Potential indicators:
unusual login
new device
country change
rapid payment attempts
multiple cards
chargeback
credential changes
sudden trading behavior
API connection churn

Fraud scoring may trigger:
step-up authentication
temporary hold
manual review

Never automatically accuse users.

---

# 5.137 CUSTOMER COMMUNICATION PREFERENCES

Separate:
transactional
security
trading
marketing

Security and critical transactional messages cannot be disabled if required for safe operation.

---

# 5.138 DOCUMENTATION

Public docs:

Getting Started
Security
Subscriptions
Connections
Signals
Automated Trading
Risk
FAQ
Troubleshooting
Supported Providers
Platform Availability

Developer docs:
API
Webhooks
SDK
Schemas
Errors
Authentication
Rate Limits

---

# 5.139 DEVELOPER PORTAL

If an external API is eventually offered:

- API keys
- OAuth where possible
- scopes
- rate limits
- sandbox
- documentation
- webhooks
- SDKs
- changelog

Never expose internal trading-control endpoints as public APIs by accident.

---

# 5.140 SANDBOX

Sandbox must:
- use fake funds
- fake or delayed market data where necessary
- simulate provider failures
- simulate payment states
- simulate risk rejection

Never let sandbox credentials reach production.

---

# 5.141 SECURITY SCANNING

CI must scan:
source
dependencies
containers
IaC
secrets
licenses

Security findings must be categorized.

Critical issues block release.

---

# 5.142 DEPENDENCY GOVERNANCE

Maintain:
dependency inventory
versions
licenses
CVE status
owner
upgrade policy

Avoid unmaintained packages.

---

# 5.143 SUPPLY CHAIN

Use:
lockfiles
signed artifacts where supported
trusted registries
dependency pinning
provenance
build isolation

Never execute arbitrary package lifecycle scripts without review.

---

# 5.144 ARTIFACT SIGNING

Production:
mobile builds
desktop builds
backend images

must be signed where feasible.

Keep signing keys in secure systems.

Never store signing keys in source control.

---

# 5.145 SECRETS ROTATION

Rotate:
cloud credentials
CI tokens
database credentials
provider credentials
signing credentials

Customer exchange credentials are rotated by the customer/provider workflow where applicable.

---

# 5.146 LOGGING POLICY

Logs may contain:
request ID
service
operation
status
latency
safe identifiers

Logs must not contain:
API secret
password
private key
session token
full payment instrument
sensitive KYC documents

---

# 5.147 REDACTION

Implement central redaction.

Patterns:
Authorization headers
API keys
JWTs
private keys
secrets
payment data
passwords

Test redaction automatically.

---

# 5.148 DATA MASKING

UI:
email -> partially masked where appropriate
account ID -> partial
connection reference -> partial

Never mask data in a way that prevents legitimate support work.

Never reveal secrets as a support convenience.

---

# 5.149 ADMIN AUDIT VIEW

Filters:
actor
customer
action
resource
date
severity
result
correlation ID

Show:
what
who
when
why
result

Sensitive payloads remain redacted.

---

# 5.150 FINAL ENGINEERING ACCEPTANCE GATE

The platform is not production-ready until:

[ ] Architecture boundaries documented
[ ] Threat model completed
[ ] API security reviewed
[ ] RBAC tested
[ ] Secrets isolated
[ ] Payment webhooks verified
[ ] Entitlements server-authoritative
[ ] Exchange adapters tested
[ ] Order idempotency tested
[ ] Unknown-order reconciliation tested
[ ] Risk engine deterministic
[ ] AI cannot bypass controls
[ ] Strategy lifecycle enforced
[ ] Market-data staleness handled
[ ] Incident runbooks written
[ ] Backups tested
[ ] Disaster recovery tested
[ ] Accessibility tested
[ ] RTL/LTR tested
[ ] Localization reviewed
[ ] Mobile builds tested
[ ] Admin app hardened
[ ] Store policy matrix reviewed
[ ] Jurisdiction matrix reviewed
[ ] Legal documents versioned
[ ] Marketing claims verified
[ ] Security scan passed
[ ] Penetration test completed
[ ] Monitoring active
[ ] Rollback tested
[ ] Support process operational
[ ] No prohibited store functionality is being disguised
[ ] No secret is visible to administrators
[ ] No AI claim implies guaranteed performance
[ ] No financial outcome is represented as guaranteed

---

# 5.151 MASTER RULE

When a requirement conflicts with security, law, platform policy, or safe execution:

SECURITY / LAW / PLATFORM POLICY / SAFE EXECUTION wins over visual preference, growth preference, or convenience.

When uncertain:

STOP
MARK AS UNKNOWN
RESEARCH OFFICIAL SOURCE
DOCUMENT ASSUMPTION
IMPLEMENT SAFE DEFAULT
REQUEST REQUIRED BUSINESS/LEGAL DECISION
TEST THE DECISION
THEN CONTINUE

Never fill an unknown regulatory or technical fact with an invented assumption.

END OF PART 5
