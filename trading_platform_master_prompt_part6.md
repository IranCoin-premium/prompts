# TRADING PLATFORM MASTER PROMPT
# PART 6 — COMPLETE FRONTEND / DESIGN SYSTEM / UX / MOTION / PAGE-BY-PAGE IMPLEMENTATION SPECIFICATION

Continuation of Parts 1–5.

This part defines the frontend experience as an implementation contract. It is intentionally framework-agnostic at the product level: the implementation team may select the final frontend framework after validating the current ecosystem, performance, accessibility, PWA support, desktop/mobile strategy, and long-term maintenance requirements.

NON-NEGOTIABLE PRINCIPLES

1. Premium does not mean visually noisy.
2. Animation must communicate hierarchy, not distract.
3. Every animation has a reduced-motion fallback.
4. Financial interfaces must feel calm, precise, and trustworthy.
5. The interface must never imply guaranteed returns.
6. No visual treatment may hide risk, fees, renewal terms, or restrictions.
7. RTL and LTR are first-class layouts, not afterthoughts.
8. English and Persian must both be native-quality experiences.
9. The frontend must never be the authority for permissions, entitlements, risk, payment state, or execution.
10. Every sensitive UI action must have a server-side authorization counterpart.

---

# 6.0 FRONTEND EXPERIENCE ARCHITECTURE

The frontend is divided into:

PUBLIC EXPERIENCE
AUTH EXPERIENCE
CUSTOMER EXPERIENCE
TRADING EXPERIENCE
CONTENT EXPERIENCE
SUPPORT EXPERIENCE
ADMIN EXPERIENCE

Shared foundation:

DESIGN SYSTEM
LOCALIZATION
AUTHENTICATION
ANALYTICS
ERROR HANDLING
NOTIFICATIONS
ACCESSIBILITY
MOTION SYSTEM
PERFORMANCE SYSTEM

Suggested route architecture:

