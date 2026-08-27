# TRADING PLATFORM MASTER PROMPT
# PART 7 — END-TO-END TRADING CORE, BROKER/EXCHANGE CONNECTIVITY, SIGNAL PIPELINE, EXECUTION, RECONCILIATION & OPERATIONAL CONTROL

CONTINUATION OF PARTS 1–6

This part defines the production-grade backend and trading-core behavior from purchase completion through subscription entitlement, connection validation, analytical processing, risk decision, signal publication, order execution where permitted, state reconciliation, and operational control.

This specification must be implemented only for products and jurisdictions where the platform is legally permitted and the relevant provider/platform policies allow the functionality.

The engineering team must re-check current official exchange, broker, payment, app-store, cloud, security, and regulatory documentation before implementation because third-party policies and APIs can change.

---

# 7.0 END-TO-END SYSTEM PRINCIPLE

The customer experience may look simple:

BUY → CONNECT → CONFIGURE → ANALYZE → RISK CHECK → SIGNAL → EXECUTE → MONITOR

The actual architecture must be:

IDENTITY → ENTITLEMENT → ELIGIBILITY → PROVIDER CONNECTIVITY → DATA VALIDATION → STRATEGY → RESEARCH → AI ENSEMBLE → DETERMINISTIC RISK → POLICY → EXECUTION → RECONCILIATION → JOURNAL → NOTIFICATION → AUDIT

No critical financial action may skip a stage without an explicit, versioned policy that allows the shortcut.

# 7.1 SERVICE OWNERSHIP MAP

AUTH SERVICE
- login
- sessions
- MFA
- passkeys
- account recovery

ACCOUNT SERVICE
- user profile
- preferences
- risk profile

CATALOG SERVICE
- products
- market modes
- pricing

SUBSCRIPTION SERVICE
- subscription state
- renewal state

ENTITLEMENT SERVICE
- capability calculation

COMPLIANCE SERVICE
- jurisdiction
- platform eligibility
- product restrictions

CONNECTION SERVICE
- provider connections
- secure secret references
- credential health

MARKET DATA SERVICE
- quotes
- candles
- order-book snapshots
- reference data

NEWS/FUNDAMENTAL SERVICE
- approved data ingestion
- provenance
- event normalization

STRATEGY SERVICE
- strategy versions
- strategy lifecycle
- signal candidates

AI ORCHESTRATOR
- strategy analysis
- risk analysis assistance
- research analysis
- ensemble orchestration

RISK SERVICE
- deterministic risk decisions
- sizing
- hard limits

SIGNAL SERVICE
- final signal construction
- publication
- expiration

EXECUTION SERVICE
- order lifecycle
- provider submission
- provider state

RECONCILIATION SERVICE
- provider/internal state comparison

PORTFOLIO SERVICE
- positions
- exposure
- P/L

NOTIFICATION SERVICE
- push
- email
- in-app

AUDIT SERVICE
- immutable operational evidence

ADMIN SERVICE
- privileged control-plane actions

# 7.2 COMMAND VS EVENT

Commands express intent.

Examples:
CreateSubscription
ConnectProvider
ValidateConnection
EnableAutomation
PauseAutomation
RequestSignal
SubmitOrder
CancelOrder
ChangeRiskProfile

Events express facts.

Examples:
SubscriptionActivated
ConnectionValidated
AutomationPaused
SignalCreated
OrderSubmitted
OrderFilled
OrderRejected

Never pretend a command is already a fact.

# 7.3 PURCHASE-TO-ACTIVATION STATE MACHINE

USER:
SELECT PRODUCT → SELECT MARKET → SELECT MODE → SELECT TERM → REVIEW → PAYMENT → PAYMENT CONFIRMED → ENTITLEMENT REQUESTED → ELIGIBILITY CHECK → ENTITLEMENT ACTIVATED → CONNECTION REQUIRED → CONNECTION VALIDATED → SERVICE READY

Possible states:
CHECKOUT_CREATED
PAYMENT_PENDING
PAYMENT_CONFIRMED
ENTITLEMENT_PENDING
ENTITLEMENT_ACTIVE
ENTITLEMENT_RESTRICTED
PAYMENT_REVERSED
SUBSCRIPTION_EXPIRED
SUBSCRIPTION_CANCELLED

No frontend success page can create entitlement.

# 7.4 ENTITLEMENT RESOLUTION

Function:
resolveEntitlement(user, subscription, paymentState, product, marketMode, platform, jurisdiction, complianceState, providerCapabilities)

Output:
capabilitySet
policyVersion
decision
reasonCodes
expiry

Example:
signals.crypto.spot = true
automation.crypto.spot = true
signals.crypto.futures = true
automation.crypto.futures = false
forex.mt5 = true
binary_options = false

# 7.5 PROVIDER CONNECTION STATES

NEW
SUBMISSION_RECEIVED
VALIDATING
VALID
INVALID
PERMISSION_MISMATCH
RATE_LIMITED
TEMPORARILY_UNAVAILABLE
REVOKED
EXPIRED
SUSPENDED
DISCONNECTED

Every state transition is auditable.

# 7.6 CONNECTION VALIDATION PIPELINE

1. syntax validation
2. secret vault write
3. provider authentication
4. account identification
5. permission discovery
6. required-permission validation
7. unsupported-permission validation
8. market capability discovery
9. risk capability discovery
10. connection health test
11. store safe metadata
12. mark connection ready

Do not submit a trade during credential validation.

# 7.7 API PERMISSION POLICY

Minimum permissions:
READ_ACCOUNT
READ_BALANCE
READ_POSITIONS
READ_ORDERS
TRADE

Avoid WITHDRAW.

If provider supports IP allowlisting, sub-account permissions, or trade-only scopes, recommend or enforce them according to provider capabilities and product policy.

# 7.8 SECRET VAULT BOUNDARY

The application database stores:
secret_reference
provider
connection_id
status

The vault stores:
API_KEY
API_SECRET
provider-specific sensitive material

