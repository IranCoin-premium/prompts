# TRADING PLATFORM MASTER PROMPT
# PART 11 — PRICING, SUBSCRIPTIONS, BILLING, CHECKOUT, STORE PAYMENTS, TAX, REFUNDS, CHARGEBACKS, ENTITLEMENTS & REVENUE OPERATIONS

CONTINUATION OF PARTS 1–10

PURPOSE

This part defines the commercial and billing architecture of the platform.

The system must support:
- weekly subscriptions
- monthly subscriptions
- quarterly subscriptions
- six-month subscriptions
- annual subscriptions
- identical product capabilities across terms when configured
- different market modes
- signal-only services
- automated trading services
- web checkout
- platform-native subscription flows where required/appropriate
- payment provider webhooks
- entitlement activation
- renewal
- expiration
- cancellation
- refund
- chargeback
- reconciliation
- invoices/receipts where required
- localized pricing
- taxes where applicable
- compliance-aware product availability

IMPORTANT:

Payment success is not the same thing as entitlement activation.

Frontend checkout state is not authoritative.

A customer may never receive premium service solely because:
- a button displayed success
- a payment redirect returned success
- local app storage contains a success flag
- a client request claims payment completed

Authoritative state must originate from verified provider events and server-side business logic.

---

# 11.0 COMMERCIAL NORTH STAR

The billing system must make it easy for a legitimate customer to understand:

WHAT THEY ARE BUYING
HOW LONG IT LASTS
WHAT IT COSTS
WHETHER IT RENEWS
WHEN IT RENews
WHAT MARKET MODE IT COVERS
WHAT CAPABILITIES IT INCLUDES
WHAT IS NOT INCLUDED
WHICH PLATFORM/REGION RESTRICTIONS APPLY
HOW TO CANCEL
WHAT THE REFUND POLICY IS
WHAT HAPPENS WHEN PAYMENT FAILS
WHAT HAPPENS WHEN THE SUBSCRIPTION EXPIRES

The customer must never need to decode billing language.

---

# 11.1 PRODUCT CATALOG SEPARATION

Separate:

PRODUCT
PLAN
PRICE
MARKET MODE
TERM
ENTITLEMENT

Example:

Product:
AUTOMATION

Market:
CRYPTO

Mode:
FUTURES

Term:
3 MONTHS

Price:
configured

Entitlement:
automation.crypto.futures

Do not combine all dimensions into an opaque product ID that cannot be reasoned about.

---

# 11.2 SERVICE TYPES

Primary service categories:

SIGNALS
AUTOMATION

Optional future:
ANALYTICS
EDUCATION
DATA
PROFESSIONAL

Every service must define its actual delivered capability.

---

# 11.3 MARKET PRODUCT CATALOG

CRYPTO:
- SPOT
- FUTURES / PERPETUALS
- HYBRID

FOREX:
- BROKER / MT5
- HYBRID where supported

BINARY-OPTIONS-LIKE:
- OTC
- INTERNATIONAL
- HYBRID

The binary-options-like category is independently restricted by platform, jurisdiction and applicable law.

Never create a plan merely to disguise an unavailable feature.

---

# 11.4 SUBSCRIPTION TERMS

Supported:

7 DAYS
1 MONTH
3 MONTHS
6 MONTHS
12 MONTHS

The actual expiration date must be calculated by the billing service.

Do not approximate one month as 30 days unless the business explicitly chooses that rule and documents it.

---

# 11.5 TERM CALCULATION

Use calendar-aware rules.

Examples:

1 month from January 31 must use an explicit date policy.

3 months from November 30 must follow the platform's approved calendar arithmetic.

12 months must represent one calendar year if that is the commercial policy.

Do not use naive second counts for calendar subscriptions.

---

# 11.6 PLAN CAPABILITY PARITY

If all plans have identical capabilities and differ only by duration, the pricing page must state:

“All plans include the same platform capabilities. The difference is subscription duration.”

Do not imply the annual plan has higher technology unless it actually does.

---

# 11.7 PRICING MODEL

Each price has:

price_id
product_id
market_mode_id
term
amount
currency
tax_behavior
billing_provider
effective_from
effective_to
status

Prices are versioned.

Never silently change an already-confirmed purchase price.

