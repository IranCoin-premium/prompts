# TRADING PLATFORM MASTER PROMPT
# PART 8 — DATABASE SCHEMA, API CONTRACTS, EVENT SCHEMAS, WEBSOCKET PROTOCOLS, WEBHOOKS & ERROR MODEL

CONTINUATION OF PARTS 1–7

This part defines implementation-grade contracts for the transactional database, REST APIs, real-time APIs, asynchronous events, provider webhooks, validation, idempotency, pagination, error handling, versioning, observability, and backward compatibility.

IMPORTANT:
- This document is a technical specification, not legal advice.
- Current provider, regulator, app-store, payment, exchange, broker and SDK documentation must be rechecked before production.
- The frontend is never a trust boundary.
- The backend is authoritative for identity, authorization, entitlement, compliance, risk and execution.
- Never store or expose customer exchange secrets in ordinary application data.
- Never permit AI output to bypass deterministic controls.
- Never fabricate execution state, payment state, market data or model results.

---

# 8.0 CONTRACT-FIRST PRINCIPLE

Every production service must publish an explicit contract defining:

- purpose
- inputs
- outputs
- authentication
- authorization
- validation
- side effects
- idempotency
- errors
- audit requirements
- events emitted
- rate limits
- versioning
- timeout behavior
- retry behavior

Frontend teams must not infer security or business rules from UI behavior.
Backend teams must not infer business requirements from visual layout alone.

---

# 8.1 API VERSIONING

Primary public API prefix:

/api/v1/

Breaking changes require a new major version.

Non-breaking additions may remain in the current version.

Every endpoint should have:
- owner
- version
- deprecation policy
- documentation
- test coverage

---

# 8.2 REQUEST IDENTIFIERS

Every request should support:

requestId
correlationId
idempotencyKey where applicable

Example headers:

X-Request-Id
X-Correlation-Id
Idempotency-Key

The server remains authoritative for request identifiers.
Client-supplied IDs must be sanitized and length-limited.

---

# 8.3 RESPONSE ENVELOPE

SUCCESS:

{
  "data": {},
  "meta": {
    "requestId": "...",
    "correlationId": "...",
    "schemaVersion": "1.0"
  }
}

ERROR:

{
  "error": {
    "code": "RISK_REJECTED",
    "message": "The request did not pass the configured risk controls.",
    "retryable": false,
    "requestId": "...",
    "correlationId": "...",
    "schemaVersion": "1.0"
  }
}

Never return stack traces or internal infrastructure details.

---

# 8.4 HTTP STATUS POLICY

200 — successful retrieval/update
201 — resource created
202 — accepted asynchronous operation
204 — successful operation without body
400 — malformed request
401 — unauthenticated
403 — authenticated but forbidden
404 — resource unavailable/not visible
409 — state conflict
422 — semantically invalid
429 — rate limited
500 — unexpected internal error
502 — upstream provider failure
503 — temporary unavailable
504 — upstream timeout

---

# 8.5 ERROR CODE POLICY

Machine-readable codes must be stable.

Examples:

AUTH_REQUIRED
AUTH_INVALID
SESSION_EXPIRED
FORBIDDEN
RESOURCE_NOT_FOUND
VALIDATION_FAILED
ENTITLEMENT_INACTIVE
PRODUCT_UNAVAILABLE
PLATFORM_RESTRICTION
REGION_RESTRICTION
CONNECTION_NOT_READY
CONNECTION_INVALID
PERMISSION_MISMATCH
PROVIDER_UNAVAILABLE
MARKET_DATA_STALE
MARKET_CLOSED
RISK_REJECTED
EXECUTION_BLOCKED
ORDER_UNKNOWN
ORDER_REJECTED
RECONCILIATION_REQUIRED
RATE_LIMITED
IDEMPOTENCY_CONFLICT
PAYMENT_PENDING
PAYMENT_FAILED
ENTITLEMENT_PENDING
AI_UNAVAILABLE
AI_OUTPUT_INVALID

Human-facing wording can evolve without changing machine codes.

---

# 8.6 PAGINATION CONTRACT

Default:
cursor-based pagination for high-volume datasets.

Request:

GET /api/v1/orders?limit=50&cursor=...

Response:

{
  "data": [],
  "meta": {
    "nextCursor": "...",
    "hasMore": true
  }
}

Limits must be enforced server-side.

Do not accept arbitrarily large limits.

---

# 8.7 SORTING AND FILTERING

Only allow approved fields.

Example:

sort=createdAt
direction=desc

Do not construct raw SQL from user-supplied field names.

---

# 8.8 DATABASE NAMING

Use consistent naming:

snake_case
lowercase
singular/plural policy chosen once

Prefer:

users
subscriptions
entitlements
exchange_connections
risk_decisions
orders

Every table has:
id
created_at
updated_at

Soft deletion only where genuinely required.

---

# 8.9 IDENTIFIER STRATEGY

Prefer non-guessable identifiers at public boundaries.

Internally:
UUID/ULID or equivalent.

Never expose sequential database IDs where they create enumeration risk.

Public IDs must still have server-side authorization.

---

# 8.10 USERS TABLE

Conceptual schema:

users
- id
- public_id
- email
- email_verified_at
- status
- locale
- timezone
- country_code
- created_at
- updated_at
- last_login_at

Do not store unnecessary personal data.

---

# 8.11 USER SECURITY TABLE

user_security
- user_id
- password_hash where passwords are supported
- mfa_enabled
- risk_level
- recovery_state
- last_security_review_at
- created_at
- updated_at

Passkey credentials should use a dedicated credential table.

---

# 8.12 PASSKEY CREDENTIALS

passkey_credentials
- id
- user_id
- credential_id
- public_key
- sign_count
- transports
- device_label
- created_at
- last_used_at
- revoked_at

Never store private keys.

---

# 8.13 SESSIONS