Only the minimum execution adapter may request the secret.

Admin services must not receive raw secret values.

Audit events store secret_reference, not secret.

Modern secrets-management guidance recommends centralized, controlled storage, provisioning, auditing and rotation. See OWASP Secrets Management guidance: https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html

# 7.9 SECRET ACCESS AUTHORIZATION

Secret access requires:
service identity
provider scope
connection scope
purpose
short-lived access
audit

Examples:
execution-service → read secret for order operation
support-service → NOT ALLOWED
content-service → NOT ALLOWED
analytics-service → NOT ALLOWED
AI-service → NOT ALLOWED
admin-mobile → NOT ALLOWED

# 7.10 CUSTOMER SECRET REPLACEMENT

Flow:
REPLACE → STEP-UP AUTH → NEW SECRET SUBMITTED → VALIDATE NEW → SWITCH → REVOKE OLD REFERENCE → AUDIT

Do not delete the old credential reference before the new connection has been validated when that could create accidental loss of connectivity.

# 7.11 CONNECTION HEALTH

HEALTHY
DEGRADED
STALE
AUTH_ERROR
PERMISSION_ERROR
PROVIDER_DOWN
UNKNOWN

Health is computed from:
last successful request
provider status
latency
authentication state
permission state
market capability

# 7.12 MARKET DATA CONTRACT

Every data packet:
provider
venue
instrument
eventTime
receivedTime
sequence
bid
ask
last
volume
quality
latency

Candles:
open
high
low
close
volume
openTime
closeTime
provider
timeframe

# 7.13 DATA FRESHNESS

Define thresholds by use case.

Execution threshold must be stricter than dashboard threshold.

If data exceeds threshold:
mark stale
block affected action where configured

# 7.14 MARKET DATA FAILOVER

Primary provider → Provider A
Fallback → Provider B

When switching:
mark provider switch
revalidate symbol mapping
revalidate timestamp compatibility
recalculate data quality

# 7.15 SYMBOL NORMALIZATION

Provider symbols such as BTCUSDT, BTC/USDT, XBTUSD, or BTCUSD.P may refer to different instruments.

Internal canonical instrument must map:
asset class
base
quote
venue
contract type

Never assume string equality means instrument equality.

# 7.16 CRYPTO SPOT ENGINE

Model:
asset quantity
average acquisition cost
realized P/L
unrealized P/L
fees
available quantity
locked quantity

Rules:
- no liquidation mechanics
- provider-specific precision
- minimum order size

# 7.17 CRYPTO DERIVATIVE ENGINE

Model may include:
contract
position side
quantity
entry price
mark price
initial margin
maintenance margin
leverage
funding
liquidation price
realized P/L
unrealized P/L

Never expose leverage without explaining liquidation and loss amplification.

# 7.18 HYBRID ENGINE

Hybrid means the platform may evaluate both spot and derivative strategies according to configured eligibility.

It does NOT mean automatically trade both simultaneously.

# 7.19 FOREX / MT5 ENGINE

Concepts:
broker
account
symbol
lot
contract size
point
tick value
margin
leverage
stop level
volume step

Do not import crypto terminology into MT5.

For MT5, strategy may be represented by an Expert Advisor (EA) or a controlled bridge. Execution architecture must clearly identify broker-side execution, bridge responsibility, and platform responsibility.

# 7.20 MT5 DEPLOYMENT MODEL

Possible models:
A. Broker/terminal-managed EA
B. Controlled bridge
C. VPS/remote terminal integration

The selected model must be documented.

Never store broker credentials in frontend code.

# 7.21 STRATEGY REQUEST

A signal request should include:
strategyVersion
instrument
marketMode
timeframe
accountContext
marketSnapshot
riskContext
dataQuality

Do not pass unnecessary PII into strategy computation.

# 7.22 STRATEGY PRECONDITIONS

Before evaluation:
subscription active
product eligible
provider connected if required
market data fresh
strategy enabled
instrument supported
risk policy available

If any critical precondition fails:
NO_TRADE / NO_SIGNAL as appropriate.

# 7.23 STRATEGY EVALUATION

Output:
candidateDirection
entryCondition
invalidCondition
timeHorizon
confidence
requiredRiskControls
strategyReasonCodes

Strategy engine must not directly place orders.

# 7.24 FUNDAMENTAL RESEARCH PIPELINE

Sources:
approved news providers
economic calendars
public filings where applicable
official announcements
curated data providers

Each source has quality score and timestamp.

# 7.25 NEWS NORMALIZATION

Input:
headline
body
publisher
publication time
retrieval time
language
entities
event type

Output:
normalized event
relevance
confidence
source quality
market relevance

# 7.26 CONFLICTING SOURCES

If sources conflict:
do not average facts.

Mark:
CONFLICTING_EVIDENCE

Lower data quality.
Require additional validation.

# 7.27 RESEARCH AGENT CONTRACT

Returns:
market_context
relevant_events
fundamental_context
risk_flags
source_references
confidence
data_quality

It cannot:
change risk limits
submit orders
activate automation
override compliance

# 7.28 STRATEGY AGENT CONTRACT

Returns:
candidate_direction
setup_quality
invalidation
time_horizon
confidence
strategy_reason_codes

No execution authority.

# 7.29 RISK AGENT CONTRACT

May recommend:
risk_score
recommended_quantity
max_quantity
risk_flags
capital_allocation

Final hard limits are implemented independently by deterministic risk code.

# 7.30 AI ENSEMBLE

Inputs:
strategy output
risk analysis
research output
technical features
market state
portfolio exposure
data quality

Output:
candidate_signal
consensus
uncertainty
risk_flags

Then:
DETERMINISTIC RISK → POLICY → FINAL SIGNAL

# 7.31 ANALYST ENSEMBLE

A 16-dimensional analytical layer is acceptable only if actually implemented.

