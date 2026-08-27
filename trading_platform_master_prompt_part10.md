# TRADING PLATFORM MASTER PROMPT
# PART 10 — SECURITY ARCHITECTURE, THREAT MODEL, ZERO-TRUST, APPLICATION SECURITY, API SECURITY, PRIVACY & INCIDENT RESPONSE

CONTINUATION OF PARTS 1–9

PURPOSE

This part defines the security architecture for a financial-technology platform containing:

- customer accounts
- subscriptions and payments
- trading signals
- automated trading
- exchange/broker integrations
- customer API credentials
- administrative controls
- AI services
- public content/blog infrastructure
- web
- PWA
- Android
- iOS
- Windows
- macOS

The platform must be designed as a security-sensitive system.

IMPORTANT PRINCIPLES

1. Security controls are enforced on the server.
2. Client-side controls are UX controls, not authorization controls.
3. No administrator should need to see a customer's raw exchange Secret Key.
4. No AI model may independently override deterministic authorization, risk, compliance, or execution controls.
5. No financial mutation should depend solely on an asynchronous UI assumption.
6. Secrets must be isolated from ordinary application data.
7. Financial and security events must be auditable.
8. Unknown states must fail safely.
9. Security must be measurable and continuously tested.
10. Compliance is a first-class product dependency.
11. Privacy must be considered during architecture, not added after implementation.
12. No security claim should be made without evidence.

---

# 10.0 SECURITY NORTH STAR

The platform must remain secure even when:

- a browser is compromised
- a mobile device is compromised
- a user account is stolen
- a frontend is modified
- an administrator makes a mistake
- a third-party API fails
- a dependency becomes vulnerable
- an AI provider is compromised
- a provider returns malicious or malformed data
- a support account is compromised
- a CI credential leaks
- a cloud credential is exposed
- a webhook is replayed
- a customer submits malicious input
- a malicious actor probes internal APIs
- one microservice is compromised

Security design must assume partial compromise.

---

# 10.1 SECURITY PRINCIPLES

Use:

LEAST PRIVILEGE
ZERO TRUST
DEFENSE IN DEPTH
SECURE DEFAULTS
FAIL CLOSED
COMPARTMENTALIZATION
STRONG AUTHENTICATION
EXPLICIT AUTHORIZATION
DATA MINIMIZATION
AUDITABILITY
REPRODUCIBILITY
RECOVERABILITY
CONTINUOUS VERIFICATION

---

# 10.2 ZERO-TRUST MODEL

Never trust based only on:

- network location
- IP address
- application package
- service name
- admin role
- “internal” request
- cached entitlement

Every sensitive request should be independently authenticated and authorized.

Reference architecture principle:
https://csrc.nist.gov/publications/detail/sp/800-207/final

Before production, verify the current official NIST Zero Trust guidance and applicable national requirements.

---

# 10.3 SECURITY BOUNDARIES

Define explicit trust zones:

ZONE 0 — PUBLIC INTERNET
ZONE 1 — PUBLIC EDGE
ZONE 2 — CUSTOMER APPLICATION
ZONE 3 — PRIVILEGED ADMIN
ZONE 4 — INTERNAL SERVICES
ZONE 5 — EXECUTION
ZONE 6 — SECRET MANAGEMENT
ZONE 7 — DATA
ZONE 8 — AUDIT / SECURITY OPERATIONS

No zone automatically trusts another.

---

# 10.4 CUSTOMER BROWSER THREAT MODEL

Assume:

- malicious extensions
- injected scripts
- stolen cookies
- compromised device
- manipulated JavaScript
- modified API requests
- replayed requests
- copied tokens

Therefore:

The browser is an untrusted client.

---

# 10.5 MOBILE CLIENT THREAT MODEL

Assume:

- reverse engineering
- root/jailbreak
- API interception
- modified binaries
- dynamic instrumentation
- local storage compromise
- notification leakage

Therefore:

Never place authoritative secrets or risk logic inside the client.

---

# 10.6 ADMIN CLIENT THREAT MODEL

Assume an administrator's phone may be stolen or compromised.

Mitigations:

- strong authentication
- device enrollment
- short-lived tokens
- biometric step-up
- remote revocation
- minimal local persistence
- no raw customer secrets
- audit of all privileged actions

---

# 10.7 AI THREAT MODEL

Assume AI may:
- hallucinate
- misinterpret data
- output malformed JSON
- be manipulated by prompt injection
- receive poisoned data
- become unavailable
- return internally inconsistent recommendations

Therefore:
AI is untrusted analytical input.

---

# 10.8 PROVIDER THREAT MODEL

External providers may:
- change APIs
- become unavailable
- return malformed data
- experience compromise
- send duplicate events
- send delayed events
- rate limit
- report ambiguous states

Provider output must be validated.

---

# 10.9 CORE ASSET INVENTORY

Critical assets:

A01 Customer Account
A02 Authentication Credentials
A03 Session Tokens
A04 Passkey Public Credentials
A05 Exchange API Key
A06 Exchange API Secret
A07 Provider Webhook Secret
A08 Payment References
A09 Subscription Entitlements
A10 Risk Policies
A11 Strategy Configurations
A12 Live Orders
A13 Positions
A14 Balances
A15 Audit Records
A16 AI Configuration
A17 Infrastructure Credentials
A18 CI/CD Credentials
A19 Signing Keys
A20 Customer Personal Data
A21 KYC Documents where applicable
A22 Market Data
A23 Proprietary Analytics
A24 Source Code
A25 Mobile/Desktop Signing Material

---

# 10.10 ASSET CLASSIFICATION

PUBLIC
INTERNAL
CONFIDENTIAL
RESTRICTED
CRITICAL

Examples:

Blog article:
PUBLIC

Customer profile:
CONFIDENTIAL

Exchange API Secret:
CRITICAL

Production signing key:
CRITICAL

Audit metadata:
RESTRICTED

---

# 10.11 DATA FLOW INVENTORY

Required data-flow diagrams:

DF01 Signup
DF02 Authentication
DF03 Payment
DF04 Entitlement
DF05 API Credential Submission
DF06 Credential Validation
DF07 Market Data
DF08 AI Analysis
DF09 Risk Evaluation
DF10 Signal Publication
DF11 Order Submission
DF12 Reconciliation
DF13 Admin Operations
DF14 Support
DF15 Audit
DF16 Analytics
DF17 Incident Response

Every flow identifies:
source
destination
protocol
authentication
data classification
retention
logging

---

# 10.12 API CREDENTIAL DATA FLOW

Customer UI
→ TLS
→ API gateway
→ connection service
→ secret-management boundary

Return:
connection state

DO NOT RETURN:
raw secret.

---

# 10.13 SECRET DATA FLOW

Only:
authorized service
→ secret manager
→ short-lived access
→ provider adapter

Never:
secret manager
→ frontend
secret manager
→ analytics
secret manager
→ AI
secret manager
→ support

---

# 10.14 ENCRYPTION

Use encryption:
in transit
at rest

For highly sensitive data:
envelope encryption where appropriate.

Key management must be centralized.

Do not invent cryptography.

Prefer mature platform-managed cryptographic services.

---

# 10.15 KEY MANAGEMENT

Define:

master/key-encryption keys
data-encryption keys
service credentials
application secrets
signing keys

Document:
owner
rotation
usage
access
backup
revocation

---

# 10.16 KEY ROTATION

Rotation strategy for:
- cloud credentials
- JWT/signing keys
- database credentials
- webhook secrets
- application API keys
- encryption keys

Customer provider credentials are rotated through supported connection workflows.

---

# 10.17 KEY REVOCATION

If a key is compromised:
- revoke
- replace
- invalidate dependent sessions/credentials if relevant
- investigate

---

# 10.18 PASSWORD POLICY

If passwords are offered:
use a modern password hashing scheme.

Never store plaintext passwords.

Prefer long passphrases and modern authentication.

Do not impose arbitrary complexity rules that reduce usability without meaningful security gain.

---

# 10.19 MFA

Support:
TOTP
Passkeys/WebAuthn
other platform-approved strong factors

Administrators should require phishing-resistant authentication where practical.

---

# 10.20 PASSKEY SECURITY

Server stores:
credential ID
public key
sign counter / relevant metadata

Never:
private key

Passkey registration/authentication flows must follow current WebAuthn specifications and platform guidance.

---

# 10.21 SESSION SECURITY

Use:
short-lived access
refresh rotation
revocation
secure cookie properties where cookies are used
device/session tracking

On high-risk events:
force reauthentication or session revocation.

---

# 10.22 TOKEN STORAGE

Prefer secure, platform-appropriate storage.

Web:
secure HttpOnly cookie architecture where suitable.

Mobile:
Keychain / Keystore or platform-equivalent secure storage.

Never:
hard-code tokens in source.

---

# 10.23 CSRF

For cookie-authenticated state-changing requests:
CSRF protection.

Use:
SameSite controls
CSRF tokens
origin validation where appropriate

---

# 10.24 XSS

Defend against:
stored
reflected
DOM-based XSS

Use:
output encoding
safe rendering
CSP
trusted sanitization
avoid dangerous HTML injection

Blog/CMS is a high-risk surface.

---

# 10.25 CONTENT SECURITY POLICY

Implement a restrictive CSP.

Minimize:
unsafe-inline
unsafe-eval

Use:
nonces
hashes
trusted sources

Review all third-party scripts.

---

# 10.26 CLICKJACKING

Prevent unauthorized framing where appropriate.

Sensitive admin/customer pages should not be frameable by arbitrary origins.

---

# 10.27 CORS

Use explicit allowlists.

Do not:
allow *

for authenticated sensitive endpoints unless specifically justified.

Validate:
origin
credentials behavior
methods
headers

---

# 10.28 SSRF

Any feature that fetches URLs must:
- validate scheme
- allowlist hosts
- block internal/private IPs
- block cloud metadata endpoints
- control redirects
- limit response size
- timeout
- log safe metadata

This is particularly important for:
AI tools
content previews
URL importers
webhooks
document processors