sessions
- id
- user_id
- device_id
- session_hash
- created_at
- expires_at
- last_seen_at
- revoked_at
- risk_level

Store only the minimum token/session material required.

---

# 8.14 DEVICES

devices
- id
- user_id
- platform
- os_name
- os_version
- app_version
- device_label
- attestation_status
- created_at
- last_seen_at
- revoked_at

---

# 8.15 ORGANIZATIONS — FUTURE READY

Do not build unnecessary enterprise complexity initially, but reserve a future abstraction:

organizations
organization_members
organization_roles

Customer data ownership must still be explicit.

---

# 8.16 PRODUCT CATALOG

products
- id
- product_key
- name
- description
- product_type
- status
- policy_version
- created_at
- updated_at

product_types:

SIGNALS
AUTOMATION

---

# 8.17 MARKET MODES

product_market_modes
- id
- product_id
- market_type
- mode
- status
- policy_version

Examples:

CRYPTO + SPOT
CRYPTO + FUTURES
CRYPTO + HYBRID
FOREX + MT5
FOREX + HYBRID
BINARY_OPTIONS_LIKE + OTC
BINARY_OPTIONS_LIKE + INTERNATIONAL
BINARY_OPTIONS_LIKE + HYBRID

The last group must be independently gated by platform and jurisdiction policy.

---

# 8.18 SUBSCRIPTION PLANS

plans
- id
- product_id
- market_mode_id
- term_unit
- term_value
- amount
- currency
- renewal_policy
- status
- effective_from
- effective_to

Supported terms:

7 DAYS
1 MONTH
3 MONTHS
6 MONTHS
12 MONTHS

---

# 8.19 SUBSCRIPTIONS

subscriptions
- id
- user_id
- plan_id
- provider
- provider_subscription_id
- status
- starts_at
- ends_at
- renews_at
- cancelled_at
- created_at
- updated_at

---

# 8.20 ENTITLEMENTS

entitlements
- id
- user_id
- entitlement_key
- status
- source_subscription_id
- starts_at
- expires_at
- policy_version
- created_at
- updated_at

Never trust entitlement status sent by the client.

---

# 8.21 ENTITLEMENT KEYS

Examples:

signals.crypto.spot
signals.crypto.futures
automation.crypto.spot
automation.crypto.futures
signals.forex.mt5
automation.forex.mt5
binary_options_like
blog.premium
analytics.advanced

Use namespace conventions.

---

# 8.22 COMPLIANCE DECISION RECORD

eligibility_decisions
- id
- user_id
- product
- market_mode
- platform
- jurisdiction
- decision
- reason_codes
- policy_version
- evaluated_at

Historical decisions remain immutable.

---

# 8.23 PAYMENT TABLE

payments
- id
- user_id
- provider
- provider_payment_id
- amount
- currency
- status
- captured_at
- refunded_at
- created_at
- updated_at

---

# 8.24 PAYMENT EVENTS

payment_events
- id
- provider
- provider_event_id
- event_type
- payload_reference
- signature_verified
- received_at
- processed_at
- processing_status

Use a uniqueness constraint on provider + provider_event_id.

---

# 8.25 REFUNDS

refunds
- id
- payment_id
- provider_refund_id
- amount
- currency
- status
- reason
- created_at
- processed_at

---

# 8.26 EXCHANGE CONNECTIONS

exchange_connections
- id
- public_id
- user_id
- provider
- account_reference
- secret_reference
- status
- permission_profile
- last_health_check_at
- last_successful_call_at
- created_at
- revoked_at

Never store the raw secret here.

---

# 8.27 PROVIDER CAPABILITIES

provider_capabilities
- provider
- asset_class
- supports_spot
- supports_futures
- supports_perpetuals
- supports_reduce_only
- supports_stop
- supports_take_profit
- supports_client_order_id
- supports_websocket
- supports_oauth
- version
- reviewed_at

---

# 8.28 STRATEGIES

strategies
- id
- strategy_key
- name
- product
- market_mode
- status
- current_version_id
- owner_team
- created_at
- updated_at

---

# 8.29 STRATEGY VERSIONS

strategy_versions
- id
- strategy_id
- version
- configuration_reference
- code_reference
- risk_policy_id
- evaluation_status
- created_at
- approved_at
- retired_at

Production strategy versions are immutable.

---

# 8.30 RISK POLICIES

risk_policies
- id
- name
- version
- configuration_reference
- status
- created_at
- approved_at
- retired_at

Historical decisions reference exact policy versions.

---

# 8.31 MARKET SNAPSHOTS

market_snapshots
- id
- provider
- venue
- instrument
- timestamp
- received_at
- bid
- ask
- last
- volume
- quality_score
- latency_ms

---

# 8.32 SIGNALS

signals
- id
- strategy_version_id
- instrument
- market_mode
- direction
- signal_strength
- confidence
- data_quality
- risk_level
- status
- generated_at
- expires_at

---

# 8.33 SIGNAL EVIDENCE

signal_evidence
- id
- signal_id
- evidence_type
- source_reference
- snapshot_reference
- score
- weight
- created_at

---

# 8.34 AI ANALYSIS RECORD

ai_analysis
- id
- signal_request_id
- agent_id
- provider
- model
- model_version
- input_snapshot_reference
- output_reference
- confidence
- data_quality
- status
- created_at

Do not store raw secrets or sensitive hidden reasoning.

---

# 8.35 ANALYST RESULT

analyst_results
- id
- signal_id
- analyst_type
- direction
- score
- confidence
- data_quality
- created_at

Analyst types may include:
trend
momentum
volatility
volume
market_structure
liquidity
order_flow
support_resistance
multi_timeframe
regime
correlation
sentiment
fundamental
news
risk
execution_quality

The number of analysts displayed to users must match the implemented system.

---

# 8.36 ENSEMBLE RESULTS