Possible dimensions:
Trend
Momentum
Volatility
Volume
Market Structure
Liquidity
Order Flow
Support/Resistance
Multi-Timeframe
Regime
Correlation
Sentiment
Fundamental
News
Risk
Execution Quality

Each output:
score
direction
confidence
timestamp
dataQuality

# 7.32 ENSEMBLE DISAGREEMENT

Calculate:
agreement
disagreement
spread
dominant direction

If disagreement is too high:
NO_TRADE

Never force a signal merely because a signal is requested.

# 7.33 SIGNAL GENERATION

Final signal fields:
signalId
instrument
product
marketMode
direction
strength
confidence
riskLevel
dataQuality
strategyVersion
ensembleVersion
generatedAt
expiresAt
status

# 7.34 SIGNAL STATUS

CANDIDATE
VALIDATING
PUBLISHED
EXPIRED
INVALIDATED
SUPERSEDED
CANCELLED

# 7.35 SIGNAL EXPIRATION

Every signal must have expiration rules based on time, price movement, market regime, data quality, strategy invalidation, and/or material news events as configured.

Expired signals must never be presented as current.

# 7.36 RISK ENGINE

Deterministic inputs:
equity
available margin
existing exposure
instrument volatility
stop distance
leverage
fees
slippage
correlation
max account risk
max position risk
daily loss
concentration
strategy limit

Output:
ALLOW
ALLOW_WITH_REDUCTION
REJECT

# 7.37 HARD RISK LIMITS

Hard limits override:
AI
strategy
admin preference
customer preference

Example:
strategy asks quantity 10
risk maximum 4
approved quantity = 4

# 7.38 RISK REASONS

RISK_TOO_HIGH
EXPOSURE_LIMIT
LEVERAGE_LIMIT
MARGIN_LIMIT
DATA_STALE
SPREAD_TOO_HIGH
VOLATILITY_TOO_HIGH
DAILY_LOSS_LIMIT
CONCENTRATION_LIMIT
STRATEGY_DISABLED
PROVIDER_UNAVAILABLE
COMPLIANCE_RESTRICTION

# 7.39 POSITION SIZE CALCULATION

Conceptual:
riskBudget ÷ riskPerUnit = rawQuantity

Then constrain by:
exchange minimum
exchange maximum
lot step
margin
leverage
portfolio limit

Use exact decimal arithmetic.

# 7.40 STOP LOSS POLICY

Where a strategy requires a stop:
risk engine validates that the stop exists, stop distance is valid, and risk amount is acceptable.

If required protection cannot be established:
REJECT

# 7.41 SLIPPAGE

Estimate historical, provider, and market-state slippage where appropriate.

If expected slippage exceeds threshold:
reject or reduce size.

# 7.42 FEES

Support:
trading fees
funding
broker commission
spread
conversion
provider-specific charges

# 7.43 ORDER BUILDING

Only after:
entitlement
eligibility
connection
data
strategy
risk
policy

Build:
provider order type
side
quantity
price
stop
take-profit
reduce-only where applicable
client order ID

# 7.44 PRE-SUBMISSION VALIDATION

Validate:
instrument
provider capability
precision
quantity
price
side
position mode
margin
risk
stale data
idempotency
user status

If any fails:
do not submit.

# 7.45 IDEMPOTENT ORDER SUBMISSION

Generate clientOrderId and persist it before provider submission.

If request times out:
do not create a new order ID and retry blindly. First query provider state.

# 7.46 ORDER UNKNOWN STATE

State:
UNKNOWN

Triggers:
provider timeout
connection loss after submit
malformed response
provider ambiguity

Process:
FREEZE DUPLICATE → QUERY PROVIDER → RECONCILE → RESOLVE

# 7.47 ORDER STATE MACHINE

CREATED
VALIDATING
RISK_CHECK
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

Never skip from CREATED to FILLED in internal records without provider evidence.

# 7.48 PARTIAL FILLS

Track:
requested quantity
filled quantity
remaining quantity
average fill price
fee
provider execution IDs

Risk engine must know actual filled exposure.

# 7.49 CANCEL POLICY

Cancellation is a command, not an assumption.

After cancel request, query provider.

Possible:
cancelled
partially filled
filled
unknown

# 7.50 OPEN POSITIONS

Position service consumes:
fills
provider position snapshots
reconciliation

Positions should not be calculated only from the customer's UI.

# 7.51 BALANCE SYNCHRONIZATION

Sync:
available
locked
equity
margin
wallet balances

Mark timestamp.

A stale balance cannot authorize a high-risk new trade.

# 7.52 PORTFOLIO EXPOSURE

Calculate:
per instrument
per asset
per strategy
per market mode
per provider
total portfolio

Use canonical instrument IDs.

# 7.53 CORRELATION RISK

Do not treat multiple positions as independent if they are strongly correlated.

Correlation layer may reduce position size or total exposure, or reject.

# 7.54 STRATEGY CONCENTRATION

Limit:
strategy-level exposure
instrument-level exposure
theme-level exposure

Five signals sharing the same underlying risk are not five independent risks.

# 7.55 MARKET REGIME

Classify conceptually:
TRENDING
RANGING
HIGH_VOLATILITY
LOW_VOLATILITY
DISLOCATED
UNKNOWN

Strategy may be enabled only for supported regimes.
UNKNOWN should be conservative.

# 7.56 EMERGENCY PAUSE CONDITIONS

Examples:
provider authentication failure
provider outage
market-data corruption
large unexpected spread
abnormal volatility
risk-engine failure
reconciliation backlog
security incident
strategy drawdown limit

# 7.57 GLOBAL PAUSE

Global pause blocks new automation.

It should not necessarily close positions or cancel all orders. Semantics must be explicit.

# 7.58 STRATEGY PAUSE

Pause one strategy version.

Existing positions are unaffected unless a separate risk policy says otherwise.

# 7.59 CUSTOMER PAUSE

Pause one customer's automation.
Other customers remain unaffected.

# 7.60 PROVIDER PAUSE

Pause one provider.
Signals may continue if signal-only service is still appropriate.
Automated execution through that provider stops.