---

# 11.8 PRICE VERSIONING

When pricing changes:
create new price version.

Existing subscriptions continue according to their contractual rules.

Do not mutate historical price records.

---

# 11.9 CURRENCY

Support configured currencies.

At minimum consider:
USD
EUR
GBP

Additional currencies depend on business requirements.

Every amount must carry:
amount
currency
precision

---

# 11.10 FX CONVERSION

Do not silently convert prices at checkout unless the pricing policy explicitly allows it.

If converting:
show:
source currency
converted currency
rate source
rate timestamp
fees where relevant

---

# 11.11 TAX

Tax handling must support:
tax included
tax excluded
tax calculated at checkout
tax not applicable where lawful

Actual tax rules depend on customer location, product classification and jurisdiction.

Do not invent a universal tax percentage.

---

# 11.12 TAX PROVIDER

Where required, use a recognized tax service or a properly maintained internal tax engine.

Store:
jurisdiction
tax rate/source
calculation ID
timestamp

---

# 11.13 TAX DISPLAY

Checkout should clearly show:

Subtotal
Tax
Total

If tax is included:
state that clearly.

---

# 11.14 CHECKOUT CONTRACT

Checkout creation request should include only:
plan identifier
market mode
term
payment context

Server derives:
price
currency
product
entitlement
eligibility
tax rules

Never trust client-provided amount.

---

# 11.15 CHECKOUT CREATION

POST /api/v1/subscriptions/checkout

Server:
1 authenticate
2 authorize
3 load catalog
4 validate plan
5 evaluate eligibility
6 calculate price
7 calculate tax if required
8 create checkout session
9 return provider-safe checkout reference

---

# 11.16 CHECKOUT SESSION

checkout_sessions
- id
- user_id
- plan_id
- price_id
- amount
- currency
- tax_amount
- total_amount
- provider
- provider_session_id
- status
- expires_at
- created_at

---

# 11.17 CHECKOUT SESSION STATES

CREATED
OPEN
PAYMENT_PENDING
PAYMENT_CONFIRMED
EXPIRED
CANCELLED
FAILED

---

# 11.18 WEB CHECKOUT

The web checkout may use:
approved payment provider
redirect/hosted checkout
embedded checkout where secure and supported

Never handle sensitive card data directly unless the entire architecture is designed and certified accordingly.

Prefer tokenized/provider-hosted payment collection.

---

# 11.19 MOBILE BILLING

For native mobile apps, billing architecture must be determined separately for each store and current platform rules.

Do not assume:
the same web payment flow
can be copied into native mobile.

Build a platform billing adapter.

---

# 11.20 BILLING PROVIDER ADAPTER

Interface:

createCheckout()
createCustomer()
createSubscription()
cancelSubscription()
changeSubscription()
refundPayment()
getPayment()
getSubscription()
verifyWebhook()
reconcile()

Provider-specific APIs remain behind the adapter.

---

# 11.21 STORE BILLING ADAPTER

For platforms with native digital-purchase requirements, use an approved platform billing mechanism where required.

Adapter examples conceptually:

GooglePlayBillingAdapter
AppleStoreKitAdapter

The exact implementation must follow current official platform documentation.

---

# 11.22 STORE PRODUCT IDENTIFIERS

Maintain:
web plan ID
Android product ID
iOS product ID

Example:

automation.crypto.futures.7d
automation.crypto.futures.1m
automation.crypto.futures.3m
automation.crypto.futures.6m
automation.crypto.futures.12m

Do not make product IDs depend on translated names.

---

# 11.23 STORE PRODUCT MAPPING

store_products
- id
- platform
- store_product_id
- internal_plan_id
- billing_type
- status
- reviewed_at

---

# 11.24 WEB VS MOBILE ENTITLEMENT

Regardless of purchase channel:
the internal entitlement service should normalize access.

Example:

WEB purchase
→ entitlement

GOOGLE PLAY purchase
→ entitlement

APPLE purchase
→ entitlement

The entitlement layer remains platform-neutral.

---

# 11.25 PURCHASE CHANNEL

Record:

WEB
ANDROID
IOS
OTHER_APPROVED_CHANNEL

This is useful for support, reconciliation and compliance.