ensemble_results
- id
- signal_id
- ensemble_version
- consensus_score
- disagreement_score
- uncertainty_score
- data_quality_score
- created_at

---

# 8.37 RISK DECISIONS

risk_decisions
- id
- request_id
- user_id
- signal_id
- policy_id
- policy_version
- decision
- risk_score
- max_quantity
- recommended_quantity
- reason_codes
- created_at

---

# 8.38 ORDERS

orders
- id
- public_id
- user_id
- signal_id
- connection_id
- client_order_id
- provider_order_id
- state
- side
- order_type
- quantity
- approved_quantity
- filled_quantity
- limit_price
- stop_price
- take_profit_price
- average_fill_price
- fee_amount
- fee_currency
- created_at
- updated_at

---

# 8.39 ORDER EVENTS

order_events
- id
- order_id
- event_type
- provider_event_id
- provider_timestamp
- payload_reference
- created_at

---

# 8.40 FILLS

fills
- id
- order_id
- provider_fill_id
- quantity
- price
- fee_amount
- fee_currency
- executed_at

---

# 8.41 POSITIONS

positions
- id
- user_id
- connection_id
- instrument
- side
- quantity
- average_entry_price
- mark_price
- margin
- leverage
- realized_pnl
- unrealized_pnl
- last_synced_at

---

# 8.42 BALANCES

balances
- id
- connection_id
- asset
- available
- locked
- total
- valuation_currency
- valuation
- last_synced_at

---

# 8.43 RECONCILIATION CASES

reconciliation_cases
- id
- connection_id
- entity_type
- entity_reference
- mismatch_type
- internal_snapshot
- provider_snapshot_reference
- severity
- status
- opened_at
- resolved_at
- resolution_reference

---

# 8.44 AUDIT EVENTS

audit_events
- id
- event_type
- actor_type
- actor_id
- resource_type
- resource_id
- action
- result
- policy_version
- correlation_id
- metadata_reference
- timestamp

---

# 8.45 DATABASE CONSTRAINTS

Enforce:
- unique provider event IDs
- unique client order IDs per connection where required
- valid foreign keys
- non-negative quantities where appropriate
- currency presence for monetary amounts
- immutable historical versions
- valid timestamps
- valid status transitions

Use database constraints wherever they can safely enforce invariants.

---

# 8.46 TRANSACTION BOUNDARIES

Use transactions for atomic state transitions.

Example:

payment event recorded
+
payment state updated
+
outbox event inserted

all in one transaction where appropriate.

---

# 8.47 OUTBOX TABLE

outbox_events
- id
- aggregate_type
- aggregate_id
- event_type
- schema_version
- payload_reference
- correlation_id
- created_at
- published_at
- attempts

This prevents state changes from being committed without their corresponding internal event.

---

# 8.48 INBOX TABLE

inbox_events
- id
- consumer
- event_id
- processed_at
- result

Used for deduplication when repeated event delivery is possible.

---

# 8.49 EVENT ENVELOPE

Every event:

{
  "eventId": "...",
  "eventType": "SignalCreated",
  "schemaVersion": "1.0",
  "occurredAt": "...",
  "producer": "signal-service",
  "aggregateType": "signal",
  "aggregateId": "...",
  "correlationId": "...",
  "data": {}
}

---

# 8.50 USER REGISTERED EVENT

{
  "eventType": "UserRegistered",
  "schemaVersion": "1.0",
  "data": {
    "userId": "...",
    "locale": "fa-IR"
  }
}

Do not include passwords or secrets.

---

# 8.51 SUBSCRIPTION ACTIVATED EVENT

{
  "eventType": "SubscriptionActivated",
  "data": {
    "subscriptionId": "...",
    "userId": "...",
    "planId": "...",
    "startsAt": "...",
    "endsAt": "..."
  }
}

---

# 8.52 ENTITLEMENT ACTIVATED EVENT

{
  "eventType": "EntitlementActivated",
  "data": {
    "userId": "...",
    "entitlementKey": "automation.crypto.spot",
    "policyVersion": "...",
    "expiresAt": "..."
  }
}

---

# 8.53 CONNECTION VALIDATED EVENT

{
  "eventType": "ConnectionValidated",
  "data": {
    "connectionId": "...",
    "provider": "...",
    "permissionProfile": {
      "read": true,
      "trade": true,
      "withdraw": false
    }
  }
}

Never include the actual secret.

---

# 8.54 SIGNAL CREATED EVENT

{
  "eventType": "SignalCreated",
  "data": {
    "signalId": "...",
    "instrument": "...",
    "direction": "BUY",
    "signalStrength": 78,
    "confidence": 71,
    "riskLevel": "MODERATE",
    "generatedAt": "...",
    "expiresAt": "..."
  }
}

---

# 8.55 RISK DECISION EVENT

{
  "eventType": "RiskDecisionCompleted",
  "data": {
    "requestId": "...",
    "decision": "ALLOW",
    "recommendedQuantity": "...",
    "maxQuantity": "...",
    "policyVersion": "..."
  }
}

---

# 8.56 ORDER SUBMITTED EVENT

{
  "eventType": "OrderSubmitted",
  "data": {
    "orderId": "...",
    "clientOrderId": "...",
    "providerOrderId": "...",
    "instrument": "...",
    "side": "BUY"
  }
}

---

# 8.57 ORDER UNKNOWN EVENT

{
  "eventType": "OrderUnknown",
  "data": {
    "orderId": "...",
    "reasonCode": "PROVIDER_TIMEOUT"
  }
}

The system must prevent duplicate execution until reconciliation resolves the state.

---

# 8.58 ORDER FILLED EVENT

{
  "eventType": "OrderFilled",
  "data": {
    "orderId": "...",
    "filledQuantity": "...",
    "averageFillPrice": "...",
    "feeAmount": "...",
    "feeCurrency": "..."
  }
}