# 7.61 MARKET PAUSE

Pause one market mode or instrument class.

Example:
crypto-futures automation disabled
crypto-spot signals remain active

# 7.62 RECONCILIATION ENGINE

Periodic cycle:
1. fetch provider orders
2. fetch provider fills
3. fetch positions
4. fetch balances
5. compare
6. identify differences
7. resolve automatically where safe
8. escalate exceptions
9. audit

# 7.63 RECONCILIATION FREQUENCY

Use event-driven updates where supported plus periodic full checks.

Do not assume WebSocket alone is infallible.

# 7.64 RECONCILIATION RESULT

MATCHED
AUTO_CORRECTED
MISMATCH
REQUIRES_REVIEW
PROVIDER_UNAVAILABLE

# 7.65 RECONCILIATION MISMATCH TYPES

ORDER_MISSING
ORDER_EXTRA
FILL_MISMATCH
QUANTITY_MISMATCH
PRICE_MISMATCH
FEE_MISMATCH
POSITION_MISMATCH
BALANCE_MISMATCH
STATE_MISMATCH

# 7.66 RECONCILIATION SAFETY

Never:
delete provider orders to match database
fabricate internal fills
silently alter historical state

Instead:
record correction event
create adjustment
retain original evidence

# 7.67 JOURNAL MODEL

Each customer trading timeline should connect:
signal
risk decision
order
fill
position
outcome
market context

# 7.68 EXECUTION JOURNAL

For each order:
what happened
when
which strategy
which signal
which risk policy
which provider
which execution adapter
which result

# 7.69 PERFORMANCE ATTRIBUTION

Attribute outcomes to:
strategy
instrument
market mode
signal
execution venue
time period

Avoid claiming that AI caused a profit without rigorous attribution.

# 7.70 PERFORMANCE DISPLAY RULE

Every performance metric needs:
period
methodology
fees treatment
sample size where relevant

If insufficient data:
DO NOT DISPLAY a misleading percentage.

# 7.71 SIGNAL FEEDBACK LOOP

After outcome capture:
signal correctness
execution quality
risk result
market regime
data quality

Feed into:
evaluation
strategy research
risk tuning

Do not automatically rewrite production strategy based on one outcome.

# 7.72 MODEL EVALUATION

Track model:
precision
recall where meaningful
calibration
abstention
false positives
false negatives
risk-adjusted contribution

Do not judge by return alone.

# 7.73 BACKTEST DATA LEAKAGE CONTROL

Prevent:
look-ahead bias
survivorship bias
future information leakage
timestamp mismatch

Freeze datasets for reproducibility.

# 7.74 WALK-FORWARD VALIDATION

Use training, validation and out-of-sample periods where applicable.

Never publish a backtest without defining methodology.

# 7.75 PAPER TRADING ENGINE

Paper environment shares:
same signal logic
same risk logic
same order state machine

Only the execution adapter is simulated.

# 7.76 SIMULATION FAILURE MODES

Simulate:
timeout
reject
partial fill
slippage
provider outage
stale data
duplicate webhook
out-of-order event

# 7.77 PROVIDER RATE LIMITING

Every adapter understands provider limits.

Scheduler should:
queue
throttle
prioritize
backoff

Do not let one customer starve the provider connection pool.

# 7.78 REQUEST PRIORITY

Priority classes:
SECURITY
EXECUTION
RECONCILIATION
ACCOUNT
ANALYTICS

Execution and reconciliation receive controlled priority.

# 7.79 PROVIDER ERROR MAPPING

Map provider errors to internal:
code
retryability
severity
customer message

Do not expose raw API error content blindly.

# 7.80 NOTIFICATION ON TRADING EVENTS

Generate notifications for:
signal
order submitted
order filled
order rejected
order unknown
position changed
automation paused
risk limit reached
provider disconnected

# 7.81 NOTIFICATION SAFETY

Security-critical notifications cannot be suppressed merely because marketing is disabled.

# 7.82 ADMIN CONTROL EVENTS

Admin actions that change trading behavior:
strategy pause
strategy resume
risk policy change
provider disable
global automation pause
entitlement correction

Must generate high-severity audit events.

# 7.83 ADMIN COMMAND SAFETY

Every high-risk admin command requires:
authorization
scope preview
confirmation
reason
audit

Dual approval where configured.

# 7.84 AUDIT CORRELATION

A single trading decision should be traceable via one correlationId across:
API
strategy
AI
risk
execution
provider
reconciliation
notification

# 7.85 REQUEST TRACE

Sample:
REQUEST → signal-service → strategy-service → AI-orchestrator → risk-service → policy-service → execution-service → exchange-adapter

All retain correlation ID.

# 7.86 NO-TRADE EXPLANATION

No-trade results are first-class outcomes.

Examples:
NO_TRADE_DATA_STALE
NO_TRADE_RISK
NO_TRADE_MARKET_UNSUPPORTED
NO_TRADE_ENTITLEMENT
NO_TRADE_STRATEGY
NO_TRADE_POLICY
NO_TRADE_PROVIDER

# 7.87 SAFETY-FIRST SIGNAL MODE

When:
AI unavailable
news unavailable
one provider unavailable
data quality degraded

possible result:
DEGRADED
or
NO_SIGNAL

Never fill missing inputs with imagined data.

# 7.88 AI UNAVAILABLE MODE

If AI is optional, deterministic strategy may continue only if policy explicitly permits it.
If AI consensus is mandatory, return NO_SIGNAL.

# 7.89 MODEL TIMEOUT

If model exceeds timeout:
cancel request
record timeout
apply fallback policy

Do not wait indefinitely in an execution path.

# 7.90 AI OUTPUT VALIDATION

Reject:
missing fields
wrong types
invalid numeric ranges
unknown actions
unsupported instruments
impossible quantities

# 7.91 MODEL CONFIDENCE

Confidence is a model signal, not a guarantee.