---

# 10.29 SQL INJECTION

Use:
parameterized queries
ORM query builders
stored procedures only where appropriate

Never concatenate raw user input into SQL.

---

# 10.30 NOSQL INJECTION

Validate structured inputs.

Do not permit user input to become arbitrary query operators.

---

# 10.31 COMMAND INJECTION

Never interpolate customer-controlled strings into shell commands.

Prefer native APIs over shell execution.

---

# 10.32 PATH TRAVERSAL

Normalize and validate paths.

Do not allow:
../
absolute path injection
arbitrary file reads

---

# 10.33 FILE UPLOAD SECURITY

Require:
size limit
content-type validation
magic-byte validation
malware scan
randomized storage name
private storage
authorization

Never execute uploaded files.

---

# 10.34 ZIP BOMB / DECOMPRESSION ATTACK

Limit:
compressed size
expanded size
file count
nesting

Reject abnormal archives.

---

# 10.35 DESERIALIZATION

Avoid unsafe deserialization of untrusted data.

Use strict schemas.

---

# 10.36 REGEX DOS

Avoid catastrophic regular expressions on user-controlled input.

Apply timeout or safe engines where appropriate.

---

# 10.37 RATE LIMITING

Apply limits to:
login
MFA
password reset
passkey operations
credential validation
checkout
order operations
admin APIs
search
support
public APIs

---

# 10.38 BUSINESS-RULE ABUSE

Rate limiting alone is insufficient.

Protect:
coupon abuse
payment retries
account creation
credential testing
signal scraping
order spam
admin action abuse

---

# 10.39 ACCOUNT ENUMERATION

Avoid revealing whether:
- an email exists
- a customer exists
- a secret exists

Use consistent responses where appropriate.

---

# 10.40 PASSWORD RESET SECURITY

Use:
single-use
short-lived
high-entropy tokens

Do not disclose account existence unnecessarily.

---

# 10.41 EMAIL VERIFICATION

Verification token:
short-lived
single-use
scoped

After verification:
mark authoritative server state.

---

# 10.42 ADMIN AUTHENTICATION

Admin access requires stronger policy.

Recommended:
passkey + device trust
or
strong MFA

Sensitive actions:
step-up authentication.

---

# 10.43 ADMIN ROLE SEPARATION

Separate:
Support
Finance
Operations
Risk
Security
Content
Super Admin

Avoid universal permissions.

---

# 10.44 PRIVILEGE ESCALATION

Test:
horizontal
vertical

Examples:
customer A accessing customer B
support accessing security data
operator changing risk policy
content editor changing payments

---

# 10.45 OBJECT-LEVEL AUTHORIZATION

Every object endpoint verifies ownership/scope.

Example:

GET /orders/{id}

must check:
order belongs to authenticated user
or admin role has explicit permission.

---

# 10.46 FUNCTION-LEVEL AUTHORIZATION

Do not rely on hidden frontend buttons.

Backend must check:
permission
resource
action

---

# 10.47 MASS ASSIGNMENT

Never bind arbitrary request fields directly to database models.

Explicitly map allowed fields.

---

# 10.48 SECURITY HEADERS

Evaluate:
HSTS
CSP
X-Content-Type-Options
Referrer-Policy
Permissions-Policy
frame protections

---

# 10.49 HTTP REQUEST SMUGGLING

Use:
consistent proxy parsing
modern servers
tested edge architecture

Do not allow conflicting Content-Length/Transfer-Encoding behavior.

---

# 10.50 API INVENTORY

Maintain inventory of:
public
internal
admin
webhook
mobile
service-to-service

Remove abandoned endpoints.

---

# 10.51 SHADOW APIs

Monitor for undocumented/old endpoints still receiving traffic.

Deprecate or secure them.

---

# 10.52 MOBILE API SECURITY

Do not assume:
application package
certificate
obfuscation

proves identity.

Use backend authentication.

---

# 10.53 APP ATTESTATION

Where available and justified:
use platform attestation as an additional risk signal.

It is not a substitute for server authorization.

---

# 10.54 DEEP LINK SECURITY

Validate:
host
scheme
path
parameters

Prevent:
open redirects
token leakage
malicious scheme invocation

---

# 10.55 DEEP LINK TOKENS

Never place long-lived credentials in URLs.

Use short-lived, single-use authorization references.

---

# 10.56 PUSH SECURITY

Do not include:
secrets
full sensitive financial details
authentication tokens

in notification payload.

---

# 10.57 WEB SECURITY FOR PWA

Protect:
service worker
cache
manifest
push subscription
offline storage

Never cache secrets.

Never cache privileged admin data unless explicitly encrypted and justified.

---

# 10.58 SERVICE WORKER SECURITY

Service worker scope must be deliberate.

Do not allow an unintended script to control broader origins.

Review caching rules.

---

# 10.59 LOCAL STORAGE POLICY

Do not store:
API secrets
refresh tokens where safer alternatives exist
private keys
sensitive financial credentials

Use secure platform storage where available.

---

# 10.60 BROWSER CACHE POLICY