---

# 11.26 RECEIPT / PURCHASE VERIFICATION

For platform-native purchases:
server-side verification is required where the platform supports/mandates it.

Never trust:
client “purchase successful” alone.

---

# 11.27 STORE RECEIPT DATA

Store only the minimum verification reference/data necessary.

Do not permanently store unnecessary raw purchase payloads.

---

# 11.28 SUBSCRIPTION ACTIVATION

Activation pipeline:

payment verified
→ subscription record
→ entitlement evaluation
→ entitlement active
→ notification
→ customer dashboard refresh

Every stage is auditable.

---

# 11.29 ENTITLEMENT LATENCY

The UI should support:

PAYMENT CONFIRMED
ACTIVATION PENDING

without incorrectly showing:
ACTIVE

until backend activation is complete.

---

# 11.30 DUPLICATE PURCHASE

If the customer buys the same plan twice:
system behavior must be explicit.

Possible policy:
extend existing subscription
create separate subscription
reject duplicate
offer upgrade/change

Do not silently create ambiguous entitlements.

---

# 11.31 REACTIVATION

If an expired customer renews:
create the new authorized period.

Do not overwrite historical subscription state.

---

# 11.32 RENEWAL

Renewal flow:

renewal_due
→ provider attempts payment
→ payment confirmed
→ subscription extended
→ entitlement extended

Failure:
payment_failed
→ grace/retry policy
→ entitlement remains active or enters grace only according to policy

---

# 11.33 GRACE PERIOD

If implemented, define:
duration
eligible features
notification
retry frequency

Do not create indefinite unpaid access.

---

# 11.34 PAYMENT RETRY

Use provider-approved retry policies.

Do not hammer payment provider.

---

# 11.35 FAILED PAYMENT UX

Show:
payment failed
reason category
next action
next retry where known
subscription status

Avoid exposing provider internal error strings.

---

# 11.36 CARD FAILURE

Example:

“Your payment could not be completed. Your subscription has not been renewed yet.”

---

# 11.37 SUBSCRIPTION STATUS

PENDING
ACTIVE
PAST_DUE
GRACE
CANCELLED
EXPIRED
REFUNDED
CHARGEBACK
SUSPENDED

---

# 11.38 CANCELLATION

Cancellation policy:
- immediate
- end-of-term
- provider-controlled

The UI must state which applies.

---

# 11.39 CANCEL UX

Show:
current end date
what stops
what remains available
renewal behavior

If cancelling only stops renewal:
say so.

---

# 11.40 EXPIRED SUBSCRIPTION

When expired:
new entitlement = inactive

For automated trading:
block new automated orders according to safety policy.

Never imply existing provider positions automatically disappear.

---

# 11.41 REFUND POLICY

Refunds must follow:
commercial policy
provider rules
app-store rules
consumer law

Do not promise a universal refund outcome.

---

# 11.42 REFUND REQUEST

POST /api/v1/refunds

Requires:
authentication
authorization
subscription/payment reference
reason

System evaluates:
eligibility
timing
provider rules

---

# 11.43 REFUND PROCESS

REQUESTED
→ UNDER_REVIEW
→ APPROVED
→ PROVIDER_SUBMITTED
→ REFUNDED
or
→ REJECTED

---

# 11.44 REFUND IDEMPOTENCY

A refund request must be idempotent.

Do not issue duplicate refunds because a button was pressed twice.

---

# 11.45 CHARGEBACK

Chargeback may trigger:
payment state change
entitlement review
fraud review
customer notification

Do not automatically accuse the customer.

---

# 11.46 CHARGEBACK STATE

CHARGEBACK_OPEN
CHARGEBACK_WON
CHARGEBACK_LOST

Business impact rules must be documented.

---

# 11.47 PAYMENT WEBHOOK

Webhook sequence:

receive
→ verify signature
→ verify timestamp
→ deduplicate
→ validate
→ write event
→ update payment
→ emit domain event
→ reconcile

---

# 11.48 WEBHOOK REPLAY

Duplicate provider event:
must not duplicate:
payment
subscription
entitlement
notification

---

# 11.49 PAYMENT RECONCILIATION

Compare:
provider
checkout sessions
payments
subscriptions
entitlements