---

# 8.59 RECONCILIATION COMPLETED EVENT

{
  "eventType": "ReconciliationCompleted",
  "data": {
    "connectionId": "...",
    "status": "MATCHED",
    "casesResolved": 2
  }
}

---

# 8.60 REST API — AUTH

POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/mfa/challenge
POST /api/v1/auth/passkeys/register/options
POST /api/v1/auth/passkeys/register
POST /api/v1/auth/passkeys/authenticate/options
POST /api/v1/auth/passkeys/authenticate

Do not implement passwordless UX without secure recovery.

---

# 8.61 REST API — ACCOUNT

GET /api/v1/me
PATCH /api/v1/me
GET /api/v1/me/security
GET /api/v1/me/sessions
DELETE /api/v1/me/sessions/{id}
GET /api/v1/me/devices

---

# 8.62 REST API — CATALOG

GET /api/v1/catalog/products
GET /api/v1/catalog/products/{id}
GET /api/v1/catalog/plans
GET /api/v1/catalog/availability

Availability must be derived from policy.

---

# 8.63 REST API — SUBSCRIPTIONS

GET /api/v1/subscriptions
GET /api/v1/subscriptions/{id}
POST /api/v1/subscriptions/checkout
POST /api/v1/subscriptions/{id}/cancel

Do not activate entitlement directly from the client.

---

# 8.64 REST API — ENTITLEMENTS

GET /api/v1/entitlements
GET /api/v1/entitlements/{key}

User can view only their entitlements.

---

# 8.65 REST API — CONNECTIONS

GET /api/v1/connections
POST /api/v1/connections
GET /api/v1/connections/{id}
POST /api/v1/connections/{id}/validate
POST /api/v1/connections/{id}/disconnect
POST /api/v1/connections/{id}/replace

Raw secrets are accepted only where necessary and never returned.

---

# 8.66 CONNECTION CREATE REQUEST

{
  "provider": "example",
  "apiKey": "...",
  "apiSecret": "..."
}

This endpoint must:
- authenticate
- authorize
- validate
- store secret securely
- return only non-sensitive status

Response:

{
  "data": {
    "connectionId": "...",
    "status": "VALIDATING"
  }
}

---

# 8.67 SIGNAL API

GET /api/v1/signals
GET /api/v1/signals/{id}
POST /api/v1/signals/request

Request:

{
  "instrument": "BTC/USDT",
  "marketMode": "SPOT",
  "strategyId": "..."
}

Server derives user/account/entitlement/risk context.

---

# 8.68 ORDER API

GET /api/v1/orders
GET /api/v1/orders/{id}
POST /api/v1/orders
POST /api/v1/orders/{id}/cancel

Order creation must require:
authentication
authorization
entitlement
connection
risk
policy
idempotency

---

# 8.69 ORDER CREATE REQUEST

{
  "signalId": "...",
  "connectionId": "...",
  "intent": {
    "side": "BUY"
  }
}

Never accept client-provided:
riskApproved
approvedQuantity
policyVersion
executionAllowed

---

# 8.70 POSITION API

GET /api/v1/positions
GET /api/v1/positions/{id}

Stale state must be marked.

---

# 8.71 RISK API

GET /api/v1/risk/profile
PATCH /api/v1/risk/profile
GET /api/v1/risk/status

Changing risk profile may require step-up authentication.

---

# 8.72 JOURNAL API

GET /api/v1/journal
GET /api/v1/journal/{id}

Journal data should be read-only from the customer application.

---

# 8.73 PAYMENT API

POST /api/v1/payments/checkout
GET /api/v1/payments/{id}
GET /api/v1/payments

Webhook endpoints remain provider-specific and isolated.

---

# 8.74 ADMIN API

/admin/v1/customers
/admin/v1/subscriptions
/admin/v1/entitlements
/admin/v1/connections
/admin/v1/strategies
/admin/v1/risk-policies
/admin/v1/incidents
/admin/v1/audit
/admin/v1/feature-flags

Admin APIs must have a separate authorization policy.

---

# 8.75 ADMIN CUSTOMER ENDPOINT

GET /admin/v1/customers/{id}

Response must contain only data allowed for the admin role.

Never include:
apiSecret
passwordHash
sessionToken
private credential

---

# 8.76 ADMIN CONNECTION ENDPOINT

GET /admin/v1/connections/{id}

Allowed:
provider
account reference
status
permission profile
health
timestamps

Forbidden:
raw API secret

---

# 8.77 ADMIN STRATEGY PAUSE

POST /admin/v1/strategies/{id}/pause

Request:

{
  "reason": "Operational safety review"
}

Require:
permission
confirmation
audit
event

---

# 8.78 ADMIN RISK POLICY CHANGE

POST /admin/v1/risk-policies/{id}/publish

Require:
authorized role
review state
approval
version increment
audit

Never edit an already-applied historical policy in place.

---

# 8.79 WEBSOCKET MODEL

Use WebSocket/SSE only where real-time updates improve the product.

Channels:

/ws/account
/ws/signals
/ws/orders
/ws/positions
/ws/system

All require authentication.

---

# 8.80 WEBSOCKET AUTH

Use an authenticated handshake or short-lived connection token.

Do not send permanent API credentials to the browser.

---

# 8.81 WEBSOCKET EVENT

{
  "type": "ORDER_UPDATED",
  "version": 3,
  "timestamp": "...",
  "data": {
    "orderId": "...",
    "state": "FILLED"
  }
}

Clients must handle out-of-order events.

---

# 8.82 WEBSOCKET RECONNECT

States:

CONNECTING
CONNECTED
RECONNECTING
STALE
DISCONNECTED

After reconnect:
re-fetch authoritative state.

Do not rely only on missed events.

---

# 8.83 SERVER-SENT EVENTS