Prevent sensitive pages from being cached by shared caches.

Use appropriate cache-control headers.

---

# 10.61 SUBRESOURCE INTEGRITY

Where useful for external static assets:
SRI

Prefer bundling critical assets under controlled build pipelines.

---

# 10.62 THIRD-PARTY SCRIPT RISK

Every third-party script:
- reviewed
- minimized
- monitored
- assigned owner

Do not allow third-party analytics to access secrets.

---

# 10.63 SUPPLY-CHAIN SECURITY

Requirements:
- dependency lockfiles
- vulnerability scanning
- SBOM
- package provenance
- signed artifacts where feasible
- controlled registries
- protected CI

---

# 10.64 MALICIOUS PACKAGE RESPONSE

If a package compromise occurs:
- freeze releases
- identify versions
- inspect builds
- replace
- rotate potentially exposed credentials
- redeploy from trusted source
- investigate

---

# 10.65 CI SECURITY

CI runners are privileged.

Use:
- ephemeral runners where practical
- least privilege
- secret isolation
- protected branches
- signed artifacts
- restricted deployment credentials

---

# 10.66 BUILD SECRETS

Never print secrets.

Prevent secrets from being embedded into:
frontend bundles
mobile bundles
containers
source maps
logs

---

# 10.67 SOURCE MAP SECURITY

Public source maps may reveal:
- proprietary source
- internal URLs
- comments
- business logic

Control distribution.

---

# 10.68 FRONTEND ENVIRONMENT VARIABLES

Anything bundled client-side is effectively public.

Never place secrets in:
NEXT_PUBLIC_*
VITE_*
REACT_APP_*
or equivalent public environment variables.

---

# 10.69 MOBILE CONFIGURATION

Any key embedded into an application should be assumed recoverable.

Only use public identifiers in client binaries.

---

# 10.70 AI SECURITY ARCHITECTURE

AI services should operate in constrained sandboxes.

AI receives:
minimum necessary data
explicit tools
structured inputs

AI does not receive:
exchange secret
admin credentials
database write authority

---

# 10.71 PROMPT INJECTION

All external text is untrusted.

Examples:
news
articles
social posts
uploaded documents

Use:
content isolation
tool permissions
schema validation
source boundaries

---

# 10.72 DATA EXFILTRATION BY AI

Prevent AI from returning:
customer PII
secrets
authentication data
internal configuration

Use:
data minimization
output filters
tool restrictions
access control

---

# 10.73 AI OUTPUT VALIDATION

Every AI output:
schema validation
range validation
enum validation
source validation
expiration

Invalid:
discard
log safely
fallback/abstain

---

# 10.74 AI TOOL SECURITY

Tools must declare:
name
purpose
parameters
authorization
side effects

Execution tool should require deterministic policy authorization.

---

# 10.75 AI MODEL SUPPLY CHAIN

Track:
provider
model
version
endpoint
configuration
prompt policy
tool policy

Changes require evaluation.

---

# 10.76 MODEL INTEGRITY

Do not automatically switch to arbitrary models when a provider fails.

Fallback models must be approved and evaluated.

---

# 10.77 AI COST ABUSE

Limit:
requests/user
tokens/user
requests/agent
global spend

Protect against looping attacks.

---

# 10.78 PAYMENT SECURITY

Never store raw payment instruments unless the payment architecture specifically requires it and all applicable obligations are met.

Prefer provider tokenization.

Payment status must be authoritative.

---

# 10.79 WEBHOOK SECURITY

Verify:
signature
timestamp
event ID

Then:
deduplicate
validate
process transaction
emit event

---

# 10.80 PAYMENT REPLAY

A previously processed event ID must not activate entitlement twice.

---

# 10.81 AMOUNT TAMPERING

Never trust:
frontend amount
frontend currency
frontend plan status

Recompute server-side from catalog.

---

# 10.82 SUBSCRIPTION TAMPERING

Customer cannot:
extend expiry
change plan
activate entitlement
modify price

by editing client requests.

---

# 10.83 COUPON SECURITY

If coupons are introduced:
- validate server-side
- limit reuse
- scope to products/periods
- log redemption
- protect against race conditions

---

# 10.84 EXCHANGE API SECURITY

Provider credentials:
- encrypted
- minimal scope
- isolated
- audited

---

# 10.85 WITHDRAWAL PERMISSION

Do not request withdrawal permission for automated trading.

If detected:
flag
warn
or reject according to policy.

---

# 10.86 IP ALLOWLISTING

Where providers support it:
recommend customer-side IP allowlisting.

Provide guidance without promising absolute protection.

---

# 10.87 PROVIDER API RATE LIMITS

Adapters must honor provider limits.

Never bypass limits through distributed bursts.

---

# 10.88 PROVIDER AUTHENTICATION ERRORS

Do not log raw secrets or provider auth payloads.

Return:
safe internal code
safe customer message

---

# 10.89 PROVIDER WEBHOOKS

Provider event authenticity must be verified.

Never trust payload alone.

---

# 10.90 PROVIDER DATA POISONING