Find:
paid_without_entitlement
entitlement_without_payment
duplicate_payment
amount_mismatch
currency_mismatch
refund_mismatch

---

# 11.50 DAILY RECONCILIATION

Run scheduled reconciliation.

The actual frequency may be higher for critical providers.

Every mismatch gets:
case ID
severity
owner
state

---

# 11.51 FINANCIAL LEDGER

Where financially appropriate, maintain an internal transaction ledger.

Possible entries:

charge
refund
chargeback
adjustment
tax
fee

The ledger should be append-oriented.

---

# 11.52 LEDGER IMMUTABILITY

Never rewrite a past financial event.

Corrections use:
adjustment entries
compensating entries

---

# 11.53 INVOICE / RECEIPT

Where required by provider/business jurisdiction:
provide:
invoice
receipt
transaction reference
tax information

---

# 11.54 INVOICE NUMBERING

Use a controlled numbering mechanism.

Do not generate duplicate invoice IDs under concurrency.

---

# 11.55 PAYMENT REFERENCES

Customer-facing reference:
safe and non-sensitive.

Never expose:
provider secret
raw webhook signature
internal infrastructure ID

---

# 11.56 PAYMENT SUPPORT VIEW

Admin may see:
payment ID
provider
amount
currency
status
subscription
entitlement state

Admin should not see:
full card details
security codes
unnecessary payment instrument details

---

# 11.57 SUBSCRIPTION SUPPORT VIEW

Show:

plan
term
price
currency
channel
status
start
end
renewal
payment
entitlement

---

# 11.58 ENTITLEMENT SUPPORT VIEW

Show:
key
state
source
policy
start
end

Allow correction only through authorized workflows.

---

# 11.59 MANUAL ENTITLEMENT ADJUSTMENT

If business requires:
support/grant/manual adjustment

Create:
reason
actor
scope
duration
approval
audit

Avoid indefinite manual access.

---

# 11.60 FREE TRIALS

If implemented:
trial duration
eligibility
payment requirement
conversion
cancellation

must be explicit.

---

# 11.61 COUPONS

Coupon fields:
code
status
discount_type
discount_value
currency rules
eligibility
start
end
max_redemptions
per_user_limit

---

# 11.62 COUPON RACE CONDITIONS

Two simultaneous redemptions must not exceed limits.

Use atomic transaction/locking strategy.

---

# 11.63 DISCOUNTS

Supported:
percentage
fixed amount
term-specific

Never let frontend choose arbitrary discount amount.

---

# 11.64 PRICING EXPERIMENTS

A/B tests may change:
presentation
copy
layout

Sensitive financial/legal terms should not be experimentally obscured.

---

# 11.65 PRICE LOCK

Once checkout session is created:
define whether price is locked.

If locked:
store price snapshot.

---

# 11.66 CHECKOUT EXPIRATION

Expired checkout cannot be paid using stale pricing unless provider/session semantics explicitly support it.

---

# 11.67 CHECKOUT RESUME

User can resume:
valid session
revalidate price
revalidate eligibility

---

# 11.68 PAYMENT CURRENCY MISMATCH

If provider returns a currency different from expected:
FLAG
do not silently accept

---

# 11.69 PAYMENT AMOUNT MISMATCH

If provider confirms an amount different from authorized expected amount:
FLAG
do not automatically grant entitlement.

---

# 11.70 PAYMENT PROVIDER OUTAGE

If provider unavailable:
show
PAYMENT TEMPORARILY UNAVAILABLE

Do not create false success.

---

# 11.71 PAYMENT TIMEOUT

If checkout times out:
status remains pending/unknown until authoritative provider result is known.

---

# 11.72 ENTITLEMENT ROLLBACK

If payment is reversed:
entitlement is reevaluated.

Do not retroactively delete historical access records.

---

# 11.73 MULTI-CHANNEL ENTITLEMENT

A user may have:
web subscription
Android purchase
iOS purchase

The entitlement engine must prevent accidental double-access while preserving legitimate independent subscriptions according to business policy.

---

# 11.74 ACCOUNT MERGE

Do not automatically merge accounts based only on matching email without secure verification.

---

# 11.75 PURCHASE RESTORE