SSE is suitable for:
signals
system status
non-mutating notifications

If bidirectional control is needed:
use authenticated REST/WebSocket commands.

---

# 8.84 PAYMENT WEBHOOK

Endpoint:

POST /webhooks/{provider}/payments

Pipeline:

receive
verify signature
verify timestamp
deduplicate
validate schema
store event
process transaction
emit event
return success

---

# 8.85 EXCHANGE WEBHOOK

If provider supports webhooks/user streams:

receive
authenticate
validate
deduplicate
apply state transition
audit
reconcile if necessary

Never accept provider-supplied user IDs without mapping validation.

---

# 8.86 WEBHOOK REPLAY PROTECTION

Track:
provider
eventId
timestamp

Reject duplicates.

For signed requests also validate freshness.

---

# 8.87 WEBHOOK FAILURE HANDLING

Transient internal failure:
return retryable provider status where appropriate.

Permanent validation failure:
reject.

Never acknowledge a webhook as successfully processed when the internal transaction was not committed.

---

# 8.88 API RATE LIMITS

Define per endpoint.

Examples conceptually:

public read:
high

authenticated dashboard:
moderate

connection validation:
low

order creation:
very low

admin destructive:
very low

Limits must be documented.

---

# 8.89 ABUSE CONTROLS

For high-cost or high-risk flows:
- velocity limits
- risk scoring
- step-up auth
- challenge
- lockout
- manual review

Do not use CAPTCHA as the only abuse control.

---

# 8.90 VALIDATION

Validate at:
edge
application
domain

Do not rely on frontend validation.

Use:
type
range
enum
format
business rule

---

# 8.91 FINANCIAL DECIMAL VALIDATION

Money/quantity should use decimal-safe representations.

Reject:
NaN
Infinity
unexpected scientific notation where inappropriate
excess precision
negative quantities unless semantically supported

---

# 8.92 ORDER VALIDATION EXAMPLE

Before provider request verify:

instrument supported
symbol mapping valid
quantity >= minimum
quantity <= maximum
step size valid
price precision valid
risk approved
provider connected
account active
entitlement active
market open
data fresh
incident lock false

---

# 8.93 API IDEMPOTENCY

Store:
key
actor
endpoint
request hash
response
created_at
expires_at

Same key + same request:
return same result.

Same key + different request:
IDEMPOTENCY_CONFLICT.

---

# 8.94 ASYNCHRONOUS COMMANDS

For long operations:

POST
→ 202 Accepted

Response:

{
  "data": {
    "operationId": "...",
    "status": "PENDING"
  }
}

Query:

GET /api/v1/operations/{id}

---

# 8.95 OPERATION STATES

PENDING
RUNNING
SUCCEEDED
FAILED
CANCELLED
EXPIRED

Operations affecting financial state must also create domain events.

---

# 8.96 LONG-RUNNING CONNECTION VALIDATION

Connection validation should not block an HTTP request indefinitely.

Use:
command
→ operation
→ background validation
→ event
→ UI update

---

# 8.97 LONG-RUNNING RECONCILIATION

Admin may request:

POST /admin/v1/connections/{id}/reconcile

Returns operationId.

Result becomes auditable.

---

# 8.98 FILE UPLOADS

If users can upload:
documents
avatars
support attachments

Use:
content type validation
size limits
malware scanning
safe object storage
randomized names
authorization
download access controls

Never execute uploaded content.

---

# 8.99 DOCUMENT DATA

KYC or legal documents require:
separate restricted storage
encryption
access logs
retention rules

Do not put sensitive documents in the general application database.

---

# 8.100 EXPORT API

GET /api/v1/exports/{type}

Generate asynchronous export job.

Require step-up auth for sensitive financial exports.

---

# 8.101 IMPORT API

Only implement imports when business requirements justify them.

Validate:
schema
size
encoding
ownership
duplicate records

Never import orders directly into live execution state.

---

# 8.102 SEARCH API

Search must be authorization-scoped.

GET /api/v1/search?q=...

Never return unauthorized results through autocomplete.

---

# 8.103 NOTIFICATION API

GET /api/v1/notifications
POST /api/v1/notifications/{id}/read
POST /api/v1/notifications/read-all

Security notices remain subject to critical-delivery rules.

---

# 8.104 CONTENT API

GET /api/v1/content/articles
GET /api/v1/content/articles/{slug}

Admin:

POST /admin/v1/content/articles
PATCH /admin/v1/content/articles/{id}
POST /admin/v1/content/articles/{id}/publish
POST /admin/v1/content/articles/{id}/unpublish

---

# 8.105 SEO API

Admin-only metadata:

title
description
canonical
robots
social image
redirects

Validate URLs and prevent open redirects.

---

# 8.106 FEATURE FLAG API

Customer-facing services may receive evaluated flags.

They should not receive the ability to mutate flags.

Admin mutations require:
authorization
audit
expiry
rollback

---

# 8.107 POLICY EVALUATION API

Internal:

POST /internal/v1/policy/evaluate

Request:

user
product
market
platform
jurisdiction
provider
accountStatus

Response:

decision
reasonCodes
policyVersion
effectiveAt

---

# 8.108 RISK EVALUATION API

Internal:

POST /internal/v1/risk/evaluate

Request:

signal
account
portfolio
marketSnapshot
strategyVersion

Response:

decision
recommendedQuantity
maximumQuantity
reasonCodes
policyVersion

---

# 8.109 STRATEGY EVALUATION API

Internal:

POST /internal/v1/strategy/evaluate

Output is a candidate only.

No order side effect is permitted.

---

# 8.110 AI ANALYSIS API

Internal:

POST /internal/v1/ai/analyze

Request includes only required context.

Response must conform to JSON schema.

---

# 8.111 AI OUTPUT SCHEMA