UI copy:
“Model confidence score”
not:
“Probability of profit”
unless a separately validated statistical interpretation supports the wording.

# 7.92 DATA PROVENANCE

Every decision can identify:
market snapshot
strategy version
risk policy
AI model versions
research references
provider

# 7.93 STRATEGY VERSION IMMUTABILITY

Once production-active:
strategy version cannot be silently mutated.

Changes create a new version.

# 7.94 RISK VERSION IMMUTABILITY

Historical decisions retain the policy version used.

Never retroactively rewrite old risk decisions.

# 7.95 PRODUCT CATALOG MODEL

Product:
SIGNALS
AUTOMATION

Market:
CRYPTO
FOREX
OTHER_SUPPORTED

Mode:
SPOT
DERIVATIVES
HYBRID
MT5
OTHER_PROVIDER_MODE

Term:
7D
1M
3M
6M
12M

Eligibility is dynamic.

# 7.96 ADMIN PRODUCT CONFIG

Authorized users can configure:
product title
description
availability
pricing
term
capabilities
supported providers
region rules
platform rules

Risk policies are separate from cosmetic product configuration.

# 7.97 PAYMENT-ENTITLEMENT RACE CONDITION

Payment confirmed → frontend still loading → user opens dashboard.

Backend checks authoritative payment and entitlement state; UI cannot grant access.

# 7.98 REFUND / CHARGEBACK

Possible state transition:
ACTIVE → REFUND_PENDING → REFUNDED → ENTITLEMENT_REVIEW → SUSPENDED/EXPIRED

# 7.99 SUBSCRIPTION EXPIRATION

At expiration:
entitlement becomes inactive.

Safe default:
block new automated orders.

Existing positions remain governed by the provider/account and separately documented operational policy.

# 7.100 CONNECTION DISCONNECT

If subscription expires or user disconnects:
stop new automated orders.

Existing exchange positions remain at provider until a deliberate supported workflow changes them.

# 7.101 USER DELETE ACCOUNT

Before deletion:
- stop automation
- revoke sessions
- revoke connection references
- follow data-retention requirements
- preserve required records
- anonymize where appropriate

# 7.102 CUSTOMER PAUSE VS DISCONNECT

PAUSE = keep provider connection
DISCONNECT = revoke provider access / stop using credential reference

Make the distinction obvious.

# 7.103 SAFE DEACTIVATION

1. pause new execution
2. stop signal-to-order pipeline for the account
3. preserve state
4. reconcile open orders
5. revoke or retain connection based on action
6. audit
7. notify user

# 7.104 REACTIVATION

Before resuming:
subscription active
eligibility active
provider healthy
credential valid
market data fresh
risk policy present
strategy active
no incident lock

# 7.105 INCIDENT LOCK

When an account/strategy/provider is under incident lock, resume is blocked until cleared.

# 7.106 OPERATIONAL LOCKS

USER_LOCK
PROVIDER_LOCK
STRATEGY_LOCK
MARKET_LOCK
GLOBAL_LOCK
SECURITY_LOCK
COMPLIANCE_LOCK

Multiple locks may coexist.

Resume only when all applicable locks clear.

# 7.107 CUSTOMER-FACING STATUS MODEL

ACTIVE
PAUSED
CONNECTING
DEGRADED
BLOCKED
REQUIRES_ACTION
EXPIRED

Internal status remains richer.

# 7.108 ADMIN INTERNAL STATUS

Admin can inspect detailed reason codes such as:
BLOCKED → PROVIDER_PERMISSION_MISMATCH → RISK_POLICY_MISSING → POLICY_REGION_RESTRICTION

# 7.109 API EXAMPLES

GET /api/v1/entitlements

returns:
capabilities
policyVersion
expiresAt

POST /api/v1/connections

returns:
connectionId
status
nextAction

POST /api/v1/signals/request

returns:
requestId
status

POST /api/v1/orders

returns:
executionId
status

Never return raw credentials.

# 7.110 SIGNAL REQUEST API

POST /api/v1/signals/request

Input:
instrument
marketMode
strategyId

Server derives:
user
subscription
entitlement
risk profile
connection capability

Never trust client-supplied:
userId
riskLimit
permission
entitlement

# 7.111 ORDER API

POST /api/v1/orders

Required:
signalId
connectionId
intent

Server derives:
user
entitlement
risk
provider
permissions

Client may not set:
finalApprovedQuantity
policyVersion
riskApproved=true

# 7.112 ADMIN API

Admin endpoints require:
admin authentication
role
permission
resource access
audit

Never reuse customer endpoints plus an isAdmin boolean as authorization.

# 7.113 API SECURITY ALIGNMENT

OWASP API Security Top 10 identifies major API risks including broken object-level authorization, broken function-level authorization, unrestricted sensitive business flows, SSRF, security misconfiguration, improper inventory management, and unsafe consumption of APIs. The architecture must address them explicitly. citeturn657281search0turn657281search1

For all object-ID endpoints:
perform server-side ownership/permission checks.

For sensitive flows:
rate-limit, authorize, audit, and apply business-level abuse protection.

# 7.114 SSRF PROTECTION

For features that fetch URLs:
allowlist domains
block private IP ranges
block metadata endpoints
limit redirects
validate scheme
limit response size
time out
log safe metadata

Do not allow arbitrary customer-provided URL fetching.

# 7.115 THIRD-PARTY API TRUST

External provider data is untrusted input.

Validate:
schema
numeric bounds
timestamps
identifiers
status values

Never execute instructions found in external text.

# 7.116 PROVIDER INVENTORY

Maintain registry:
provider
API version
base URL
capabilities
limits
status
adapter version
last reviewed date

Retired endpoints must be removed.

# 7.117 ADAPTER CONTRACT TESTS

For every provider:
authentication
balance
positions
order
cancel
order lookup
reconciliation
errors
rate limits

Use provider sandbox where available.

# 7.118 CONTRACT TEST DATA

Maintain fixtures for:
valid
invalid
edge
timeout
partial
duplicate
stale
malformed