For supported stores:
restore purchases
→ verify with store
→ map to internal user
→ recalculate entitlement

---

# 11.76 PLATFORM PURCHASE RESTORE

Customer must be able to recover a valid purchase when store policy supports it.

Do not require buying again solely because app was reinstalled.

---

# 11.77 FAMILY SHARING / SHARED PURCHASES

If a platform offers sharing behavior:
document whether supported.

Do not assume store purchase semantics.

---

# 11.78 WEB ACCOUNT LINKING

If store purchase is associated with a user:
link through secure authenticated identity.

Never link purchase to arbitrary user ID supplied by client.

---

# 11.79 SUPPORT FOR REGION PRICING

Prices may differ by:
currency
market
tax
business policy

But any difference must be intentional and configurable.

---

# 11.80 REGIONAL CATALOG

Region rules can affect:
availability
currency
payment provider
tax
product
market mode

Never rely solely on IP address.

Use a combination of:
account country
jurisdiction data
provider rules
platform

---

# 11.81 VPN / GEO MANIPULATION

Do not treat IP-only location as definitive compliance evidence.

If region is uncertain:
REQUIRES_REVIEW
or
RESTRICT

Do not assist users in bypassing geographic restrictions.

---

# 11.82 STORE REGION

Native store availability may differ from web availability.

Maintain platform-region matrix.

---

# 11.83 PAYMENT METHOD MATRIX

For each region:
supported methods
currency
provider
refund policy
billing limitations

---

# 11.84 PAYMENT METHOD STORAGE

Prefer provider tokenization.

Do not store raw card data unless the entire compliance architecture is explicitly designed for it.

---

# 11.85 PCI CONSIDERATION

If card payment is used, minimize card-data scope through hosted/tokenized provider flows where possible.

Legal/compliance review determines exact obligations.

---

# 11.86 SUBSCRIPTION CHANGE

Plan changes may be:
upgrade
downgrade
mode change
product change

Behavior must be explicit.

---

# 11.87 MODE CHANGE

Example:

Crypto Spot
→ Crypto Futures

This is not necessarily the same as a cosmetic plan change.

It may require:
new entitlement
new eligibility
new risk acknowledgment
new provider capability

---

# 11.88 PRODUCT CHANGE

Signals
→ Automation

May require:
new agreement
new subscription
additional risk disclosure
connection setup

---

# 11.89 PRORATION

If proration is supported:
calculate server-side.
Provider rules may differ.

Show customer:
credit
remaining period
new charge

---

# 11.90 SUBSCRIPTION PAUSE

Only implement if commercially and technically required.

Define:
duration
feature behavior
renewal
entitlement

---

# 11.91 BILLING NOTIFICATIONS

Events:
payment succeeded
payment failed
renewal upcoming
renewal failed
subscription cancelled
subscription expiring
refund completed
chargeback state changed

---

# 11.92 PAYMENT NOTIFICATION SECURITY

Never send full payment information in email/push.

Use safe references.

---

# 11.93 WEBHOOK MONITORING

Track:
received
verified
rejected
duplicate
processed
failed
retrying

---

# 11.94 BILLING DASHBOARD

Admin metrics:
revenue
active subscriptions
new subscriptions
renewals
cancellations
refunds
chargebacks
failed payments
entitlement lag
reconciliation cases

Do not use vanity metrics without definitions.

---

# 11.95 REVENUE RECOGNITION

Accounting recognition is distinct from payment receipt.

Use finance/accounting guidance and applicable standards.

Do not infer accounting treatment solely from subscription activation.

---

# 11.96 CUSTOMER BILLING DASHBOARD

Customer sees:
current plan
market mode
term
price
renewal
payment method
billing history
receipts
cancel
change where supported

---

# 11.97 BILLING HISTORY

Fields:
date
type
amount
currency
status
reference

No raw card data.

---

# 11.98 RECEIPT DOWNLOAD

Provide authenticated download.

Use temporary URLs.

---

# 11.99 REFUND STATUS

Customer can see:
requested
under review
approved
processing
completed
rejected

---

# 11.100 CHARGEBACK STATUS

If disclosed:
use neutral language.

---

# 11.101 ENTITLEMENT DISPLAY

Customer UI should distinguish:

PAYMENT CONFIRMED
SUBSCRIPTION ACTIVE
SERVICE READY

These are not identical.

---

# 11.102 SERVICE READY CONDITION

For automation:

subscription
+
entitlement
+
eligibility
+
connection
+
risk readiness

must be satisfied.

---

# 11.103 PAYMENT TO AUTOMATION TRANSITION

Payment confirmation alone must never activate trading automation.

---

# 11.104 PAYMENT TO SIGNAL TRANSITION

Signal service may activate once entitlement is confirmed, even when exchange connection is not required.

---

# 11.105 SUBSCRIPTION EXPIRATION & AUTOMATION

On expiration:
block new automated orders according to policy.

Do not automatically liquidate positions unless a separate documented workflow explicitly governs such action.

---

# 11.106 REFUND & AUTOMATION

Upon confirmed refund:
evaluate entitlement immediately.

If entitlement is revoked:
automation should not create new orders.

---

# 11.107 CHARGEBACK & AUTOMATION

Confirmed chargeback:
entitlement review
possible suspension
new-order block

---

# 11.108 PAYMENT FRAUD

Signals:
multiple failed payments
velocity
unusual account patterns
coupon abuse
chargeback history

Responses:
step-up
manual review
temporary hold

---

# 11.109 FRAUD FALSE POSITIVES

Do not automatically accuse customers.

Use:
“Payment verification required.”

---

# 11.110 RECONCILIATION CASE MANAGEMENT

Case fields:

case_id
provider
entity
mismatch
severity
opened_at
owner
status
resolution
resolved_at

---

# 11.111 BILLING INCIDENTS

Potential incidents:
double charge
missing entitlement
duplicate refund
incorrect price
wrong currency
webhook outage
store verification failure

---

# 11.112 BILLING INCIDENT RESPONSE

1 freeze unsafe entitlement changes
2 identify scope
3 reconcile provider
4 correct with evidence
5 notify affected users where appropriate
6 document

---

# 11.113 MANUAL REFUND SECURITY

Manual refund requires:
permission
reason
target
amount
currency
confirmation
audit

---

# 11.114 MANUAL ENTITLEMENT SECURITY

Manual entitlement:
- limited duration
- reason
- approval
- audit

---

# 11.115 ADMIN BILLING RBAC

FINANCE_ADMIN:
payments/refunds

SUPPORT_ADMIN:
view billing
request approved actions

SUPER_ADMIN:
broader but audited

CONTENT_ADMIN:
no payment access

---

# 11.116 BILLING API

GET /api/v1/billing
GET /api/v1/billing/history
GET /api/v1/subscriptions
POST /api/v1/subscriptions/checkout
POST /api/v1/subscriptions/{id}/cancel
POST /api/v1/refunds

---

# 11.117 ADMIN BILLING API

GET /admin/v1/payments
GET /admin/v1/subscriptions
GET /admin/v1/refunds
POST /admin/v1/refunds/{id}/approve
POST /admin/v1/entitlements/{id}/adjust

All privileged actions audited.

---

# 11.118 BILLING API AUTHORIZATION

Customer:
own records

Admin:
role-scoped data

Never expose unrestricted billing search.

---

# 11.119 PRICE TAMPERING TEST

Client submits:
price = 0

Expected:
server ignores client amount
loads authoritative plan price.

---

# 11.120 ENTITLEMENT TAMPERING TEST

Client submits:
entitlementActive = true

Expected:
ignored.

---

# 11.121 SUBSCRIPTION EXPIRY TEST

Client submits:
endAt = future date

Expected:
ignored.

---

# 11.122 REFUND DUPLICATION TEST

Two identical refund requests:
one financial action.

---

# 11.123 WEBHOOK DUPLICATION TEST

Same event twice:
one financial state transition.

---

# 11.124 PAYMENT RACE TEST

Payment webhook and dashboard request occur simultaneously.

Expected:
no unauthorized access
no contradictory state.

---

# 11.125 STORE PURCHASE RACE TEST

Store purchase event and restore request arrive together.

Expected:
idempotent entitlement result.

---

# 11.126 BILLING DATA MODEL

Recommended minimum:

customers
plans
prices
store_products
checkout_sessions
subscriptions
subscription_events
payments
payment_events
refunds
entitlements
entitlement_events
billing_adjustments
reconciliation_cases
invoices
tax_records

---

# 11.127 SUBSCRIPTION EVENTS

Examples:

SubscriptionCreated
SubscriptionActivated
SubscriptionRenewalDue
SubscriptionRenewed
SubscriptionPastDue
SubscriptionCancelled
SubscriptionExpired
SubscriptionRefunded
SubscriptionChargedBack

---

# 11.128 PAYMENT EVENTS

Examples:

PaymentCreated
PaymentAuthorized
PaymentCaptured
PaymentFailed
PaymentRefunded
PaymentChargeback

---

# 11.129 ENTITLEMENT EVENTS

Examples:

EntitlementGranted
EntitlementExtended
EntitlementSuspended
EntitlementRevoked
EntitlementExpired

---

# 11.130 BILLING EVENT VERSIONING

Every event:
schemaVersion
eventId
occurredAt
correlationId

---

# 11.131 BILLING AUDIT

Audit:
price creation
price change
refund
coupon
manual entitlement
plan change
billing configuration change

---

# 11.132 BILLING REPORTING

Reports:
MRR
ARR
active subscriptions
new subscriptions
churn
renewal
refund rate
chargeback rate
average revenue per user

Each metric must define its calculation methodology.

---

# 11.133 MRR

Monthly recurring revenue calculation must define:
annual normalization
discount treatment
refund treatment
one-time fees

---

# 11.134 ARR

Annual recurring revenue should be derived according to an explicit finance definition.

---

# 11.135 CHURN

Define:
customer churn
subscription churn
revenue churn

Do not mix them.

---

# 11.136 REFUND RATE

Define:
count-based
revenue-based

---

# 11.137 NET REVENUE

Separate:
gross payment
refunds
chargebacks
fees
taxes
net revenue

Do not present gross collections as profit.

---

# 11.138 FINANCE EXPORT

Export:
payments
refunds
tax
fees
subscriptions
adjustments

Use secure export.

---

# 11.139 TAX RECORD SECURITY

Tax records may contain sensitive customer/location information.

Restrict access.

---

# 11.140 CUSTOMER INVOICE LOCALIZATION

Invoice language follows:
customer/account locale where supported.

Financial/legal content must retain exact values.

---

# 11.141 PERSIAN BILLING UX

Display:
قیمت
مدت اشتراک
تاریخ شروع
تاریخ پایان
تمدید
مالیات
مبلغ نهایی

Use Persian naturally.

---

# 11.142 ENGLISH BILLING UX

Display:
Price
Duration
Start Date
End Date
Renewal
Tax
Total

---

# 11.143 RTL BILLING NUMBERS

Financial values may use Latin digits for technical clarity even in RTL layouts, if that is the selected product policy.

Document the rule and apply consistently.

---

# 11.144 BILLING COPY

Avoid:
“Only today!”
“Guaranteed!”
“Risk-free!”

Use:
“3-month subscription”
“Renews on…”
“Cancel before…”

---

# 11.145 PRICING PAGE TRUST

Always make renewal:
visible.

Never hide auto-renewal in small text.

---

# 11.146 CHECKOUT CONSENT

Where required:
accept terms
risk disclosure
subscription terms

Store version and timestamp.

---

# 11.147 TERMS VERSIONING

At purchase:
terms_version
privacy_version
subscription_terms_version
risk_disclosure_version where applicable

---

# 11.148 CONSENT RECORD

consent_id
user_id
document
version
locale
timestamp
source

---

# 11.149 STORE POLICY ALIGNMENT

Native subscription implementation must be compatible with the current official rules for the target store.

Do not assume web checkout can simply be embedded into a native application when store rules require platform billing for digital services.

Verify current:
Google Play Billing
Apple StoreKit
regional payment restrictions
financial-app policies

before launch.

---

# 11.150 WEB PAYMENT SEPARATION

The web payment stack should be decoupled from native billing adapters.

This allows:
store-specific compliance
payment-provider replacement
regional methods
centralized entitlement.

---

# 11.151 PAYMENT PROVIDER MIGRATION

Support dual-provider transition where necessary:

OLD PROVIDER
NEW PROVIDER