{
  "direction": "BUY",
  "confidence": 0,
  "riskFlags": [],
  "reasonCodes": [],
  "sourceReferences": [],
  "expiresAt": "..."
}

Validate:
enum
range
required fields
source references

---

# 8.112 AI TOOL PERMISSIONS

AI services must not receive unrestricted tools.

Allowed tools should be explicit:

get_market_snapshot
get_approved_news
get_strategy_context

Forbidden by default:

place_order
change_risk_policy
reveal_secret
change_entitlement
change_subscription
modify_user_security

---

# 8.113 INTERNAL SERVICE AUTH

Use service-to-service authentication.

Each service receives a service identity.

Authorization based on:
service
operation
resource

Do not rely solely on network location.

---

# 8.114 INTERNAL NETWORK

Assume internal network is hostile.

Require:
authentication
authorization
encryption where appropriate
service identity
logging

---

# 8.115 DATABASE ACCESS

Each service should receive the minimum DB permissions.

Prefer:
read-only replicas
separate application users
migration-only credentials
least privilege

---

# 8.116 MIGRATION API

No public endpoint.

Migration is a deployment operation.

Maintain:
version
checksum
applied_at

---

# 8.117 BACKWARD COMPATIBILITY

When changing API/event schema:

add new field
support old consumers
migrate
then remove old field after compatibility period

Never break consumers silently.

---

# 8.118 EVENT SCHEMA VERSIONING

Each event has:
schemaVersion

Example:
SignalCreated.v1
SignalCreated.v2

Consumers declare supported versions.

---

# 8.119 API DEPRECATION

Mark endpoints:
deprecated
sunset date
replacement

Publish migration documentation.

---

# 8.120 CONTRACT TESTING

For every public API:
- request schema
- response schema
- auth
- forbidden
- validation
- errors
- pagination
- idempotency

For every event:
- schema
- producer
- consumer
- version
- duplicate delivery

---

# 8.121 PROPERTY-BASED TESTING

For financial logic test invariants.

Examples:
approved quantity <= max quantity
filled quantity <= requested quantity
negative equity does not produce invalid JSON
risk approval never exceeds configured limit
duplicate idempotency key never creates duplicate order

---

# 8.122 STATE MACHINE TESTING

Test every transition.

Example:
UNKNOWN cannot directly become FILLED without evidence/reconciliation.

---

# 8.123 DATABASE INTEGRITY TESTS

Verify:
foreign keys
unique constraints
state transitions
version immutability
audit persistence

---

# 8.124 LOAD TESTING API

Load-test:
public reads
authenticated dashboard
signal streams
notifications

Run execution tests only in sandbox/simulation unless a tightly controlled production validation is specifically approved.

---

# 8.125 OBSERVABILITY FIELDS

Every request log:
timestamp
service
route
status
latency
requestId
correlationId
safe user reference

Never log:
password
API secret
access token
full payment information

---

# 8.126 TRACE SPANS

Suggested spans:

api.request
entitlement.evaluate
eligibility.evaluate
market.snapshot
strategy.evaluate
ai.analysis
risk.evaluate
order.prepare
provider.submit
provider.query
reconciliation

---

# 8.127 METRICS

HTTP:
request_count
error_count
latency

Trading:
signals_created
signals_suppressed
risk_rejections
orders_submitted
orders_filled
orders_rejected
orders_unknown
reconciliation_cases

Payment:
payments_confirmed
payments_failed
webhook_lag
entitlement_lag

AI:
requests
timeouts
schema_failures
latency
cost

---

# 8.128 ALERTS

Critical alerts:

secret access anomaly
payment entitlement mismatch
unknown orders above threshold
reconciliation backlog
risk-engine failure
market-data staleness
provider outage
authorization failure spike
admin privilege anomaly

---

# 8.129 DATA QUALITY ALERTS

Alert if:
timestamp drift
missing quotes
sequence gap
provider disagreement
stale feed
abnormal spread

---

# 8.130 SECURITY EVENT STREAM

Security events:
login_failed
mfa_failed
passkey_added
passkey_removed
session_revoked
admin_role_changed
secret_accessed
connection_created
connection_revoked

Sensitive event details are restricted.

---

# 8.131 ADMIN AUDIT API

GET /admin/v1/audit

Filters:
actor
resource
action
date
severity
result
correlationId

Every query itself may be audited for highly sensitive environments.

---

# 8.132 INCIDENT API

GET /admin/v1/incidents
POST /admin/v1/incidents
POST /admin/v1/incidents/{id}/pause
POST /admin/v1/incidents/{id}/resolve

Incident state:

OPEN
INVESTIGATING
MITIGATED
RESOLVED
CLOSED

---

# 8.133 GLOBAL PAUSE API

POST /admin/v1/control/global-pause

Requires:
security/operations authorization
reason
confirmation
audit

The backend enforces the pause.

---

# 8.134 CUSTOMER PAUSE API

POST /api/v1/automation/pause
POST /api/v1/automation/resume

Resume must re-evaluate all eligibility and health gates.

---

# 8.135 AUTOMATION STATUS API

GET /api/v1/automation/status

Response:

{
  "data": {
    "status": "ACTIVE",
    "locks": [],
    "providerHealth": "HEALTHY",
    "riskStatus": "HEALTHY",
    "dataStatus": "FRESH"
  }
}

---

# 8.136 NO-TRADE API RESULT

A signal request can validly return:

{
  "data": {
    "status": "NO_TRADE",
    "reasonCode": "DATA_STALE"
  }
}

No fake signal should be generated merely to satisfy the UI.

---

# 8.137 ERROR LOCALIZATION

Backend error responses should expose stable codes.

Frontend maps them to localized strings.

This avoids server-dependent translation logic.

---

# 8.138 PERSIAN ERROR EXAMPLES

RISK_REJECTED:
«این درخواست با محدودیت‌های مدیریت ریسک تنظیم‌شده مطابقت نداشت.»