No real customer data.

# 7.119 SECURITY RUNBOOK

If secret compromise is suspected:
1. pause affected connections
2. revoke provider credentials where appropriate
3. revoke sessions
4. preserve audit evidence
5. assess scope
6. notify customer where required
7. rotate credentials
8. investigate
9. document
10. resume cautiously

# 7.120 TRADING INCIDENT RUNBOOK

If a wrong-order incident is suspected:
1. global or scoped pause
2. prevent duplicates
3. freeze mutable strategy configuration
4. capture provider records
5. reconcile
6. determine exposure
7. notify incident lead
8. assess customer impact
9. correct only with evidence
10. postmortem
11. improve tests

# 7.121 POST-INCIDENT REVIEW

Document:
timeline
cause
impact
detection
response
recovery
customer impact
preventive controls
tests added

# 7.122 DRILLS

Run tabletop exercises for:
provider outage
database outage
AI provider outage
payment outage
secret compromise
credential leakage
wrong strategy deployment
reconciliation backlog
global pause

# 7.123 OPERATIONAL SLA

Define internally:
availability targets
RPO
RTO
signal latency
reconciliation latency
incident response

Do not publicly promise 100% uptime without a genuine contractual basis and supporting architecture.

# 7.124 CUSTOMER TRANSPARENCY

When automated trading is paused, show why at an appropriate level.

Example:
“Automation is temporarily paused because the connected provider is unavailable.”

Not:
“AI decided to stop trading.”

# 7.125 ADMIN TRANSPARENCY

Admin can see:
provider health
decision chain identifiers
risk decision
strategy version
AI status
policy version

But not customer secrets.

# 7.126 PRODUCT AVAILABILITY MATRIX

Required generated matrix:

WEB
PWA
ANDROID
IOS
WINDOWS
MACOS

For each:
SIGNALS
CRYPTO_SPOT
CRYPTO_DERIVATIVES
FOREX_MT5
BINARY_OPTIONS

Use:
AVAILABLE
RESTRICTED
UNAVAILABLE
REGION_DEPENDENT

Google Play's current Financial Services policy requires applicable regional compliance and a Financial Features declaration, and explicitly does not allow apps that provide users the ability to trade binary options. citeturn657281search6turn657281search15

Therefore, store distribution must never depend on disguising a prohibited feature.

# 7.127 WEB-ONLY CAPABILITY

Where a capability cannot be distributed through a particular native store, the web product may be a separate eligible distribution channel only after legal and platform review.

The mobile app must not be used as a disguised launcher for prohibited functionality.

# 7.128 CUSTOMER JOURNEY — SIGNAL ONLY

1 register
2 verify
3 select Signals
4 choose market
5 choose mode
6 select subscription term
7 pay
8 open dashboard
9 view signals
10 inspect evidence
11 receive notifications
12 maintain connection only if needed for account-context analytics

# 7.129 CUSTOMER JOURNEY — AUTOMATION

1 register
2 verify
3 select Automation
4 choose market
5 choose mode
6 select term
7 pay
8 entitlement activates
9 connect provider
10 validate
11 configure risk
12 enable automation
13 strategy evaluates
14 risk engine approves/rejects
15 execution occurs if permitted
16 reconcile
17 notify
18 journal

# 7.130 CUSTOMER JOURNEY — FAILED PAYMENT

checkout → payment failed → entitlement not activated → user remains in checkout state → safe retry

Never: payment failed → premium activated.

# 7.131 CUSTOMER JOURNEY — INVALID API

connect → validation failed → no order → explain → replace credentials

# 7.132 CUSTOMER JOURNEY — PROVIDER OUTAGE

automation active → provider unavailable → new execution paused → positions preserved → customer notified → reconciliation after recovery → controlled resume

# 7.133 CUSTOMER JOURNEY — STALE MARKET DATA

data stale → signal suppressed or marked stale → automation paused where required → UI says stale → after recovery revalidate → resume

# 7.134 CUSTOMER JOURNEY — RISK REJECTION

signal generated → risk rejects → no order submitted → customer sees reason → audit records risk policy

# 7.135 CUSTOMER JOURNEY — UNKNOWN ORDER

order requested → provider timeout → internal UNKNOWN → duplicate blocked → reconciliation → final state

This is a critical acceptance test.

# 7.136 CUSTOMER JOURNEY — SUBSCRIPTION EXPIRATION

expiration → entitlement revoked → new automated orders blocked → user notified → existing positions handled by separate documented policy

# 7.137 CUSTOMER JOURNEY — ADMIN PAUSE

authorized admin → strategy pause → confirmation → audit → event → execution blocked → customer status updated

# 7.138 ADMIN COMMAND IDEMPOTENCY

Sensitive admin commands use idempotency too.

Repeated PAUSE_STRATEGY requests produce the same final state rather than repeated side effects.

# 7.139 DATABASE CONSISTENCY

For critical transitions, use transaction boundaries carefully.

Example:
create payment event → update payment → emit entitlement request

Do not mark entitlement active before payment state is authoritative.

# 7.140 OUTBOX PATTERN

For important events:
write domain state and outbox event in one transaction.
Then publish asynchronously.

# 7.141 INBOX / DEDUP PATTERN

Consumers record processed IDs, especially for:
payments
provider webhooks
execution events
entitlement events

# 7.142 WEBHOOK ORDERING

Provider events may arrive out of order.

Use provider timestamp, event sequence where available, current object query, and state-transition rules.

# 7.143 CLOCK SKEW

Use server time and validated provider timestamps with reasonable tolerance.

Never use browser clock for financial authorization.

# 7.144 RATE / PRICE VALIDATION

Before submitting an order, check current provider quote.

Do not use an old UI price.

# 7.145 QUOTE EXPIRY

An intended execution price has a validity window. If exceeded, reprice/revalidate.

# 7.146 PRICE PROTECTION

Where supported:
slippage tolerance
limit price
stop constraints