External provider data must be treated as untrusted.

Validate:
numbers
timestamps
states
symbol mappings

---

# 10.91 MARKET DATA MANIPULATION

Do not use one external data point as unquestioned truth where the strategy requires cross-source validation.

Use:
quality score
provider health
anomaly detection

---

# 10.92 SECURITY OF STRATEGY CONFIGURATION

Strategies are privileged code/configuration.

Protect from:
unauthorized edits
version rollback
tampering

---

# 10.93 STRATEGY SIGNING / INTEGRITY

Where feasible:
store immutable artifacts
hash configuration
record version

Production engine verifies approved version.

---

# 10.94 RISK POLICY INTEGRITY

Risk policies are critical.

Every active policy:
signed/verified or integrity-protected
versioned
audited

---

# 10.95 EXECUTION POLICY INTEGRITY

Execution rules must be protected against:
runtime mutation
unreviewed remote changes
frontend manipulation

---

# 10.96 REMOTE CONFIG SECURITY

No arbitrary remote JSON may directly alter:
risk
execution
entitlement
authorization

High-impact configuration must be strongly authenticated, validated, versioned and audited.

---

# 10.97 KILL SWITCH SECURITY

Kill switch:
- highly privileged
- accessible during partial outage
- audited
- protected with strong auth

---

# 10.98 KILL SWITCH INTEGRITY

No ordinary feature flag should be able to disable the kill switch.

---

# 10.99 AUDIT LOG SECURITY

Audit records must be:
- append-oriented
- restricted
- integrity-protected
- time-stamped

---

# 10.100 AUDIT TAMPERING

Operators should not be able to silently delete their own audit trail.

Use restricted write paths and immutable storage where justified.

---

# 10.101 SECURITY LOGGING

Record:
login failures
MFA changes
admin actions
secret access
privilege changes
configuration changes
execution decisions
payment changes
suspicious requests

---

# 10.102 PRIVACY LOGGING

Do not log unnecessary PII.

Use pseudonymous IDs where possible.

---

# 10.103 PII CLASSIFICATION

Examples:
email
phone
country
identity documents
financial records

Map:
purpose
retention
access.

---

# 10.104 DATA MINIMIZATION

Collect only data required for:
service
security
legal/compliance
billing
support

---

# 10.105 PRIVACY BY DESIGN

At feature design:
ask:
What data?
Why?
Who needs it?
How long?
Where stored?
Can we avoid it?

---

# 10.106 DATA RETENTION

Define retention per class:
account
payment
audit
support
analytics
security logs
KYC
market data

---

# 10.107 RIGHT-TO-DELETE WORKFLOW

Account deletion should trigger:
session revocation
connection handling
data classification
anonymization/deletion where legally permitted
retention exceptions

---

# 10.108 DATA ACCESS REQUEST

If privacy law requires:
allow appropriate export/access workflow.

---

# 10.109 DATA CORRECTION

Users should be able to correct eligible profile data.

Financial history should remain accurate and should not be rewritten to alter historical truth.

---

# 10.110 ENCRYPTION KEY ACCESS

Use separate access policies for:
application
security
operations
backup

---

# 10.111 KYC DATA

If regulated onboarding requires KYC:
store separately
restrict access
audit access
encrypt
apply legal retention

---

# 10.112 SUPPORT ACCESS

Support sees:
safe operational state

Not:
raw secrets
private keys
passwords

---

# 10.113 CUSTOMER SCREENSHOTS

If sensitive areas are rendered:
consider screenshot protection on mobile/admin where technically appropriate.

Do not assume this prevents all capture.

---

# 10.114 CLIPBOARD

Do not automatically copy:
secrets
tokens

For legitimate copy actions:
clear clipboard guidance where platform permits.

---

# 10.115 BROWSER PASSWORD MANAGER

Use appropriate autocomplete semantics.

Do not disable password managers unnecessarily.

---

# 10.116 PHISHING RESISTANCE

Prefer:
passkeys
domain consistency
secure notifications
clear login URL patterns

Never ask customers for secrets through support chat.

---

# 10.117 CUSTOMER SUPPORT PHISHING

Official support must never request:
API Secret
password
MFA code

Publish this policy visibly.

---

# 10.118 ADMIN SOCIAL ENGINEERING

Operators should have:
identity verification procedure
high-risk action approval
no secret disclosure workflow

---

# 10.119 INSIDER THREAT

Use:
least privilege
dual control
audit
separation of duties
monitoring

---

# 10.120 SEPARATION OF DUTIES

Examples:
developer != production approver
support != security admin
content editor != payment admin
requester != dual-control approver

---

# 10.121 PRIVILEGED ACCESS MANAGEMENT

For critical roles:
just-in-time access
time-limited elevation
approval
audit

---

# 10.122 BREAK-GLASS PROCEDURE

When normal controls fail:
- strong identity
- reason
- approval where possible
- time limit
- alert
- retrospective review

---

# 10.123 SECURITY INCIDENT CLASSIFICATION

SEV0:
critical security/financial impact

SEV1:
major customer/security impact