DATA_STALE:
«داده‌های بازار در حال حاضر به‌اندازه کافی به‌روز نیستند. اجرای خودکار متوقف شده است.»

ORDER_UNKNOWN:
«وضعیت سفارش هنوز از سمت ارائه‌دهنده تأیید نشده است. برای جلوگیری از ثبت سفارش تکراری، وضعیت در حال بررسی است.»

---

# 8.139 ENGLISH ERROR EXAMPLES

RISK_REJECTED:
“This request did not pass the configured risk controls.”

DATA_STALE:
“Market data is currently too stale for safe automated execution.”

ORDER_UNKNOWN:
“The provider has not yet confirmed the order state. Duplicate submission is blocked while reconciliation is in progress.”

---

# 8.140 CORRELATION EXAMPLE

One trading decision:

correlationId = C123

Records:
signal request
strategy analysis
AI analysis
risk decision
policy decision
order
provider response
fill
reconciliation
notification

All can be traced through C123.

---

# 8.141 AUDIT EXAMPLE

{
  "eventType": "ORDER_SUBMIT_ATTEMPT",
  "actorType": "SYSTEM",
  "actorId": "execution-service",
  "resourceType": "ORDER",
  "resourceId": "...",
  "action": "SUBMIT",
  "result": "SUCCESS",
  "policyVersion": "...",
  "correlationId": "..."
}

No secret in metadata.

---

# 8.142 RETRY POLICY

Retry only when:
- transient failure
- operation is idempotent
- provider semantics are understood

Do not automatically retry:
risk rejection
invalid credentials
permission mismatch
compliance block
invalid order

---

# 8.143 TIMEOUT POLICY

Every network call has:
connect timeout
read timeout
overall timeout

Never leave an execution request hanging indefinitely.

---

# 8.144 CIRCUIT BREAKERS

Providers:
CLOSED
OPEN
HALF_OPEN

When OPEN:
fail safely.

---

# 8.145 BULKHEADS

Separate capacity pools for:
payment
market data
AI
execution
notifications

One failing subsystem should not consume all system capacity.

---

# 8.146 QUEUE PRIORITIES

SECURITY
EXECUTION
RECONCILIATION
ACCOUNT
NOTIFICATION
ANALYTICS

Priority should be controlled and observable.

---

# 8.147 DEAD LETTERS

Every queue:
DLQ
replay workflow
ownership
alert

Financial event DLQs require operational investigation.

---

# 8.148 REPLAY

Replay is allowed only for events designed to be safely replayable.

Never replay an order creation event blindly.

---

# 8.149 SAFE REPROCESSING

For every event:
determine:
idempotent?
stateful?
financial side effect?
safe to replay?

If uncertain:
manual review.

---

# 8.150 DATA EXPORT PRIVACY

Exports must:
authorize
minimize
expire
be access-controlled

Download links should be temporary and authenticated.

---

# 8.151 API DOCUMENTATION

Publish OpenAPI documentation for public APIs.

Document:
auth
schemas
examples
errors
rate limits
idempotency
pagination
deprecation

Do not publish sensitive internal APIs.

---

# 8.152 OPENAPI QUALITY

Every endpoint:
summary
description
request schema
response schema
errors
security requirement
example

Financial endpoints require explicit side-effect documentation.

---

# 8.153 JSON SCHEMA

Maintain JSON Schema for:
events
AI outputs
provider normalized payloads
webhooks
API objects

Use schema validation in CI.

---

# 8.154 PROVIDER ADAPTER CONTRACT

Adapter must normalize:

authentication
balances
positions
orders
fills
market data
errors
limits

Provider-specific behavior stays behind adapter boundary.

---

# 8.155 PROVIDER CONTRACT TESTS

Test:
valid credentials
invalid credentials
permission mismatch
market closed
minimum quantity
maximum quantity
timeout
partial fill
duplicate webhook
out-of-order webhook

---

# 8.156 BROKER / MT5 CONTRACT

Normalize:
account
symbol
lot
point
tick
contract
margin
position

Never assume exchange-style quantity semantics.

---

# 8.157 CRYPTO DERIVATIVE CONTRACT

Normalize:
contract
quantity
side
leverage
margin
funding
liquidation
reduce-only

Provider-specific nomenclature must remain available internally.

---

# 8.158 SPOT CONTRACT

Normalize:
asset
available
locked
quantity
average cost
fees

No liquidation model.

---

# 8.159 HYBRID CONTRACT

Hybrid is an orchestration mode, not a claim that both markets will always be traded.

---

# 8.160 BINARY-OPTIONS-LIKE CONTRACT

Keep isolated in a policy-gated domain.

No mobile store release should activate prohibited native functionality.

Google Play's current policy explicitly disallows apps that enable binary-options trading; Apple also restricts facilitating binary-options trading in the App Store. citeturn657281search6turn657281search15

---

# 8.161 PLATFORM CAPABILITY API

GET /api/v1/catalog/availability

Returns capabilities based on:
platform
locale
jurisdiction
user eligibility

Example:

{
  "data": {
    "automation.crypto.spot": "AVAILABLE",
    "automation.crypto.futures": "AVAILABLE",
    "automation.forex.mt5": "REGION_DEPENDENT",
    "binary_options_like": "UNAVAILABLE"
  }
}

---

# 8.162 NO SECURITY THROUGH OBSCURITY

Do not assume an unlisted route is secure.

All protected routes enforce server-side authorization.

---

# 8.163 OPEN REDIRECT PROTECTION

Any redirect target must be:
- allowlisted
or
- same-origin

Never redirect to arbitrary user-supplied URLs.

---

# 8.164 WEBHOOK SECRET STORAGE

Provider webhook signing secrets belong in secure secret infrastructure.

Do not put them in source code.

---

# 8.165 API KEY ROTATION

Application/service credentials must have rotation plans.