Use explicit policy.

# 7.147 MARKET CLOSED

If provider says market closed:
REJECT.

Do not retry endlessly.

# 7.148 MINIMUM ORDER

If below provider minimum, lot minimum, or notional minimum:
NO_TRADE or user-facing configuration correction.

# 7.149 MAXIMUM ORDER

Never let client choose above provider max, risk max, strategy max, or account max.

# 7.150 LEVERAGE POLICY

Leverage selection is a risk configuration.

Never default to maximum leverage.

If provider does not allow programmatic leverage control, document actual provider behavior.

# 7.151 FUNDING / FINANCING COSTS

Derivative systems should account for funding, borrow, swap, and financing where applicable.

# 7.152 FEES DISPLAY

Use:
estimated fee
actual fee

Never confuse them.

# 7.153 SLIPPAGE DISPLAY

Show expected and actual when data is available.

# 7.154 EXECUTION QUALITY SCORE

Optional metric based on slippage, latency, fill quality, and provider response.

Do not represent it as profitability.

# 7.155 PROVIDER HEALTH SCORE

Use:
UP
DEGRADED
DOWN

Do not expose a fake percentage without methodology.

# 7.156 SYSTEM HEARTBEAT

Each critical service publishes health.

Admin sees:
healthy
warning
critical

Stale heartbeat means unhealthy.

# 7.157 QUEUE HEALTH

Monitor:
queue depth
oldest age
processing rate
failure rate

Trading backlog must trigger alerts.

# 7.158 RECONCILIATION BACKLOG

Metric:
open reconciliation cases
age
severity

High backlog may trigger automation pause.

# 7.159 AI BACKLOG

Monitor:
requests
latency
timeouts
cost
failed schema
provider availability

# 7.160 SECURITY TELEMETRY

Monitor:
failed login
MFA failure
session anomalies
secret access attempts
admin privilege changes
suspicious API activity

# 7.161 FRONTEND-TO-BACKEND TRUST MODEL

Frontend says:
“I would like to execute.”

Backend decides:
“Is this permitted?”

Frontend says:
“User selected risk 20.”

Backend decides:
“Is 20 within policy?”

Frontend says:
“Payment succeeded.”

Backend decides:
“Provider/webhook confirms payment.”

Never reverse this trust relationship.

# 7.162 MOBILE-TO-BACKEND TRUST MODEL

Mobile application is untrusted.

Do not embed production risk secrets, admin credentials, or provider API secrets.

Every request is reauthorized server-side.

# 7.163 ADMIN-TO-BACKEND TRUST MODEL

Admin app is privileged client.
Still:
authenticate
authorize
audit
scope
confirm

Do not trust an admin app merely because package name matches.

# 7.164 SECRET REVEAL POLICY

Preferred policy:
CUSTOMER → can enter secret
SYSTEM → can use secret when authorized
ADMIN → cannot view raw secret

Any unavoidable exception requires break-glass controls.

# 7.165 BREAK-GLASS

Break-glass access:
disabled by default
strong authentication
reason
approval
short TTL
full audit
automatic expiration

# 7.166 CUSTOMER DATA ISOLATION

Every query must include authorization scope.

Avoid “SELECT all then filter frontend.”

Prefer server-scoped queries.

# 7.167 SUPPORT DATA ISOLATION

Support sees minimum required.
Customer secrets: NEVER.

# 7.168 ANALYTICS DATA ISOLATION

Analytics uses pseudonymous identifiers and aggregated data where possible.
Do not dump the production database into analytics.

# 7.169 TESTING END-TO-END

Required scenarios:
purchase success
purchase failure
entitlement delay
connection success
connection failure
permission mismatch
market data stale
signal generated
signal suppressed
risk approved
risk rejected
order filled
order rejected
partial fill
unknown order
reconciliation
provider outage
subscription expiration
refund
admin pause
security lock

# 7.170 CHAOS TESTING

Simulate:
network failure
database latency
queue delay
provider timeout
duplicate events
AI timeout
news provider outage

Objective:
prove safe degradation.

# 7.171 LOAD TESTING

Load:
authentication
public content
signals
dashboard
notifications
provider adapters

Execution endpoints require carefully controlled sandbox/simulation load tests.

# 7.172 CAPACITY PLANNING

Define:
expected users
concurrent users
signals/minute
orders/minute
provider calls/minute
AI requests/minute
webhooks/minute

Use actual measurements.

# 7.173 TENANCY

Customer isolation is required even if infrastructure is initially single-tenant.
Design every business object around user/account ownership.

# 7.174 FUTURE B2B

Do not prematurely build enterprise complexity, but leave room for organization, team, role, sub-account, and billing account concepts.

# 7.175 DOCUMENTATION CONTRACT

Every service documents:
purpose
owned data
API
events
dependencies
failure states
security
SLO
runbook
owner

# 7.176 ARCHITECTURE DECISION RECORDS

Record why technologies, providers, data stores, queues, AI providers, secret managers, and cross-platform frameworks were selected.

# 7.177 PROVIDER REVIEW CADENCE

For every critical provider:
review official documentation before major release.

Track:
last checked
checked by
API version
policy changes
required actions

# 7.178 STORE POLICY REVIEW

Maintain a platform-policy register.

At minimum:
Google Play
Apple App Store
Microsoft Store if applicable
Cafe Bazaar
Myket

For every store record:
financial services rules
billing rules
content rules
permissions
privacy
restricted features
submission requirements

Unknown local-store requirements must be marked:
MANUAL VERIFICATION REQUIRED.

# 7.179 COMPLIANCE CHANGE MANAGEMENT

If store or policy rules change, update eligibility, feature matrix, marketing copy, submission package, backend gating, and test cases.

Do not solve policy changes by hiding functionality.

# 7.180 PRODUCTION CUTOVER

Order:
1 infrastructure
2 databases
3 secrets
4 monitoring
5 authentication
6 catalog
7 payments
8 entitlement
9 connections
10 market data
11 strategy
12 risk
13 signals
14 execution
15 reconciliation
16 notifications
17 admin
18 public release