/
/pricing
/features
/technology
/security
/how-it-works
/about
/contact
/blog
/blog/[slug]
/docs
/docs/[slug]
/faq
/legal/*
/login
/register
/verify
/forgot-password
/dashboard
/dashboard/signals
/dashboard/automation
/dashboard/connections
/dashboard/orders
/dashboard/positions
/dashboard/journal
/dashboard/subscription
/dashboard/security
/dashboard/settings
/checkout
/checkout/success
/checkout/failed
/support
/404
/500
/admin/*

Routes must be generated from capability and policy where appropriate.

---

# 6.1 VISUAL DIRECTION

The visual language should combine:

precision
editorial typography
large negative space
high-quality imagery
subtle depth
controlled gradients
premium materials
restrained glass effects
technical diagrams
data visualization
micro-interactions
cinematic section transitions

Do not copy Apple.com literally.

Use Apple-like principles:
clarity
large-scale storytelling
progressive disclosure
high-quality motion
product focus
careful typography

Do not copy proprietary layouts, assets, copy, or branding.

---

# 6.2 DESIGN PERSONALITY

Brand attributes:

PREMIUM
INTELLIGENT
DISCIPLINED
CALM
ADVANCED
TRANSPARENT
SECURE
FORWARD-LOOKING

Avoid:

CASINO
MEME-COIN
HYPE
GAMBLING
GET-RICH-QUICK
CRYPTO-BRO
OVERLY-MILITARY
FAKE-AI
FINANCIAL-GURU

---

# 6.3 COLOR SYSTEM

Use semantic tokens rather than raw colors.

Required token groups:

brand-primary
brand-secondary
brand-accent

background-primary
background-secondary
background-elevated
background-inverse

text-primary
text-secondary
text-muted
text-inverse

border-subtle
border-default
border-strong

success
warning
danger
info

market-positive
market-negative
market-neutral

risk-low
risk-medium
risk-high

Do not use red and green as the only distinction.

Pair color with:
icon
label
shape
text
position

---

# 6.4 TYPOGRAPHY

Provide at least four Persian-compatible font families and four English-compatible font families after licensing and technical review.

One Persian option must be Vazirmatn / وزیرمتن or an equivalent officially licensed Vazir-family implementation.

Typography system:

display-xl
display-lg
display-md
heading-xl
heading-lg
heading-md
heading-sm
body-lg
body-md
body-sm
caption
overline
numeric-xl
numeric-lg
numeric-md
code

Financial numbers must use tabular numerals where appropriate.

---

# 6.5 FONT RULE

Do not blindly force the same font in Persian and English.

Define:

Persian primary
Persian secondary
Latin primary
Latin secondary
Monospace

Use font fallback chains that preserve readable Persian glyphs.

Verify:
numbers
punctuation
Arabic/Persian digits
Latin ticker symbols
currency symbols
minus signs
decimal separators

---

# 6.6 RTL / LTR ARCHITECTURE

Language switch must change:

document direction
layout direction
typography
navigation alignment
icon direction where meaningful
table alignment
number presentation where configured
motion origin where directionally meaningful

Do not simply mirror the entire page.

Examples:

Back arrow may reverse.
Play icon generally does not.
Charts generally preserve market chronology.
Brand logo orientation does not change unless designed for it.

---

# 6.7 LANGUAGE SWITCHER

Desktop:
two compact circular flag controls.

Required:
Iran / Persian
United Kingdom / English

The flags are language controls, not statements about nationality.

Mobile:
compact segmented language control or accessible menu.

Every language switch:
- preserves current route where translation exists
- preserves relevant page state where safe
- updates document language
- updates direction
- updates SEO alternate metadata on public pages

---

# 6.8 PERSIAN QUALITY GATE

Persian UI must be reviewed by a native Persian editor.

Check:

formal/informal tone
financial terminology
spacing
 نیم‌فاصله
Persian punctuation
digits
currency
dates
pluralization
button length
line wrapping
RTL table behavior

Never ship machine-translated Persian blindly.

---

# 6.9 ENGLISH QUALITY GATE

English must be natural, concise, professional product English.

Avoid literal translations from Persian.

Check:
tone
grammar
financial terminology
technical accuracy
CTA clarity
legal meaning

---

# 6.10 SPACING SYSTEM

Use a consistent spacing scale.

Example conceptual scale:

2
4
8
12
16
24
32
40
48
64
80
96
128
160
192

Do not create arbitrary spacing values in individual components.

---

# 6.11 LAYOUT GRID

Desktop:
12-column conceptual grid.

Tablet:
8-column conceptual grid.

Mobile:
4-column conceptual grid.

Use fluid containers.

Avoid excessive maximum-width constraints on cinematic hero sections.

Text measure should remain readable.

---

# 6.12 RESPONSIVE BREAKPOINTS

Do not design around device names only.

Use content-driven breakpoints.

Test:
small phone
large phone
tablet portrait
tablet landscape
laptop
desktop
large desktop

---

# 6.13 HEADER

Header states:

transparent
scrolled
dark
light
dashboard
admin

Public header:

Logo
Products
How It Works
Technology
Pricing
Blog
Security
Language
Sign In
Get Started

Mobile:
Logo
Menu
Primary CTA

Do not overload the header.

---

# 6.14 HERO SECTION

Hero structure:

eyebrow
headline
supporting statement
primary CTA
secondary CTA
trust/safety context
visual centerpiece

Example positioning:

“Intelligent trading infrastructure, built around discipline.”

Do not say:
“Guaranteed AI profits.”

CTA examples:

Explore the Platform
View Plans
See How It Works

---

# 6.15 HERO MOTION

Initial load:

1. background enters
2. logo/eyebrow fades
3. headline rises subtly
4. supporting text appears
5. CTA appears
6. visual system activates

Total initial animation should be fast enough that the user can interact immediately.

Do not delay the CTA for cinematic animation.

---

# 6.16 360° PRODUCT STORY

Use a scroll-controlled visual sequence for appropriate product storytelling.

Implementation model:

SCROLL POSITION
→ NORMALIZED PROGRESS
→ FRAME INDEX
→ IMAGE FRAME

Requirements:

- responsive frame selection
- preloading window
- lazy loading
- poster frame
- low-resolution preview
- high-resolution replacement
- touch support
- keyboard fallback
- reduced-motion fallback

Never require 360° motion to understand the product.

---

# 6.17 360° ASSET PIPELINE

Prepare:

poster
thumbnail
low-resolution sequence
high-resolution sequence
mobile sequence if needed
desktop sequence
manifest
frame count
dimensions
compression metadata

Prefer efficient image formats.

Do not load 300+ full-resolution frames immediately.

Use:
near-viewport prefetch
adaptive quality
intersection observer
progressive loading

---

# 6.18 SCROLL CHOREOGRAPHY

Every cinematic section follows:

ENTER
HOLD
TRANSFORM
EXIT

Example:

0–15%:
visual enters

15–50%:
360° rotation

50–70%:
text transformation

70–90%:
product detail

90–100%:
transition

Do not make every section pin the viewport.

Use pinning selectively.

---

# 6.19 TEXT-IMAGE SYNCHRONIZATION

When visual progress reaches a threshold:

headline changes
supporting paragraph changes
annotation appears
metric appears

All text changes must remain understandable if animation is disabled.

Use semantic DOM content rather than rendering all important text into canvas.

---

# 6.20 REDUCED MOTION

If prefers-reduced-motion is enabled:

- no parallax
- no continuous rotation
- no forced scroll animation
- no large transforms
- use simple fades
- provide static visual

The experience must remain premium.

---

# 6.21 MOTION TOKENS

Define:

motion-fast
motion-standard
motion-slow
motion-cinematic

Define easing:
ease-standard
ease-emphasis
ease-enter
ease-exit

Do not use random easing curves.

---

# 6.22 BUTTON SYSTEM

Variants:

primary
secondary
tertiary
ghost
danger
success

States:

default
hover
focus
active
disabled
loading
success
error

Every button needs accessible name.

Loading buttons must prevent duplicate submission.

---

# 6.23 FORM SYSTEM

Inputs:

text
email
password
OTP
select
combobox
date
currency
number
slider
toggle
checkbox
radio

Every input:
label
description where needed
error
required state
autocomplete where appropriate

Never rely on placeholder as the label.

---

# 6.24 PASSWORDLESS / PASSKEY UX

If passkeys are supported:

Create passkey
Use passkey
Manage passkeys
Remove passkey

Explain the feature in plain language.

Fallback:
secure authentication method.

Never lock a customer out solely because a browser lacks passkey support.

---

# 6.25 TOAST SYSTEM

Use for:
successful non-critical actions
background completion
minor warnings

Do not use toast for:
critical risk disclosure
payment terms
irreversible financial action
security incidents

Critical messages remain visible.

---

# 6.26 MODAL SYSTEM

Use modal for:
confirmation
focused configuration
security step-up
important legal confirmation

Do not place entire complex dashboards inside modals.

Mobile modals may become full-screen sheets.

---

# 6.27 DRAWER SYSTEM

Use drawers for:
filters
details
secondary navigation
activity

Maintain keyboard focus.

Close with:
X
Escape
outside click where safe

---

# 6.28 TABLE SYSTEM

Financial tables must support:

sorting
filtering
pagination
responsive adaptation
column visibility
empty state
loading
error
stale state

On mobile:
convert selected rows into cards or horizontal scroll.

Do not make the entire dashboard a tiny desktop table on mobile.

---

# 6.29 CHART SYSTEM

Charts must show:

title
time range
units
data source where useful
last updated
empty state
error state

Do not use decorative charts that imply predictive certainty.

---

# 6.30 SIGNAL CARD

Structure:

instrument
market mode
direction
signal strength
confidence
risk
age
strategy
status

Visual hierarchy:

Instrument
→ Direction
→ Strength
→ Risk
→ Timestamp

The card must not resemble a casino betting slip.

---

# 6.31 SIGNAL DETAIL PAGE

Sections:

Overview
Why This Signal
Market Context
Strategy Context
Risk Context
AI-Assisted Analysis
Evidence
Execution Status
Expiration
Disclaimer

If the signal is expired:
show EXPIRED prominently.

---

# 6.32 AI ANALYSIS UI

Do not show fake “thinking animations” implying consciousness.

Show:

AI-assisted analysis
Model ensemble status
Evidence sources
Last update
Data quality
Consensus
Uncertainty

Example:

“3 analytical agents evaluated the current context.”

Do not state:
“Three AIs guarantee the trade.”

---

# 6.33 THREE-AGENT PRODUCT STORY

If the business actually implements the architecture described previously, present:

STRATEGY AGENT
Evaluates strategy conditions and trade eligibility.

RISK AGENT
Evaluates exposure, position sizing, and risk constraints.

RESEARCH AGENT
Processes approved market/news/fundamental information.

Then state:

“The final decision is subject to deterministic risk and policy controls.”

Do not present AI as autonomous authority.

---

# 6.34 “16 ANALYSTS” CLAIM

Do not publish a claim such as “16 analysts” unless the actual system contains 16 independently defined analytical components and the methodology is documented.

If implemented, name the categories rather than inventing personalities.

Potential analytical dimensions:

trend
momentum
volatility
volume
market structure
support/resistance
multi-timeframe
liquidity
spread
fundamental context
news
correlation
regime
sentiment
risk
execution quality

The count must match the actual implementation.

---

# 6.35 CLOUD MODEL CLAIMS

Do not promise “100% uptime.”

If a cloud AI provider/model is used:
display provider/model information only where contractually and technically accurate.

Do not claim that a named model is running 24/7 unless the production architecture actually does so.

Keep marketing copy separate from implementation assumptions.

---

# 6.36 PRODUCT COMPARISON SECTION

Compare:

Signals
Automation
Market modes
Risk controls
Analytics
Journal
Notifications
Support
Platform availability

Do not compare plans using misleading “value” badges if all plans have identical capabilities and differ only in duration.

---

# 6.37 PRICING PAGE

Required:

monthly/term price
currency
duration
renewal
features
market modes
availability
risk disclosure
refund link
support

Optional:
effective monthly equivalent

Avoid:
fake original price
fake discount
fake countdown

---

# 6.38 SUBSCRIPTION TERM SELECTOR

Options:

7 Days
1 Month
3 Months
6 Months
12 Months

If capabilities are identical:
display:

“Every plan includes the same platform capabilities. Only the subscription duration differs.”

---

# 6.39 PRODUCT CONFIGURATION WIZARD

Step 1:
Choose service

SIGNALS
AUTOMATION

Step 2:
Choose market

CRYPTO
FOREX

If a binary-options-like product is legally/platform eligible in the user's context, it appears only after server-side policy validation.

Step 3:
Choose mode

Crypto:
Spot
Futures / Perpetuals
Hybrid

Forex:
Broker / MT5
Hybrid where actually supported

Step 4:
Choose subscription term

Step 5:
Review

Step 6:
Payment

Step 7:
Connection

---

# 6.40 MARKET MODE CARDS

Each mode card includes:

Name
Short description
Who it is for
Mechanics
Key risks
Availability
Supported providers

Example:

SPOT

“Designed for direct purchase/sale of supported digital assets. It does not use futures-style liquidation mechanics, although asset prices can still fall significantly.”

FUTURES / PERPETUALS

“Derivative contracts may involve margin, leverage, funding and liquidation. Losses can be substantial.”

HYBRID

“Combines approved spot and derivative strategies according to the platform's configuration.”

---

# 6.41 FOREX CARD

Use:

FOREX — BROKER / MT5

Explain:

“Forex automation is executed through a supported broker account and, where applicable, MetaTrader 5 infrastructure. Position sizing may use lots and broker-defined contract specifications.”

Do not say:
“Forex is basically futures.”

---

# 6.42 BINARY-OPTIONS AVAILABILITY CARD

Where unavailable:

“Unavailable on this platform or in your region.”

Provide:
reason category
educational alternative where lawful
web availability only if legally permitted
support information

Do not disguise the feature.

---

# 6.43 CHECKOUT PAGE

Layout:

Order summary
Product
Market
Mode
Duration
Price
Taxes
Renewal
Risk disclosure
Terms
Payment

CTA:
“Confirm Subscription”

Before final submission:
validate entitlement availability again.

---

# 6.44 CHECKOUT SUCCESS

Show:

Payment confirmed
Subscription active/pending
Next step
Connection setup
Dashboard CTA

If payment succeeded but entitlement activation is delayed:

“Payment received. Your subscription is being activated.”

Do not falsely say “Active” until backend confirms it.

---

# 6.45 CHECKOUT FAILURE

States:

payment declined
payment pending
provider timeout
verification required
duplicate request

Every state needs:
human-readable message
next action
support route

---

# 6.46 CONNECTION WIZARD

Step:

Provider
→ Permissions
→ Credential entry
→ Secure submission
→ Validation
→ Test connection
→ Ready

Before credential entry explain:
what is requested
why it is requested
what permissions are needed
what permissions are not needed

---

# 6.47 API KEY UX

Never ask users to paste a secret into an ordinary chat box.

Use a secure form.

Fields:
API Key
API Secret

Optional:
Passphrase / account identifier only where provider requires it.

Immediately transmit to secure backend handling.

Never:
display secret after submission
put secret into URL
put secret into analytics
put secret into logs
store in localStorage

---

# 6.48 PERMISSION EXPLANATION

Display permissions in plain language.

Example:

READ:
“The platform can view account and market information.”

TRADE:
“The platform may submit approved orders.”

WITHDRAW:
“Withdrawal permission is not required and should remain disabled.”

If a provider allows IP restrictions:
recommend them.

---

# 6.49 CONNECTION SUCCESS

Show:

Connected
Provider
Account reference
Permission profile
Last validation
Supported modes

CTA:
Continue to Dashboard

---

# 6.50 CONNECTION FAILURE

Show:

What failed
Whether credentials were accepted
Whether the provider was reachable
Whether permissions are sufficient
What to try next

Never reveal raw provider error payloads if they contain sensitive information.

---

# 6.51 AUTOMATION DASHBOARD

Sections:

Automation Status
Current Strategy
Risk Profile
Connection
Last Decision
Open Positions
Recent Orders
Safety Controls

Primary control:
Pause Automation

This should be visually prominent and accessible.

---

# 6.52 PAUSE CONTROL

Pause states:

PAUSING
PAUSED
RESUMING
ACTIVE
BLOCKED

When paused:
new automated orders are blocked.

Existing positions are not automatically closed unless an explicitly configured and legally appropriate policy says so.

---

# 6.53 EMERGENCY STOP

If implemented:

“Emergency Stop”

Must explain exact semantics.

Possible semantics:
block new automation
cancel eligible pending orders
do not automatically close existing positions unless explicitly configured

Do not use vague “STOP EVERYTHING.”

---

# 6.54 RISK CENTER

Show:

risk profile
current exposure
concentration
leverage
daily risk state
limits
recent risk blocks

Example:

“Automation is currently paused because market-data freshness is below the configured threshold.”

---

# 6.55 ORDERS PAGE

Filters:

date
instrument
status
strategy
side
market
provider

Status labels:

Pending
Submitted
Acknowledged
Partially Filled
Filled
Rejected
Cancelled
Unknown
Reconciled

---

# 6.56 UNKNOWN ORDER STATE

This is critical.

If provider confirmation is uncertain:

show:

“Order status pending reconciliation.”

Do not allow a second identical order merely because the first response timed out.

---

# 6.57 POSITIONS PAGE

Show:

instrument
side
quantity
entry
mark
unrealized P/L
margin
leverage
last update

Use clear warnings when data is stale.

---

# 6.58 JOURNAL PAGE

Timeline:

Signal
Risk Decision
Execution
Fill
Position
Outcome

Each event has timestamp and source.

---

# 6.59 SECURITY PAGE

Sections:

MFA
Passkeys
Sessions
Devices
Exchange Connections
Security Events
Account Recovery

Use security-centered language.

---

# 6.60 SETTINGS

Sections:

Profile
Language
Timezone
Currency
Notifications
Risk Profile
Privacy
Security
Connected Providers
Subscription

---

# 6.61 BLOG HOMEPAGE

Hero article
Featured guides
Latest analysis
Risk education
Glossary
Search
Categories

Do not make every article a trading call.

Educational content improves trust and long-term SEO.

---

# 6.62 ARTICLE PAGE

Structure:

Title
Summary
Author
Reviewer
Published
Updated
Reading time
Content
Sources
Related articles
Risk note
CTA

Use semantic headings.

---

# 6.63 CONTENT QUALITY

Articles must avoid:

unsupported claims
fabricated statistics
fake citations
guaranteed returns
undisclosed affiliate relationships
fake expert authorship

AI-generated content must receive editorial quality control.

---

# 6.64 ABOUT PAGE

Tell:

why platform exists
engineering philosophy
risk philosophy
technology
team
security
support

Do not invent team credentials.

---

# 6.65 SECURITY PAGE

Explain:

encryption
secret isolation
access control
audit logs
authentication
monitoring
incident response
data minimization

Do not reveal exploitable infrastructure details.

---

# 6.66 CONTACT PAGE

Provide:

support channels
business inquiry
technical support
legal/compliance contact where appropriate
response expectations

Avoid exposing personal employee information unnecessarily.

---

# 6.67 404 PAGE

Design:

minimal
premium
slightly playful
clear navigation

Example:

“Lost in the market?”

Then:
“The page you're looking for is not available.”

Buttons:
Back Home
Open Dashboard
Search Blog

Do not over-animate.

---

# 6.68 500 PAGE

Message:

“Something went wrong on our side.”

Actions:
Retry
Home
Support

Include incident reference ID where appropriate.

Do not expose stack traces.

---

# 6.69 LOADING EXPERIENCE

Use skeletons for data-heavy screens.

Use cinematic loading only for initial application boot where it does not delay interaction.

Never show:
“AI is making millions of calculations”
unless literally measurable and accurate.

---

# 6.70 EMPTY STATES

Every list needs an empty state.

Example:

No signals:
“No active signals match your filters.”

No connection:
“Connect a supported provider to continue.”

No orders:
“No orders have been recorded yet.”

No blog results:
“No articles match your search.”

---

# 6.71 ERROR STATES

Every feature needs:
loading
success
empty
partial
error
offline
stale
permission denied

Do not design only the happy path.

---

# 6.72 STALE DATA UI

If data age exceeds threshold:

show:
STALE
Last updated X ago

For trading-critical data:
disable relevant action.

---

# 6.73 SKELETON SYSTEM

Skeletons should resemble actual content.

Avoid full-screen generic spinners.

Use spinner only for short blocking actions.

---

# 6.74 RESPONSIVE DASHBOARD

Desktop:
sidebar + content

Tablet:
collapsible sidebar

Mobile:
bottom navigation or compact navigation

Suggested mobile nav:

Home
Signals
Automation
Activity
Account

Admin mobile navigation should be separate.

---

# 6.75 ADMIN DESIGN LANGUAGE

Admin is premium but operational.

Priorities:

information density
speed
clarity
safe controls
auditability

Avoid cinematic animation in operational screens where it slows work.

---

# 6.76 ADMIN HOME

Widgets:

System Health
Trading Health
Payments
Subscriptions
Exchange Health
AI Health
Incidents
Pending Approvals

Every widget has:
status
last updated
drill-down

---

# 6.77 ADMIN CUSTOMER DETAIL

Sections:

Profile
Subscription
Entitlements
Payments
Connections
Activity
Security
Support
Audit

Secrets never appear.

---

# 6.78 ADMIN SUBSCRIPTION DETAIL

Show:

product
market
mode
term
status
payment state
entitlement
start
end
renewal
policy restrictions

Actions require permissions.

---

# 6.79 ADMIN PAYMENT DETAIL

Show:

provider
transaction reference
amount
currency
status
timestamps
reconciliation state

Mask sensitive payment data.

---

# 6.80 ADMIN STRATEGY PAGE

Show:

strategy
version
state
deployment
risk policy
market modes
instruments
last evaluation
health

Actions:

pause
resume
promote
rollback

Promotion requires appropriate approval.

---

# 6.81 ADMIN AI PAGE

Show:

provider
model
version
latency
failure rate
cost
quality score
last evaluation
status

Actions:
disable provider
switch approved fallback
review evaluation

Never expose unrestricted model controls to ordinary admins.

---

# 6.82 ADMIN FEATURE FLAGS

Show:

flag
description
environment
current value
target
owner
expiry
audit

Dangerous flags require confirmation.

---

# 6.83 ADMIN CONTENT CMS

Functions:

draft
edit
preview
translate
review
schedule
publish
unpublish
archive

Publishing must record:
author
reviewer
version
timestamp

---

# 6.84 SEO CONTROL PANEL

Admin may manage:

title
description
canonical
robots
social image
schema
redirect

Dangerous SEO changes require audit.

---

# 6.85 COMPONENT INVENTORY

Build reusable components:

AppShell
Header
Footer
Sidebar
BottomNav
Button
IconButton
Badge
Tag
Card
GlassCard
Section
Container
Grid
Stack
Heading
Text
Link
Tooltip
Popover
Modal
Drawer
Tabs
Accordion
Dropdown
Select
Combobox
Input
Textarea
PasswordInput
OTPInput
DatePicker
Slider
Switch
Checkbox
Radio
Table
DataTable
Chart
Metric
Timeline
Progress
Skeleton
Toast
Alert
Banner
EmptyState
ErrorState
StatusIndicator
LanguageSwitcher
CurrencyDisplay
PriceDisplay
RiskBadge
SignalCard
OrderCard
ConnectionCard
SubscriptionCard

---

# 6.86 COMPONENT RULE

Each component must specify:

purpose
variants
props
states
accessibility
responsive behavior
RTL behavior
motion behavior
loading behavior
error behavior

---

# 6.87 DESIGN TOKEN IMPLEMENTATION

Never put business logic into design tokens.

Tokens define appearance.

Business rules live in domain/application layers.

---

# 6.88 ICONOGRAPHY

Use one coherent icon family.

Do not mix:
outline
filled
3D
emoji
random SVG styles

Use semantic icons.

Never use color alone for status.

---

# 6.89 IMAGE ART DIRECTION

Visuals should communicate:

technology
precision
markets
security
infrastructure
human trust

Avoid cliché:
bulls
bears
gold coins
rocket ships
cash rain
luxury cars

The brand should look like a serious technology company.

---

# 6.90 BRAND LOGO SYSTEM

Logo generation may use AI-assisted ideation, but final logo must be:

original
legally usable
vectorized
legible
scalable
monochrome-compatible
favicon-compatible
app-icon compatible

Required outputs:

horizontal
symbol
stacked
monochrome
light-background
dark-background
favicon
app icon

Perform trademark clearance before commercial launch.

---

# 6.91 COLOR PALETTE DELIVERABLE

The design team must produce one visual palette sheet containing:

Primary
Secondary
Accent
Background
Surface
Text
Muted
Success
Warning
Danger
Info

Each swatch includes:
HEX
RGB
HSL
usage
contrast note

Do not select colors solely because they “look luxurious.”

Verify accessibility contrast.

---

# 6.92 LANDING PAGE SECTION ORDER

Recommended:

1 Hero
2 Trust / positioning
3 Product story
4 360° visual
5 How it works
6 Market coverage
7 AI architecture
8 Risk architecture
9 Signals
10 Automation
11 Security
12 Platform ecosystem
13 Pricing
14 Education / blog
15 FAQ
16 Final CTA
17 Footer

Order may be optimized using evidence and testing.

---

# 6.93 “HOW IT WORKS”

Five stages:

1 CONNECT
2 CONFIGURE
3 ANALYZE
4 RISK-CHECK
5 EXECUTE / DELIVER SIGNAL

If automation is disabled:
signal-only flow.

---

# 6.94 AI SECTION

Visual:

three-node architecture:

Research
Strategy
Risk

Then:

Decision Layer
→ Policy Engine
→ Signal / Execution

Make clear that risk/policy controls remain authoritative.

---

# 6.95 SECURITY SECTION

Visual layers:

Client
Identity
API Gateway
Application
Secrets
Execution
Audit

Use subtle animation.

Do not expose real infrastructure topology.

---

# 6.96 PLATFORM SECTION

Show:

Web
PWA
Android
iOS
Windows
macOS

For each:
availability
capability
limitations

Do not use platform logos in ways that imply endorsement.

---

# 6.97 PRICING PSYCHOLOGY

Use:
clarity
comparability
low cognitive load

Avoid:
artificial urgency
fake social proof
fake popularity
misleading anchoring

If one plan is visually emphasized, label it based on a real business reason.

---

# 6.98 SOCIAL PROOF

Only real:
customer count
reviews
case studies
media mentions
security certifications
partnerships

Synthetic testimonials are prohibited in production.

---

# 6.99 FOOTER

Columns:

Product
Markets
Technology
Security
Resources
Company
Legal

Bottom:
Language
Currency
Copyright
Risk notice
Privacy
Terms

---

# 6.100 SEO / PERFORMANCE RULE

Do not let animation destroy SEO.

Critical text must exist in crawlable HTML.

Images:
responsive
compressed
lazy where appropriate
preloaded only when critical

Fonts:
subset
preload only critical
avoid excessive font families on first paint

---

# 6.101 ACCESSIBILITY IMPLEMENTATION

Every interactive element:

keyboard accessible
focusable
labeled
visible focus
appropriate role
correct state

Do not create clickable divs when native controls exist.

---

# 6.102 SCREEN READER TESTING

Test:

NVDA / Windows
VoiceOver / Apple platforms
TalkBack / Android

At minimum for critical flows.

---

# 6.103 FOCUS MANAGEMENT

After navigation:
move focus appropriately.

After modal open:
focus first meaningful control.

After modal close:
return focus to triggering element.

---

# 6.104 KEYBOARD SHORTCUTS

Optional dashboard shortcuts:

/
search

G then S
signals

G then A
automation

G then O
orders

Shortcuts must never interfere with text input.

---

# 6.105 TOUCH

Interactive targets should be comfortably tappable.

Do not rely on hover for essential information.

Hover-only tooltips need mobile alternatives.

---

# 6.106 PWA INSTALL EXPERIENCE

Do not aggressively force installation.

Show install prompt only after user demonstrates engagement.

Explain:
“Install for faster access and an app-like experience.”

Do not claim native capabilities that the PWA does not possess.

---

# 6.107 SERVICE WORKER

Cache:
static shell
safe assets
public content according to policy

Network-first for:
dynamic financial data

Never cache sensitive API responses indiscriminately.

---

# 6.108 DEEP LINKS

Support:
email links
notification links
blog links
subscription links
support links

Authentication required:
redirect to login
preserve safe destination
validate destination
prevent open redirects

---

# 6.109 UNIVERSAL / APP LINKS

Configure:
Android App Links
Apple Universal Links

Only verified domains.

Do not accept arbitrary redirect URLs.

---

# 6.110 DEEP-LINK SECURITY

Validate:
scheme
host
path
parameters

Reject:
javascript:
unknown custom schemes
malformed redirect
untrusted external target

---

# 6.111 ANALYTICS FRONTEND

Track meaningful UX events.

Do not track secrets.

Use consent mechanisms where legally required.

Events must include:
event name
schema version
timestamp
safe anonymous/session identifier

---

# 6.112 FRONTEND ERROR MONITORING

Capture:
route
component
release version
browser
OS
locale
safe request ID

Do not capture:
passwords
API keys
payment data
private messages unless explicitly justified

---

# 6.113 PERFORMANCE OBSERVABILITY

Measure:

LCP
INP
CLS
TTFB
JS errors
route transition
API latency
image load
font load

Use real-user monitoring where lawful.

---

# 6.114 CORE WEB VITALS

Public pages should target strong Core Web Vitals.

Do not optimize synthetic scores at the expense of actual usability.

---

# 6.115 ANIMATION PERFORMANCE

Prefer:
transform
opacity
GPU-friendly techniques

Avoid expensive layout thrashing.

Do not animate:
width
height
top
left
unless necessary and performance-tested.

---

# 6.116 360° PERFORMANCE

Use:
requestAnimationFrame
frame throttling
device-aware quality
preload queue
memory release

If memory pressure occurs:
reduce frame cache.

---

# 6.117 VIDEO

Use video only where it communicates more efficiently than images.

Provide:
poster
captions where spoken content exists
pause
reduced-motion alternative
responsive source

Do not autoplay loud audio.

---

# 6.118 AUDIO

Default:
muted.

Audio is optional.

Never use audio to communicate critical financial information.

---

# 6.119 DARK MODE

Dark mode must be designed independently.

Do not simply invert colors.

Check:
contrast
charts
status colors
shadows
images
glass surfaces
borders

---

# 6.120 LIGHT MODE

Light mode must remain premium and calm.

Avoid:
pure white everywhere
excessive shadows
low contrast gray text

---

# 6.121 HIGH CONTRAST

Ensure status remains distinguishable.

Do not rely on translucent gradients for essential controls.

---

# 6.122 RESPONSIVE TYPOGRAPHY

Use fluid typography carefully.

Prevent:
headline overflow
Persian word clipping
numeric overflow
button wrapping that changes meaning

---

# 6.123 LONG CONTENT

Test:
long Persian words
long English compound words
large numbers
long instrument symbols
long exchange names

Use safe wrapping.

---

# 6.124 LOCALIZATION OF NUMBERS

Define a consistent product policy for:

Persian digits
Latin digits
decimal separator
thousands separator

Financial screens may use Latin digits for technical precision while explanatory Persian content uses the selected locale convention.

Do not mix formats randomly.

---

# 6.125 DATE DISPLAY

Support:
relative time
absolute date
timezone

Example:
“2 min ago”
“27 Aug 2026, 14:32 UTC”

For financial records, always make exact timestamp accessible.

---

# 6.126 CURRENCY DISPLAY

Examples:

€29.00
$29.00
£29.00

Do not display:
“29” without currency context in checkout.

---

# 6.127 SECURITY COPY

Use direct language.

Bad:
“Oops! Something weird happened.”

Good:
“We could not validate this connection. No order was submitted.”

---

# 6.128 RISK COPY

Bad:
“Smart AI protects your money.”

Good:
“Risk controls can restrict or block trades when configured limits are exceeded. They cannot eliminate market loss.”

---

# 6.129 TRUST COPY

Bad:
“100% accurate AI.”

Good:
“AI-assisted analysis is combined with deterministic policy and risk controls.”

---

# 6.130 CTA COPY

Primary CTAs should describe action:

Start Subscription
Connect Account
View Signals
Configure Automation
Review Risk
Read Documentation

Avoid:
WIN NOW
MAKE MONEY
GET RICH

---

# 6.131 ONBOARDING PROGRESS

Show:

1 Account
2 Security
3 Product
4 Subscription
5 Connection
6 Ready

Allow returning users to resume safely.

---

# 6.132 ONBOARDING INTERRUPTION

If user leaves:

save safe non-sensitive progress.

Never save secrets in local storage.

On return:
verify current backend state.

---

# 6.133 CONFIRMATION UX

For destructive or financial actions:

state consequence
show object
require deliberate confirmation

Example:
“Pause automated trading?”

Then:
“New automated orders will be blocked. Existing positions are not automatically closed.”

---

# 6.134 DOUBLE SUBMISSION

Prevent through:
disabled state
idempotency
server-side duplicate detection

Never rely only on disabled button.

---

# 6.135 NETWORK INTERRUPTION

If mutation response is lost:

do not automatically repeat financial mutation.

Show:
“Request status is being verified.”

Then query backend/provider reconciliation.

---

# 6.136 FRONTEND STATE MANAGEMENT

Separate:

server state
UI state
form state
session state
local preferences

Do not put the entire backend database into global client state.

Sensitive state should have minimal client lifetime.

---

# 6.137 DATA FETCHING

Use:
request cancellation
stale-while-revalidate where safe
pagination
cursor pagination for large activity
optimistic updates only for non-financial UI

Do not optimistically show a financial order as successful.

---

# 6.138 LIVE UPDATES

Use:
WebSocket
Server-Sent Events
push notifications

where justified.

For trading state:
server remains source of truth.

Reconnect safely.

---

# 6.139 LIVE CONNECTION STATUS

Display:
LIVE
CONNECTING
RECONNECTING
STALE
OFFLINE

Do not show LIVE merely because a socket object exists.

---

# 6.140 SECURITY OF CLIENT CONFIG

Public client configuration may include:
public API endpoint
analytics ID
public feature flags

Never include:
secret keys
database credentials
private signing keys
admin credentials

---

# 6.141 ENVIRONMENT CONFIG

Development:
debugging allowed

Staging:
production-like security

Production:
debug disabled
source maps restricted according to policy
secure headers
strict CSP

---

# 6.142 CONTENT SECURITY POLICY

Implement a strict CSP appropriate to the final architecture.

Minimize:
unsafe-inline
unsafe-eval

Use nonces/hashes where needed.

Audit third-party scripts.

---

# 6.143 SECURITY HEADERS

Consider:
HSTS
CSP
X-Content-Type-Options
Referrer-Policy
Permissions-Policy
frame protections

Configure according to current browser/platform requirements.

---

# 6.144 THIRD-PARTY SCRIPT GOVERNANCE

Every third-party script requires:

owner
purpose
data collected
security review
performance impact
legal/privacy review
removal plan

Do not add dozens of marketing trackers.

---

# 6.145 COOKIE POLICY

Categorize:
essential
analytics
marketing

Implement according to applicable law.

Do not place non-essential cookies before required consent.

---

# 6.146 ACCESSIBLE COOKIE CONSENT

Consent UI:
keyboard accessible
clear
not manipulative
same prominence for accept/reject where required

---

# 6.147 SUPPORT CHAT

If integrated:
clearly identify AI vs human.

AI support must not:
execute trades
change risk limits
change payment details
reveal secrets

unless a separately authorized workflow exists and all actions are controlled.

---

# 6.148 AI CUSTOMER SUPPORT

Support AI may:
explain
navigate
summarize
troubleshoot

Support AI must not invent:
refund status
license status
trade status
payment confirmation
execution result

For authoritative status, query backend.

---

# 6.149 NOTIFICATION UI

Notification center:
unread
read
security
trading
system
billing

Critical alerts stay visible until acknowledged where appropriate.

---

# 6.150 MOBILE PUSH

Push payload should contain minimal sensitive data.

Prefer:
“You have a new security notification.”

Then open authenticated application.

Do not send exchange credentials or detailed financial data in push payload.

---

# 6.151 APP ICON

Create:
1024px master
Android adaptive icon
iOS icon set
PWA icon set
favicon set

Maintain brand consistency.

---

# 6.152 SPLASH SCREEN

Minimal.

Do not create a 10-second cinematic splash.

Use platform-native launch behavior where possible.

---

# 6.153 MOBILE NAVIGATION

Do not hide:
security
subscription
support
pause automation

These should remain reachable.

---

# 6.154 DESKTOP WINDOW STATES

Test:
small window
large window
multiple monitors
high DPI
zoom
keyboard navigation

Do not assume full-screen.

---

# 6.155 ADMIN MOBILE SAFETY

For high-impact actions:
require confirmation
show exact consequence
optionally require biometric confirmation

Example:
“Pause strategy version 12 for all eligible accounts?”

Display scope clearly.

---

# 6.156 BULK ACTIONS

Bulk operations require:
scope
count
preview
confirmation
authorization
audit

Never provide “select all users → delete” to ordinary administrators.

---

# 6.157 ADMIN SEARCH SAFETY

Search results are permission-filtered.

Autocomplete must not reveal unauthorized customer names.

---

# 6.158 ROLE BADGES

Admin role labels:

Support
Operations
Content
Finance
Risk
Security
Super Admin

Never assume “admin” means everything.

---

# 6.159 ROLE-BASED UI

Hide controls the role cannot use.

But remember:
hiding a button is not authorization.

Backend must enforce.

---

# 6.160 ADMIN AUDIT UX

Show:
before
after
actor
reason
approval
timestamp

For secrets:
show only metadata.

---

# 6.161 DESIGN QA CHECKLIST

Every page:

[ ] desktop
[ ] tablet
[ ] mobile
[ ] RTL
[ ] LTR
[ ] dark
[ ] light
[ ] keyboard
[ ] screen reader
[ ] reduced motion
[ ] loading
[ ] empty
[ ] error
[ ] offline
[ ] stale
[ ] permission denied
[ ] long content
[ ] large numbers

---

# 6.162 PAGE COMPLETENESS MATRIX

For every route define:

purpose
audience
primary action
secondary actions
data dependencies
permissions
SEO
analytics
loading
empty
error
offline
stale
RTL
LTR
responsive
accessibility
motion
security

No route is “complete” until all states exist.

---

# 6.163 FRONTEND DEFINITION OF DONE

A frontend feature is complete only when:

design approved
copy approved
English reviewed
Persian reviewed
RTL reviewed
responsive reviewed
accessibility reviewed
security reviewed
analytics reviewed
performance reviewed
error states implemented
loading states implemented
API contract tested
feature flag configured
documentation updated

---

# 6.164 FINAL FRONTEND PRINCIPLE

The platform should feel like:

A serious technology company building disciplined financial infrastructure.

It should NOT feel like:

A casino
A get-rich-quick scheme
A fake AI miracle
A generic crypto dashboard
A copied Apple website

Premium means:
clarity
craft
speed
restraint
confidence
honesty

END OF PART 6