Customer provider credentials follow provider/customer lifecycle.

The system should support replacement without downtime where provider semantics permit.

---

# 8.166 AUDIT RETENTION

Audit retention is policy-driven.

Financial/audit records may require longer retention than ordinary UX analytics.

Do not delete records solely because a UI account is deleted when legal retention applies.

---

# 8.167 TEST FIXTURES

Create fixtures for:
- happy path
- invalid data
- stale data
- provider outage
- duplicate event
- delayed event
- out-of-order event
- risk rejection
- payment reversal
- entitlement expiration
- compliance block

---

# 8.168 CONTRACT REGRESSION

Every release runs:
API contract tests
event contract tests
provider contract tests
database migration tests
authorization tests

---

# 8.169 FINAL API READINESS GATE

[ ] API versions defined
[ ] OpenAPI published
[ ] Error codes stable
[ ] Authorization tested
[ ] BOLA tests passed
[ ] Idempotency tested
[ ] Pagination tested
[ ] Rate limits tested
[ ] Webhook signatures verified
[ ] Replay protection tested
[ ] Event schema versioning implemented
[ ] Outbox implemented for critical events
[ ] Inbox/dedup implemented where required
[ ] Database constraints active
[ ] Financial decimal rules enforced
[ ] Unknown order state tested
[ ] Reconciliation tested
[ ] AI outputs schema-validated
[ ] No secret is returned by any endpoint
[ ] Admin API cannot reveal customer secrets
[ ] Store/platform gating is server-enforced
[ ] API documentation is current

---

# 8.170 FINAL DATA READINESS GATE

[ ] Core tables defined
[ ] Ownership boundaries defined
[ ] Foreign keys defined
[ ] Unique constraints defined
[ ] Historical versions immutable
[ ] Audit coverage complete
[ ] Data retention documented
[ ] Export policy defined
[ ] Deletion/anonymization flow defined
[ ] Sensitive documents isolated
[ ] Secrets isolated
[ ] Analytics separation implemented

---

# 8.171 FINAL EVENT READINESS GATE

[ ] Event envelope standardized
[ ] schemaVersion present
[ ] event IDs unique
[ ] correlation IDs present
[ ] producer identified
[ ] consumer documented
[ ] duplicate handling defined
[ ] replay policy defined
[ ] dead-letter workflow defined
[ ] financial events audited

---

# 8.172 FINAL END-TO-END API SCENARIO

Scenario:

Customer buys 6-month crypto automation.

1. POST checkout
2. payment provider confirms asynchronously
3. webhook verified
4. payment updated
5. SubscriptionActivated emitted
6. entitlement service evaluates
7. EntitlementActivated emitted
8. customer connects provider
9. secret stored in secure boundary
10. connection validation operation created
11. provider permission profile discovered
12. connection validated
13. market data snapshot obtained
14. strategy candidate generated
15. research agent returns evidence
16. strategy agent returns candidate
17. risk agent recommends sizing
18. ensemble is calculated
19. deterministic risk engine approves/rejects
20. policy engine evaluates
21. signal created if eligible
22. customer requests/automation requests execution
23. pre-submit validation repeats
24. idempotency key stored
25. order submitted
26. provider acknowledgment recorded
27. fill events processed
28. position updated
29. reconciliation validates state
30. customer notified
31. journal updated
32. audit chain completed

Every stage must be independently observable.

---

# 8.173 UNKNOWN FAILURE SCENARIO

Provider timeout after order submission:

- set ORDER_UNKNOWN
- block duplicate order
- begin reconciliation
- query provider
- resolve state
- emit final event
- notify customer
- update journal

Never invent “cancelled” or “filled”.

---

# 8.174 DATABASE FAILOVER PRINCIPLE

If transactional database is unavailable:

Do not accept financial mutations that cannot be durably recorded.

Read-only degraded mode may remain available where safe.

---

# 8.175 EVENT BUS FAILURE PRINCIPLE

If event bus is unavailable but database is healthy:
commit critical state + outbox event.

Publisher retries later.

Do not pretend downstream systems already processed the event.

---

# 8.176 CACHE FAILURE PRINCIPLE

Cache failure should not change authorization or financial correctness.

Fallback to authoritative data where safe.

---

# 8.177 AI FAILURE PRINCIPLE

AI failure:
use approved fallback
or abstain.

Never fabricate an output to maintain UX continuity.

---

# 8.178 PROVIDER FAILURE PRINCIPLE

Provider failure:
stop unsafe operations
preserve state
reconcile later.

---

# 8.179 PAYMENT FAILURE PRINCIPLE

Payment uncertainty:
do not activate entitlement until authoritative state confirms payment.

---

# 8.180 POLICY FAILURE PRINCIPLE

If policy service is unavailable for a sensitive action:
safe default:
DENY / NO_EXECUTION.

---

# 8.181 FINAL CONTRACT PRINCIPLE

The complete platform must be deterministic at the boundaries:

CLIENT
→ API CONTRACT
→ AUTHORIZATION
→ ENTITLEMENT
→ POLICY
→ RISK
→ EXECUTION
→ PROVIDER
→ RECONCILIATION
→ AUDIT

Every uncertain transition must produce a visible, testable state.

---

# 8.182 PART 8 FINAL DIRECTIVE

Treat this part as the backend contract layer for implementation.

Before writing production code:
- freeze domain terminology
- approve schemas
- generate OpenAPI
- generate event schemas
- define migrations
- define authorization policies
- define provider adapters
- build sandbox fixtures
- build failure-path tests
- verify current external policies
- then begin implementation

The highest priority is not “make the trade happen.”

The highest priority is:

MAKE THE CORRECT TRADE HAPPEN ONLY WHEN IT IS PERMITTED, VALIDATED, RISK-APPROVED, TRACEABLE, AND RECONCILED.

END OF PART 8