During migration:
- preserve historical records
- map IDs
- verify webhooks
- reconcile
- avoid duplicate subscriptions

---

# 11.152 PROVIDER FAILOVER

Do not automatically switch providers during a live checkout unless the workflow explicitly supports it.

A failed payment session should not silently become a different transaction.

---

# 11.153 PAYMENT WEBHOOK OUTAGE

If webhook delivery is delayed:
entitlement may remain pending.

Provide reconciliation path.

---

# 11.154 PURCHASE RESTORATION JOB

Periodic:
store receipts/purchases
→ verify
→ reconcile entitlement.

---

# 11.155 ENTITLEMENT DRIFT

Detect:
subscription active but entitlement missing
entitlement active but subscription invalid
expired subscription but active entitlement

Auto-correct only where deterministic.

Otherwise:
manual review.

---

# 11.156 BILLING HEALTH METRICS

Measure:
payment success
payment failure
webhook latency
entitlement activation latency
refund latency
reconciliation drift

---

# 11.157 BILLING SLA

Define internally:
checkout availability
webhook processing latency
entitlement activation latency
refund processing

Do not publicly promise unsupported guarantees.

---

# 11.158 FINAL BILLING ACCEPTANCE CHECKLIST

[ ] Catalog separated from entitlements
[ ] Price versioning implemented
[ ] Subscription term logic defined
[ ] Calendar-aware expiration
[ ] Server-side pricing
[ ] Tax handling defined
[ ] Web checkout secured
[ ] Native store billing adapters planned
[ ] Store product mapping
[ ] Server-side receipt/purchase verification
[ ] Payment webhook signature verification
[ ] Replay protection
[ ] Payment idempotency
[ ] Refund idempotency
[ ] Subscription state machine
[ ] Entitlement state machine
[ ] Chargeback workflow
[ ] Reconciliation
[ ] Financial audit trail
[ ] Billing RBAC
[ ] Customer billing history
[ ] Invoice/receipt capability
[ ] Regional availability
[ ] RTL/LTR billing
[ ] Renewal transparency
[ ] No misleading pricing
[ ] No client-controlled price
[ ] No client-controlled entitlement
[ ] Store policy review completed
[ ] Legal review completed where applicable

---

# 11.159 MASTER BILLING PRINCIPLE

PAYMENT CONFIRMED
does not automatically mean:
SERVICE READY.

The final service state requires:

PAYMENT
+
ENTITLEMENT
+
ELIGIBILITY
+
REQUIRED CONNECTION
+
SYSTEM READINESS

---

# 11.160 MASTER COMMERCIAL PRINCIPLE

The best billing UX is not the one that extracts the most money.

It is the one that makes:
price
duration
renewal
scope
risk
cancellation
refund
availability

easy to understand.

---

# 11.161 MASTER RECONCILIATION PRINCIPLE

When provider state and internal state disagree:

DO NOT GUESS.

Reconcile.

---

# 11.162 MASTER PAYMENT SAFETY PRINCIPLE

Never trust:
frontend payment status
frontend amount
frontend currency
frontend entitlement
frontend renewal date

Server-side provider verification wins.

---

# 11.163 MASTER ENTITLEMENT PRINCIPLE

Entitlement is a computed authorization state, not a payment receipt.

---

# 11.164 MASTER STORE PRINCIPLE

Each distribution platform may have different commerce and financial-feature rules.

The architecture must isolate those differences instead of assuming one payment flow works everywhere.

---

# 11.165 PART 11 FINAL DIRECTIVE

Build the commercial system so that a customer can confidently answer:

“What exactly did I purchase?”
“How long does it last?”
“When does it renew?”
“What market mode does it enable?”
“What happens if payment fails?”
“How do I cancel?”
“What happens after refund?”
“Why is a capability unavailable?”
“Is my service actually active?”

And the platform can answer, with authoritative records:

WHO PAID
WHAT THEY PAID FOR
HOW MUCH
WHEN
THROUGH WHICH CHANNEL
UNDER WHICH TERMS
WHICH ENTITLEMENT WAS GRANTED
WHICH POLICY ALLOWED IT
WHEN IT EXPIRES
WHAT HAPPENED AFTERWARD

END OF PART 11