SEV2:
significant

SEV3:
limited

SEV4:
informational

---

# 10.124 INCIDENT RESPONSE PHASES

PREPARE
DETECT
CONTAIN
ERADICATE
RECOVER
LEARN

---

# 10.125 INCIDENT DETECTION

Sources:
logs
metrics
SIEM
WAF
cloud alerts
provider alerts
customer reports
support
anomaly detection

---

# 10.126 INCIDENT CONTAINMENT

Actions:
revoke credentials
pause provider
disable feature
block IP patterns
revoke sessions
pause automation
isolate service

---

# 10.127 INCIDENT ERADICATION

Remove:
malicious code
compromised credentials
vulnerable component
unauthorized access

---

# 10.128 INCIDENT RECOVERY

Steps:
restore trusted state
rotate secrets
verify integrity
reconcile financial state
monitor

---

# 10.129 CUSTOMER NOTIFICATION

When required:
clear
accurate
timely
non-speculative

Explain:
what happened
impact
what user should do

---

# 10.130 FORENSICS

Preserve:
logs
timestamps
artifact digests
audit events
provider records

Do not contaminate evidence.

---

# 10.131 CHAIN OF CUSTODY

For serious incidents:
record:
who collected
when
where
hash
storage

---

# 10.132 POST-INCIDENT REVIEW

Record:
root cause
contributing factors
impact
detection gap
control failure
remediation
test added

---

# 10.133 VULNERABILITY MANAGEMENT

Track:
CVE
severity
affected components
exposure
owner
deadline
status

---

# 10.134 PATCH POLICY

Critical vulnerabilities:
expedited remediation.

Do not wait for the next quarterly release for exploitable critical issues.

---

# 10.135 SECURITY ADVISORIES

Monitor:
frameworks
cloud
OS
mobile SDKs
provider APIs
browser security changes

---

# 10.136 BUG BOUNTY / RESPONSIBLE DISCLOSURE

Publish a responsible disclosure process.

Provide:
security contact
scope
prohibited testing
response expectation

---

# 10.137 PENETRATION TESTING

Before public launch and periodically:
- web
- API
- mobile
- admin
- cloud
- authentication
- authorization
- trading controls

---

# 10.138 RED TEAM EXERCISES

When mature:
simulate:
account takeover
admin compromise
secret theft
provider compromise
supply-chain attack

---

# 10.139 SECURITY ARCHITECTURE REVIEW

Review at:
initial architecture
pre-launch
major feature
major provider
major cloud change
critical incident

---

# 10.140 THREAT MODEL REVIEW

Update when:
new data source
new payment provider
new exchange
new AI model
new mobile capability
new admin role
new region
new regulatory requirement

---

# 10.141 SECURE SDLC

STAGE 1:
requirements security review

STAGE 2:
threat modeling

STAGE 3:
secure design

STAGE 4:
implementation

STAGE 5:
automated security tests

STAGE 6:
manual security review

STAGE 7:
release approval

STAGE 8:
continuous monitoring

---

# 10.142 CODE REVIEW

Security-sensitive code requires designated reviewer.

Areas:
auth
payments
execution
risk
secrets
admin
cryptography
infra

---

# 10.143 SECURITY TEST FIXTURES

Maintain malicious fixtures:
XSS
SQLi
SSRF
JWT abuse
BOLA
CSRF
file upload
webhook replay
idempotency
prompt injection

---

# 10.144 AUTHORIZATION TEST MATRIX

Test:

customer → own resource = ALLOW
customer → other resource = DENY
support → support data = ALLOW
support → secrets = DENY
content → content = ALLOW
content → payments = DENY
finance → payments = ALLOW
finance → secrets = DENY
security → security logs = ALLOW
security → customer secrets = DENY

---

# 10.145 SECRET ACCESS TEST

Verify:
admin UI cannot retrieve raw secret
support API cannot retrieve raw secret
analytics cannot retrieve raw secret
AI cannot retrieve raw secret
logs cannot retrieve raw secret

---

# 10.146 PAYMENT SECURITY TEST

Verify:
price tampering
currency tampering
plan tampering
webhook replay
refund mismatch
entitlement race

---

# 10.147 TRADING SECURITY TEST

Verify:
order replay
order duplication
permission bypass
quantity tampering
risk bypass
policy bypass
wrong-account access
connection substitution

---

# 10.148 PROVIDER SECURITY TEST

Verify:
fake webhook
malformed response
stale response
out-of-order event
provider timeout
rate-limit behavior

---

# 10.149 AI SECURITY TEST

Verify:
prompt injection
tool abuse
secret extraction
schema violation
false confidence
malicious source
conflicting sources

---

# 10.150 ADMIN SECURITY TEST

Verify:
role escalation
IDOR
session theft
stale permissions
bulk action abuse
dual-control bypass

---

# 10.151 LOG SECURITY TEST

Inject:
secret-like strings
tokens
malicious control characters

Verify:
redaction
encoding
safe display

---

# 10.152 MONITORING SECURITY