Only activate live execution after all control-plane safety checks pass.

# 7.181 LIVE TRADING ACTIVATION

Must require explicit environment configuration.

Example:
LIVE_EXECUTION_ENABLED = true

But also:
policy permits
provider permits
strategy approved
risk engine healthy
reconciliation healthy
monitoring healthy

A single boolean must never be the only safety gate.

# 7.182 MULTI-GATE EXECUTION

Execution allowed only if:
ENTITLEMENT = ACTIVE
ELIGIBILITY = TRUE
CONNECTION = HEALTHY
MARKET_DATA = FRESH
STRATEGY = ACTIVE
RISK_ENGINE = HEALTHY
RISK_DECISION = ALLOW
POLICY = ALLOW
INCIDENT_LOCK = FALSE
PROVIDER = HEALTHY

Otherwise:
NO_EXECUTION.

# 7.183 FINAL EXECUTION CHECK

Immediately before provider submission re-check:
connection
provider capability
risk
quantity
price
market state
idempotency
account state

# 7.184 POST-EXECUTION

After provider submission:
record provider response
transition order
start reconciliation/watch
notify customer
write audit

Do not assume a successful HTTP response equals a filled order.

# 7.185 FINAL CUSTOMER OUTCOME

Customer sees:
Signal → Decision → Execution → Fill → Position

Each is a distinct state.

# 7.186 MASTER NON-BYPASS RULE

No user interface, mobile client, API client, support user, administrator, AI agent, feature flag, or remote configuration may bypass:
authorization
entitlement
compliance
risk
execution-state integrity

unless a separate formally approved break-glass policy explicitly allows it.

# 7.187 MASTER QUALITY RULE

The system must reliably answer:

WHY WAS THIS SIGNAL CREATED?
WHY WAS THIS TRADE APPROVED?
WHY WAS THIS TRADE REJECTED?
WHICH RISK POLICY APPLIED?
WHICH STRATEGY VERSION APPLIED?
WHICH AI/model configuration participated?
WHAT MARKET DATA WAS USED?
WHICH PROVIDER ACCEPTED/REJECTED IT?
WHAT EXACTLY WAS FILLED?
WHAT IS THE CURRENT RECONCILED STATE?
WHO/WHAT CAUSED THE STATE CHANGE?

If the platform cannot answer these questions reliably, it is not production-ready.

# 7.188 MASTER SAFETY RULE

When data, provider status, risk calculation, entitlement, compliance state, or order state is uncertain:

ABSTAIN.

Do not guess.

# 7.189 MASTER BUSINESS RULE

The customer should feel that the platform is:
precise
transparent
disciplined
premium
advanced
safe-by-design

without being led to believe that:
AI predicts the future
profits are guaranteed
markets are risk-free
approval is guaranteed
execution is infallible

# 7.190 END-TO-END ACCEPTANCE TEST

SCENARIO:
A customer purchases a 3-month crypto automation subscription.

EXPECTED:
1. payment provider confirms payment
2. entitlement becomes active
3. customer chooses Futures/Perpetuals
4. customer selects supported exchange
5. customer submits API Key + Secret
6. system stores sensitive credential only through secure secret boundary
7. validation confirms read/trade permissions
8. withdrawal permission is absent/rejected
9. market data is fresh
10. strategy candidate is generated
11. research evidence is available according to strategy requirements
12. AI ensemble produces structured analysis
13. deterministic risk engine calculates permitted size
14. policy engine confirms eligibility
15. final signal is created
16. order intent is generated
17. pre-submit checks run again
18. provider receives exactly one idempotent order request
19. response is tracked
20. fill is reconciled
21. position is updated
22. customer receives notification
23. journal records the complete chain
24. admin sees operational metadata but not customer secret
25. audit record ties all events through correlation ID

FAILURE VARIANT:
At step 19 provider times out.

EXPECTED:
order state = UNKNOWN
duplicate submission = BLOCKED
reconciliation begins
customer sees “Pending reconciliation”
system resolves provider status
journal is updated
only then may further execution occur.

# 7.191 END-TO-END ACCEPTANCE TEST — RISK FAILURE

Scenario:
signal is valid,
customer is entitled,
provider is healthy,
but recommended quantity exceeds configured risk.

Expected:
risk decision = REJECT or REDUCE
no unapproved order
customer sees reason
audit records risk policy
signal may remain informational
no fabricated execution event

# 7.192 END-TO-END ACCEPTANCE TEST — DATA FAILURE

Scenario:
market data is stale.

Expected:
signal suppressed or marked stale
automation blocked
customer sees data age
admin sees provider/data health
no order submission

# 7.193 END-TO-END ACCEPTANCE TEST — AI FAILURE

Scenario:
AI provider unavailable.

Expected:
fallback according to strategy policy
or NO_SIGNAL.

Never invent AI output.

# 7.194 END-TO-END ACCEPTANCE TEST — PAYMENT REVERSAL

Scenario:
subscription was active, provider later confirms chargeback/refund.

Expected:
payment state updated
entitlement reevaluated
automation stops for new orders
customer notified
audit preserved
no deletion of financial history

# 7.195 END-TO-END ACCEPTANCE TEST — STORE RESTRICTION

Scenario:
user is on a platform where a requested capability is prohibited.

Expected:
server returns UNAVAILABLE_ON_PLATFORM
frontend hides/blocks activation
alternative eligible experience may be shown
no bypass
no hidden webview
no disguised label

# 7.196 PART 7 FINAL DIRECTIVE

Implement this part as the authoritative end-to-end trading-core contract.

When implementation details are missing:
do not invent unsafe behavior.

Select a conservative default:
NO_EXECUTION
NO_TRADE
REQUIRES_REVIEW
or
UNAVAILABLE

Then document the missing decision.

The final system must be explainable, auditable, secure, policy-aware, reversible, and resilient.

END OF PART 7