Alerts:
secret access anomaly
admin role change
suspicious login
impossible travel signal
high-volume API abuse
sudden order spikes
unusual credential validation
provider mismatch

---

# 10.153 SECURITY DASHBOARD

Widgets:

Authentication
Admin Security
API Security
Secrets
Payments
Trading
Provider Health
AI Security
Vulnerabilities
Incidents
Audit

---

# 10.154 SECURITY SCORE

Do not show a simplistic security percentage unless methodology is rigorous.

Prefer:
PASS
WARNING
ACTION REQUIRED

with concrete controls.

---

# 10.155 CUSTOMER SECURITY CENTER

Customer sees:

MFA
Passkeys
Sessions
Devices
Connected Providers
Permission Status
Security Events

---

# 10.156 SECURITY EVENT COPY

Example:

“New device signed in.”

“An exchange connection was revoked.”

“Trading automation was paused because the provider connection became unhealthy.”

Avoid fear-inducing language unless necessary.

---

# 10.157 ADMIN SECURITY CENTER

Admin sees:
- privileged sessions
- security incidents
- permission changes
- suspicious events
- secrets access metadata
- authentication health

Not secret values.

---

# 10.158 SECURITY STATUS PAGE

Public page may show:
operational
degraded
incident

Do not expose:
attack paths
internal topology
customer details

---

# 10.159 SECURITY DOCUMENTATION

Public:
security overview
credential handling
privacy
responsible disclosure

Private:
runbooks
architecture
incident procedures
key maps

---

# 10.160 SECURITY CLAIM GOVERNANCE

Claims such as:
“military-grade encryption”
“bank-level security”
“zero risk”
“100% secure”
must not be used without precise evidence.

Prefer technical statements:
“Secrets are stored in dedicated secret-management infrastructure and are not displayed in the administration interface.”

---

# 10.161 CERTIFICATION CLAIMS

Do not claim:
SOC 2
ISO 27001
PCI DSS
GDPR certification
other certification

unless actually obtained and current.

Compliance with a regulation and certification against a standard are not the same claim.

---

# 10.162 PRIVACY POLICY LINKAGE

Every product surface involving personal data should have access to current privacy documentation.

---

# 10.163 CONSENT RECORDS

For required consents:
store:
document
version
locale
timestamp
user

---

# 10.164 LEGAL DOCUMENT INTEGRITY

Published legal versions should be immutable after publication.

New changes produce a new version.

---

# 10.165 ADMIN CONTENT SECURITY

CMS editors must not be able to inject arbitrary executable code into public pages.

Sanitize rich content.

---

# 10.166 SEO SECURITY

Protect:
redirects
canonical URLs
metadata
sitemap generation

Prevent:
open redirects
spam injection
unauthorized content publication

---

# 10.167 BLOG ABUSE

Protect comments/UGC if enabled against:
spam
phishing
malware links
XSS
financial scams

---

# 10.168 EMAIL SECURITY

Configure:
SPF
DKIM
DMARC

Use a controlled sending domain.

Monitor abuse and bounce patterns.

---

# 10.169 EMAIL LINK SECURITY

Links should:
use official domains
avoid unnecessary tracking secrets
expire for sensitive actions

---

# 10.170 ACCOUNT TAKEOVER DEFENSE

Signals:
unusual login
new device
new country
password reset
MFA change
provider connection change

Response:
step-up
session revoke
notification
manual review where appropriate

---

# 10.171 CUSTOMER SESSION REVOCATION

User can:
sign out all sessions
revoke device
revoke suspicious session

Admin can do so with proper authorization.

---

# 10.172 PROVIDER CONNECTION REVOCATION

Customer can revoke.

System can revoke on:
credential compromise
subscription state
security event
provider error

All actions audited.

---

# 10.173 STRATEGY SECURITY

Production strategy:
immutable version
approved state
integrity validation

---

# 10.174 RISK ENGINE SECURITY

Risk logic is security-critical business logic.

Protect against:
tampering
configuration injection
integer overflow
precision errors
race conditions
stale account state

---

# 10.175 FINANCIAL PRECISION SECURITY

Use safe decimal representations.

Test:
rounding
overflow
underflow
extreme quantities
large balances

---

# 10.176 CONCURRENCY SECURITY

Protect against:
double order
double refund
double entitlement
double pause
double credential rotation

Use:
idempotency
transactions
locks where justified
state machines

---

# 10.177 RACE CONDITION EXAMPLES

Race:
payment webhook + dashboard request

Race:
two order requests

Race:
credential replacement + execution

Race:
subscription expiration + order submission

Every race must have deterministic outcome.

---

# 10.178 SECURE ORDER SEQUENCE

1 authenticate
2 authorize
3 entitlement
4 eligibility
5 connection
6 market data
7 strategy
8 risk
9 policy
10 idempotency
11 final provider validation
12 submit
13 reconcile
14 audit

---

# 10.179 FINAL EXECUTION SECURITY BARRIER

Immediately before provider call:
revalidate:
user state
connection state
risk
policy
market freshness
order constraints
idempotency

---

# 10.180 SECURITY OF RECONCILIATION

Reconciliation is security-sensitive because it can change customer-visible state.

Only trusted services may modify reconciled status.

---

# 10.181 FINANCIAL RECORD IMMUTABILITY

Do not rewrite historical fills/orders to hide incidents.

Use:
corrections
adjustments
superseding records

---

# 10.182 INCIDENT LOCK SECURITY

Incident lock cannot be removed by ordinary customer frontend.

Only authorized backend policy/admin action.

---

# 10.183 GLOBAL PAUSE

Global pause must have:
independent control path
strong auth
audit
monitoring

---

# 10.184 SECURITY FAILURE PRINCIPLE

If critical security control is unavailable:
deny sensitive action.

Examples:
secret manager unavailable
→ no new credential operation

risk service unavailable
→ no automated execution

audit service unavailable
→ sensitive mutation blocked or queued safely depending on documented policy

---

# 10.185 OBSERVABILITY FAILURE

Do not silently disable security logging because the logging provider is unavailable.

Use local buffering / durable queue / safe degradation according to policy.

---

# 10.186 SECURITY BACKUP

Back up:
critical configurations
audit records
security policies

Backups are themselves sensitive.

---

# 10.187 BACKUP ACCESS

Separate backup credentials from production runtime.

---

# 10.188 RESTORE SECURITY

Before restoring to production:
verify integrity
verify artifact/version
verify backup provenance

---

# 10.189 COMPROMISED BACKUP

Do not restore an untrusted backup without investigation.

---

# 10.190 SECURITY DRILL

At least periodically simulate:
secret compromise
admin takeover
database breach
provider compromise
malicious deployment

---

# 10.191 TABLETOP EXERCISE

Required participants:
security
engineering
operations
support
legal/compliance where applicable
executive owner

---

# 10.192 SECURITY TRAINING

Train staff on:
phishing
social engineering
secret handling
customer verification
incident reporting
least privilege

---

# 10.193 DEVELOPER SECURITY TRAINING

Train on:
OWASP risks
secure coding
auth
secrets
financial logic
AI prompt injection
dependency security

---

# 10.194 ADMIN SECURITY TRAINING

Train on:
privileged access
dual control
support boundaries
incident escalation
secret handling

---

# 10.195 SUPPORT SECURITY TRAINING

Support must know:
never request passwords
never request API secrets
never request MFA codes
verify identity before account actions

---

# 10.196 SECURITY ACCEPTANCE GATE

[ ] Threat model completed
[ ] Asset inventory complete
[ ] Trust boundaries documented
[ ] Data flows documented
[ ] IAM reviewed
[ ] RBAC tested
[ ] Object authorization tested
[ ] Function authorization tested
[ ] Secrets isolated
[ ] Secrets cannot be seen by admin/support
[ ] MFA/passkeys tested
[ ] Sessions revocable
[ ] CSRF protected
[ ] XSS tests passed
[ ] SSRF protected
[ ] SQL/NoSQL injection protected
[ ] Rate limits active
[ ] Payment webhook verified
[ ] Replay protection active
[ ] Exchange credentials protected
[ ] Withdrawal permissions blocked where policy requires
[ ] AI tools restricted
[ ] Prompt injection tests passed
[ ] Dependency scanning active
[ ] SBOM generated
[ ] Artifacts trusted
[ ] Audit logs protected
[ ] Incident response tested
[ ] Backup/restore tested
[ ] DR exercised
[ ] Security documentation current

---

# 10.197 PRODUCTION SECURITY PRINCIPLE

A system is secure only to the extent that its controls are:

implemented
tested
monitored
maintained
reassessed

A written policy without enforcement is not a control.

---

# 10.198 MASTER SECURITY PRINCIPLE

For any critical action:

PROVE IDENTITY
→ PROVE AUTHORIZATION
→ PROVE ELIGIBILITY
→ PROVE VALID STATE
→ EXECUTE MINIMAL ACTION
→ RECORD EVIDENCE
→ VERIFY OUTCOME

---

# 10.199 MASTER INCIDENT PRINCIPLE

When compromise is suspected:

CONTAIN FIRST
PRESERVE EVIDENCE
REVOKE CREDENTIALS
PROTECT CUSTOMERS
RECONCILE FINANCIAL STATE
RECOVER FROM TRUSTED ARTIFACTS
LEARN
IMPROVE

---

# 10.200 PART 10 FINAL DIRECTIVE

Treat security as an architectural property rather than a feature.

The platform must remain safe even when:
- the frontend is manipulated
- the user device is compromised
- one service fails
- one provider fails
- one AI model fails
- an administrator makes an error
- a credential leaks
- a dependency becomes vulnerable
- a malicious actor attempts to exploit race conditions
- external data is poisoned

The security objective is not:

“prevent every possible attack.”

The objective is:

REDUCE ATTACK SURFACE
LIMIT PRIVILEGE
DETECT ABUSE
CONTAIN COMPROMISE
PROTECT FINANCIAL STATE
PROTECT CUSTOMER DATA
MAINTAIN AUDITABILITY
RECOVER SAFELY

END OF PART 10
