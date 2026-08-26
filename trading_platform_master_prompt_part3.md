# MASTER PROMPT — PART 3
## Complete UX/UI System, Screen-by-Screen Product Specification, Design Tokens, Motion System, RTL/LTR Localization, Responsive Behavior, Accessibility, and Frontend Implementation Contract

**Document role:** Continuation of MASTER PROMPT — PART 1 and PART 2.
**Verification date:** 2026-08-27.
**Primary purpose:** Convert the product vision into an execution-grade design and frontend specification that can be handed to Figma designers, UX researchers, frontend engineers, motion designers, content designers, QA engineers, and accessibility reviewers without relying on undocumented assumptions.

This document defines the product interface, interaction language, visual system, information architecture, responsive rules, motion principles, localization contract, and screen-level acceptance criteria. It does not replace legal/compliance review, exchange documentation, app-store policy review, or financial-risk disclosure review.

**Non-negotiable product principle:** luxury must never be confused with visual excess. The platform should feel expensive because it is calm, precise, coherent, responsive, and intentional—not because every element is animated at once.

# 1. MASTER FRONTEND DIRECTIVE

Build a premium global financial technology experience with the visual confidence of a top-tier technology brand, while avoiding imitation of Apple or any other proprietary visual identity. The reference quality bar is: exceptional typography, disciplined spacing, restrained materials, cinematic product storytelling, smooth transitions, high perceived performance, excellent accessibility, and a feeling that every interaction has been designed.

The public website must be SEO-first, fast, crawlable, resilient, and persuasive. The authenticated web/PWA must feel like a professional financial terminal without becoming cluttered. The owner-only administrative application must prioritize control, auditability, security, and operational speed over marketing aesthetics.

The design system must support:

- English LTR and Persian RTL as first-class modes.
- Four Persian font families and four matching English/Latin font families, with one Persian family being Vazirmatn/Vazir-class and with documented fallback behavior.
- User-selectable text alignment where semantically appropriate: left, center, right; directional defaults remain language-aware.
- Two circular language controls represented by Iran and UK flags, with a clear accessible text alternative; flags are language shortcuts, not claims about nationality.
- Light and dark themes, with dark as the premium default only where it preserves readability and product trust.
- Desktop, tablet, mobile, foldable, and large-monitor layouts.
- Reduced-motion mode and low-power mode.
- Keyboard, switch-control, screen-reader, touch, mouse, and trackpad interactions.
- High-density financial data without sacrificing hierarchy.
- Progressive enhancement: core content and actions remain usable if advanced animation or WebGL features fail.

Do not let the visual layer become the business logic. Every client-side component must consume server-authoritative state and must gracefully represent loading, stale, unavailable, restricted, pending, error, and disabled states.

# 2. EXPERIENCE PILLARS

## 2.1 Trust before excitement
The interface must never imply guaranteed profit, certainty, risk-free trading, or superior returns. Visual treatment must distinguish product quality from financial outcome. A beautifully designed signal card cannot visually imply that the signal is certain.

## 2.2 Calm authority
Use deliberate whitespace, typography, hierarchy, and micro-motion. Avoid casino aesthetics, flashing profit counters, green/red celebration loops, or emotionally manipulative urgency.

## 2.3 Progressive disclosure
Expose the minimum information needed for the current decision first. Advanced detail remains one layer away through expandable panels, technical drawers, or dedicated detail screens.

## 2.4 Explain before asking
Every consequential selection—market type, execution mode, subscription duration, risk profile, connection permission—must have a human explanation before confirmation.

## 2.5 State is visible
A user should be able to understand what the platform is doing, what has happened, what is waiting, and what they can do next without guessing.

## 2.6 Motion communicates structure
Animation is used to establish spatial relationships, reveal hierarchy, indicate progress, and preserve context. Motion must not exist merely to look impressive.

## 2.7 Web-first discoverability, app-first operations
The marketing website and blog should be indexable and shareable. Authenticated operations can be app-like, but their critical information must remain accessible and linkable within the security model.

# 3. INFORMATION ARCHITECTURE

Top-level public navigation:

1. Home
2. Products
3. Automated Trading
4. Signals
5. Supported Markets
6. How It Works
7. Pricing
8. AI / Decision Architecture
9. Risk & Transparency
10. Academy / Blog
11. About
12. Contact
13. Sign In
14. Get Started

Authenticated user navigation:

1. Overview
2. Trading
3. Signals
4. Connections
5. Subscriptions
6. Orders & Billing
7. Activity / Journal
8. Risk Controls
9. Notifications
10. Profile & Security
11. Help Center

Owner/admin navigation:

1. Command Center
2. Users
3. Subscriptions
4. Orders & Payments
5. Trading Integrations
6. Signals
7. Strategy Registry
8. AI Agents
9. Risk Engine
10. Content / Blog
11. Remote Configuration
12. Feature Flags
13. Regions & Store Policies
14. Support
15. Security Events
16. Audit Logs
17. System Health
18. Data Exports
19. Staff & Permissions
20. Emergency Controls

Navigation must be generated from server-side permission claims and product configuration. Do not hard-code administrative visibility in a mobile client.

# 4. DESIGN SYSTEM FOUNDATIONS

## 4.1 Color architecture
Use semantic color tokens rather than raw hex values in components. Define at least:

- `background.canvas`
- `background.surface`
- `background.elevated`
- `background.inverse`
- `text.primary`
- `text.secondary`
- `text.tertiary`
- `text.inverse`
- `border.subtle`
- `border.strong`
- `accent.primary`
- `accent.secondary`
- `status.success`
- `status.warning`
- `status.error`
- `status.info`
- `market.bullish`
- `market.bearish`
- `market.neutral`
- `focus.ring`
- `overlay.scrim`

Do not use green and red as the sole differentiators for financial states. Add icons, labels, patterns, or explicit text. In Persian interfaces, ensure numerals, punctuation, and signs remain visually legible in mixed-direction strings.

The palette image delivered with Part 1 is a starting art direction, not a license to hard-code colors. The final tokens must be validated for contrast and calibrated independently for light/dark modes.

## 4.2 Typography
Provide four Persian families and four Latin families, with role-specific weights. One Persian family must be Vazirmatn/Vazir-class. Each family must have documented licensing, subset strategy, loading strategy, and fallback order.

Suggested semantic roles:

- Display XL: hero headlines
- Display L: section headlines
- Heading 1–6
- Body L
- Body M
- Body S
- Label M
- Label S
- Numeric Display
- Financial Numeric
- Code / API / Key Identifier

Financial numbers must use tabular numerals where supported. Do not let font fallback change alignment between digits.

## 4.3 Spacing
Adopt a consistent base spacing scale and expose aliases rather than arbitrary pixel values. Recommended conceptual tiers: 2, 4, 6, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96, 128. Final values can be tuned through visual QA.

## 4.4 Shape
Use a restrained radius scale. Marketing cards can have larger radii than dense terminal surfaces. Do not combine excessive roundedness with glass effects, thick borders, heavy shadows, and gradients simultaneously.

## 4.5 Elevation
Use elevation primarily to separate layers. Avoid decorative shadows. Define surface levels rather than component-specific shadow recipes.

## 4.6 Iconography
Icons must share a consistent visual grammar. Use filled or outlined families intentionally. Avoid mixing multiple icon vendors in a single context.

# 5. MOTION SYSTEM

The platform requires cinematic scroll-driven storytelling, including object rotation, progressive reveals, and synchronized text movement. Implement this with a layered motion system rather than one global animation framework.

Modern web platforms provide scroll-driven animation capabilities and View Transition APIs; use progressive enhancement so unsupported environments retain a coherent static presentation. The View Transition API can animate navigation and element changes, while CSS scroll-driven animation links keyframes to scroll progress. These capabilities should be feature-detected rather than assumed. citeturn655264search0turn655264search2turn655264search5

## 5.1 Motion tiers

Tier 0 — Essential: opacity, focus, loading indicators, pressed states.
Tier 1 — Structural: page transitions, modal entrance, accordion expansion.
Tier 2 — Storytelling: scroll-linked product object rotation, text reveals, pinned scenes.
Tier 3 — Premium effects: depth parallax, subtle particle fields, shader/WebGL effects.

Tier 3 must always have a graceful fallback.

## 5.2 Duration principles

- Micro-interactions: approximately 120–220 ms.
- Small component transitions: approximately 180–320 ms.
- Page/state transitions: approximately 250–500 ms.
- Storytelling transitions: timeline-controlled rather than fixed-time where possible.

These values are starting points; validate perceptually on real devices.

## 5.3 Easing
Prefer soft acceleration/deceleration and avoid constant-rate movement for interface transitions. Scroll-linked animations should not create noticeable lag between gesture and visual response.

## 5.4 Reduced motion
Honor `prefers-reduced-motion`. In reduced-motion mode:

- replace large translations with fades or immediate state changes;
- disable auto-playing decorative video unless essential;
- stop perpetual background animation;
- avoid camera-like zooms and aggressive parallax;
- preserve information conveyed by motion using labels and static visual states.

## 5.5 Premium scroll scene
For a 360-degree product visual:

1. Reserve a stable visual stage.
2. Pin or semi-pin the stage while content progresses.
3. Map scroll progress to a frame index or transform angle.
4. Synchronize the text block to semantic milestones, not arbitrary percentages.
5. Preload only the necessary frame range.
6. Provide a progress indicator where users could otherwise become uncertain.
7. Allow reduced-motion users to swipe through discrete stills or see a representative hero image.
8. Never block page interaction while frames load.

## 5.6 Page transitions
Where supported, use View Transitions selectively for navigational continuity. The API is designed to preserve visual context across same-document and supported cross-document transitions. citeturn655264search0turn655264search2

Do not animate navigation if the transition causes focus confusion, delays critical interaction, or increases time-to-content.

# 6. GLOBAL HEADER SPECIFICATION

Desktop header:

- Logo left in LTR and right in RTL.
- Primary navigation centered or adjacent to logo depending on width.
- Language control pair.
- Theme selector if enabled.
- Sign In.
- Primary CTA.
- Header transforms from transparent/overlay state to a compact surface after scrolling.

The header must remain readable over hero imagery. Use a surface material or contrast treatment when necessary. Apple’s current Human Interface Guidelines describe materials as a way to establish foreground/background hierarchy and emphasize that dynamic materials should not be indiscriminately used in content layers. Use that principle rather than copying Apple’s exact appearance. citeturn655264search3

Mobile header:

- Logo.
- Language button.
- menu button.
- optional account/avatar after authentication.

Do not cram the complete desktop navigation into a mobile drawer. Organize links by task.

Header states:

`default`, `hero-overlay`, `scrolled`, `menu-open`, `authenticated`, `restricted`, `loading`, `offline`, `maintenance`.

# 7. FOOTER SPECIFICATION

Footer must be a deliberate trust layer, not a link dump.

Sections:

Company
- About
- Contact
- Careers when applicable
- Press

Products
- Automated Trading
- Signals
- Supported Markets
- Integrations

Resources
- Blog
- Academy
- Glossary
- FAQ

Legal & Risk
- Terms
- Privacy
- Cookie Policy
- Risk Disclosure
- Trading Disclaimer
- Regional Availability
- Data Processing information

Technical
- System Status
- Security
- API documentation when public

The footer must show current legal document version/date where relevant and the current region/product availability mechanism.

# 8. HOME / LANDING PAGE — SCREEN-BY-SCREEN

## 8.1 Hero
Purpose: communicate the category and premium positioning in under five seconds.

Required elements:
- concise category statement;
- distinctive headline;
- one-sentence explanation;
- primary CTA;
- secondary CTA;
- trust/disclosure microcopy near consequential claims;
- animated product visual.

Forbidden:
- fake live profit counters;
- unverifiable “win rate” numbers;
- “guaranteed” language;
- false scarcity;
- fake user counts;
- simulated exchange badges presented as certifications.

Hero motion should have one clear focal point. The first screen must remain useful if all advanced effects are disabled.

## 8.2 Philosophy section
Introduce the product architecture with three to five concise principles. Use staggered reveal animation on scroll. Text should be understandable by a financially literate non-expert.

## 8.3 Product split
Two primary product paths:

A. Automated Trading
B. Trading Signals

Each path explains who it is for, what it does, what it does not do, and the major risks/requirements.

## 8.4 Market coverage
Visual market selector:

- Crypto
- Forex
- Binary-options-related availability where legally/platform permissible

The user must understand that availability may vary by jurisdiction, platform, provider, and account eligibility.

## 8.5 AI decision architecture
Show the three-agent conceptual system using a simple sequence:

Market intelligence → strategy evaluation → risk/capital evaluation → independent verification → final decision state.

Do not portray AI as infallible or autonomous in a way that suggests guaranteed performance.

## 8.6 How it works
Five steps:

1. Choose product.
2. Choose market/execution mode.
3. Choose subscription duration.
4. Connect supported account or use signals-only mode.
5. Monitor, review, and control.

## 8.7 Product visual laboratory
Large 360-degree visual with pinned content. This is the flagship motion section.

## 8.8 Risk transparency
Explain that automation amplifies execution consistency, not certainty of outcome. Link directly to risk documentation.

## 8.9 Pricing preview
Show duration choices without overwhelming the user. Highlight duration—not artificial “discount pressure”—as the principal distinction when all plans have equal capabilities.

## 8.10 Editorial proof
Display selected educational articles, not fabricated testimonials. Every testimonial must be traceable to a legitimate source/consent record.

## 8.11 Final CTA
High-trust close: “Explore the platform” or equivalent. Avoid “Become profitable now” framing.

# 9. PRODUCTS PAGE

Create a comparison architecture rather than two unrelated marketing cards.

Primary tabs:
- Automated Trading
- Signals

Shared section: “Choose your operating model.”

Automated Trading flow must distinguish:

Crypto:
- Spot
- Perpetual/Futures where supported
- Hybrid / Mix
- Platform-defined advanced profile where genuinely available

Forex:
- MetaTrader-compatible execution path / Expert Advisor where supported
- Broker/account-specific execution modes
- Hybrid or multi-strategy only where technically supported

Binary-options-related product:
- OTC Market (labeled with local-language explanation and “OTC” in parentheses)
- International Markets
- Mixed / Hybrid

However, the UI must dynamically hide or explain unsupported offerings based on jurisdiction, distribution channel, broker/exchange capability, and compliance policy.

For each mode, require:
- plain-language definition;
- technical definition;
- example scenario;
- key risks;
- account requirements;
- permissions requested;
- expected operational behavior;
- limitations.

# 10. PRICING PAGE

All duration plans may use equal capabilities as requested. The product must not imply that a longer subscription increases trading performance.

Duration selector:

- 1 Week
- 1 Month
- 3 Months
- 6 Months
- 1 Year

Display:
- total price;
- effective period;
- renewal state;
- cancellation terms;
- included capability;
- supported product/market combinations;
- tax/payment notes when applicable.

Do not use a fake “most popular” label unless analytics genuinely support it and the label is transparent.

Pricing page must separate:

Product entitlement
from
Payment state
from
Execution eligibility.

A user who has paid but is ineligible for a requested market must not be shown as “fully activated.” The UI should say “Payment confirmed — eligibility/setup required.”

# 11. CHECKOUT / PURCHASE FLOW

## Step 1 — Product
Choose Automated Trading or Signals.

## Step 2 — Market
Choose Crypto, Forex, or an eligible binary-options-related category.

## Step 3 — Execution mode
Contextual choices appear according to product + market.

## Step 4 — Duration
Choose weekly/monthly/quarterly/semiannual/annual duration.

## Step 5 — Review
A single clear review card summarizes:

Product → Market → Execution mode → Duration → Price → Renewal → Required setup → Major risk notice.

## Step 6 — Payment
Use the approved payment mechanism for the environment. Never ask for exchange secrets on the payment screen.

## Step 7 — Activation
After server confirmation, create entitlement and guide setup.

## Step 8 — Connection
For automated trading, ask for the minimum necessary account connection information. Explain permission scope before consent.

## Step 9 — Connection validation
Show:

Connection requested → Credentials protected → Provider verified → Permissions checked → Ready / Action required.

Never describe an account as connected merely because a secret was submitted.

# 12. CONNECTION CENTER

The connection screen is one of the highest-trust surfaces in the product.

Top state:
- provider name;
- connection status;
- last validation time;
- permission scope;
- trading enablement state;
- emergency disable switch.

Credential entry should follow a secret-handling architecture where the secret is transmitted securely, stored only in an appropriate protected system, never rendered back in plaintext, and never logged. The UI should display masked identifiers and permission summaries, not recoverable secrets.

Example permission explanation:

“Read market/account data” — allows the platform to read data needed for monitoring.
“Place trades” — allows execution on your account.
“Withdraw funds” — must be unavailable unless there is an explicitly justified, separately reviewed product requirement; default design should avoid requesting withdrawal permission.

Use a permission matrix rather than a single generic checkbox.

Connection failure states:
- invalid credentials;
- insufficient permissions;
- provider unavailable;
- region unsupported;
- account type unsupported;
- maintenance;
- consent expired;
- risk restriction;
- verification required.

# 13. DASHBOARD

The dashboard must answer five questions immediately:

1. What is my subscription state?
2. What is the system currently doing?
3. Is my connection healthy?
4. What decisions/signals recently occurred?
5. Is there anything I need to do?

Primary dashboard modules:

- Subscription status card.
- Connection health card.
- Trading mode card.
- System state / automation state.
- Recent signals.
- Recent execution events.
- Risk snapshot.
- AI decision summary.
- Notifications.
- Action-required queue.

Avoid vanity metrics. “AI confidence” should never be displayed without defining what it means and clarifying that it is a model/system score rather than probability of profit.

## 13.1 Automation status
Use explicit states:

`OFF`
`READY`
`RUNNING`
`PAUSED`
`BLOCKED`
`DEGRADED`
`ERROR`
`MAINTENANCE`

Each state must have a reason code available in the details view.

# 14. SIGNAL CENTER

A signal card is a decision artifact, not a casino alert.

Required fields:
- instrument;
- market;
- timeframe;
- direction or setup type;
- entry context;
- stop-loss where applicable;
- take-profit levels where applicable;
- signal strength score if used;
- timestamp and age;
- status;
- source architecture summary;
- risk note;
- expiration/validity window.

Use neutral language: “Setup detected”, “Signal active”, “Signal invalidated”, “Signal expired”, “Execution blocked”.

Do not label a signal “WINNER”, “SAFE”, “GUARANTEED”, or similar outcome-promising terms.

Detail drawer should explain the evidence categories without leaking proprietary strategy internals.

# 15. AUTOMATED TRADING CONTROL SURFACE

Provide explicit controls for:

- Enable / disable automation.
- Pause new entries.
- Allow management of existing positions.
- Emergency stop.
- Risk profile.
- Maximum concurrent exposure.
- Allowed markets.
- Strategy profile.
- Trading window if supported.

Separate “pause entries” from “close positions.” They are not the same operation.

Emergency stop must require a confirmation that explains consequences, but the path to safety must remain shorter than the path to normal configuration.

The interface should show the last actor and timestamp for material control changes.

# 16. JOURNAL

Journal view should support both chronological and analytical perspectives.

Chronological mode:

Timestamp | Event | Market | Strategy | Action | Result state | Risk state

Analytical mode:
- strategy activity;
- execution quality;
- signal lifecycle;
- rejected trades;
- risk blocks;
- AI decision disagreements;
- system degradation incidents.

Every event should have an immutable event identifier and a detailed drawer.

Do not retroactively rewrite historical decision records. Corrections should be represented as new audit events.

# 17. AI DECISION EXPERIENCE

Present the three-agent architecture as a decision-support and control system, not as three magical autonomous personalities.

Agent A — Strategy Evaluation

Purpose: evaluate candidate setups against strategy rules and market context.

Agent B — Risk & Capital Evaluation

Purpose: determine whether the proposed exposure is compatible with configured risk constraints and current conditions.

Agent C — Fundamental / Journal / News Intelligence

Purpose: aggregate relevant market information and produce structured context for the strategy evaluation pipeline.

Optional independent final verification layer:

A separate rule-driven verification service can reconcile outputs and reject structurally invalid decisions.

The UI may show:

Strategy: PASS / REJECT / REVIEW
Risk: PASS / BLOCK / REVIEW
Context: SUPPORTIVE / CONFLICTING / UNKNOWN
Final Gate: APPROVED / BLOCKED / EXPIRED

Never show fictional internal reasoning, hidden chain-of-thought, or fabricated explanations. Show concise evidence categories, inputs, system rules, and machine-readable reason codes where appropriate.

# 18. MARKET DETAIL PAGE

For each instrument or market:

- instrument name;
- provider/exchange/broker;
- market state;
- current data timestamp;
- spread/fee metadata where available;
- active positions if authorized;
- active signals;
- risk status;
- news/context status.

Use a compact chart header and keep key actions stable while the chart scrolls or resizes.

# 19. SUBSCRIPTION CENTER

Sections:

Current plan
- product;
- duration;
- status;
- start/end;
- renewal;
- entitlement scope.

Payment history
- invoices/receipts when supported;
- payment state;
- refund/cancellation state.

Change plan
- upgrade;
- extend;
- renew;
- cancel renewal.

Eligibility
- market access;
- connection eligibility;
- regional restrictions.

Never make “subscription active” equal to “trading active.”

# 20. PROFILE & SECURITY

Provide:

- name/username;
- email/contact identifier;
- language;
- appearance;
- notification preferences;
- session list;
- sign-in methods;
- MFA/passkey management where supported;
- security events;
- privacy controls;
- account deletion/export pathways.

Security-sensitive operations require re-authentication or step-up verification when policy demands it.

Do not expose API secrets, recovery codes, or other sensitive credential material after initial secure submission.

# 21. BLOG / ACADEMY UX

The blog is a growth engine, but its visual system must remain credible enough for a financial context.

Article page requirements:

- canonical URL;
- title;
- author identity;
- reviewer identity where appropriate;
- publication date;
- last reviewed date;
- category;
- reading time;
- table of contents;
- share controls;
- sources;
- related articles;
- risk/disclaimer context for market-sensitive content;
- update history where materially revised.

Use schema markup appropriate to actual page type. Do not fabricate author expertise, ratings, publication dates, or structured-data fields.

Content hierarchy:

Education → Market Concepts → Product Education → Strategy Research → Platform News → Regulatory/Industry Updates.

Market-news articles must display “information as of” timestamps when freshness matters.

# 22. CONTACT / SUPPORT UX

Contact page should reduce support friction.

Primary actions:
- general contact;
- technical support;
- billing support;
- connection help;
- report a security issue;
- account access help.

Support tickets must not request users to paste secret API keys or credentials.

Provide a safe diagnostics bundle concept that shares non-secret metadata needed for troubleshooting.

# 23. 404 PAGE

The 404 page must feel intentional and premium.

Composition:

- restrained animated brand mark or visual;
- concise explanation;
- home button;
- search/content discovery;
- support link.

Do not rely on a giant “404” alone. In RTL, numbers may remain LTR where appropriate while surrounding copy is RTL.

# 24. LOADING / SKELETON SYSTEM

Differentiate:

- page loading;
- data loading;
- background refresh;
- connection validation;
- payment processing;
- trading action processing;
- AI evaluation processing.

Never use a generic spinner for long asynchronous operations where progress or stage is knowable.

For trading operations, present explicit lifecycle states:

Request received → validation → risk gate → provider submission → provider acknowledgement → final state / retry required.

The client must not infer success from HTTP request completion alone.

# 25. ERROR SYSTEM

Errors must be actionable and human-readable.

Template:

What happened?
Why it may have happened.
What is safe to do now.
What the system has already done.
Reference ID.

For financial actions, distinguish:

- request rejected before submission;
- submission unknown;
- provider accepted;
- provider rejected;
- status pending.

Never convert an unknown execution outcome into “failed” without provider evidence.

# 26. EMPTY STATES

Every empty state needs a reason and a next action.

Examples:

No connections → “Connect a supported account.”
No signals → “No active signals match your selected filters.”
No journal events → “Activity will appear here after the first supported event.”
No invoices → “No billing records yet.”
No blog results → “Try a broader search.”

Avoid empty states that sound like system failure.

# 27. RTL / LTR CONTRACT

RTL is not a right-aligned skin over an English interface. Build direction-aware layout primitives.

Rules:

- Use logical CSS properties: `margin-inline`, `padding-inline`, `inset-inline`, `border-start-start-radius`, etc.
- Icons that convey direction may mirror; icons whose meaning is culturally stable may not.
- Charts remain mathematically consistent; do not reverse temporal axes unless the chart specification explicitly calls for RTL presentation and users understand it.
- Financial numbers should remain legible in mixed-script text.
- API keys, UUIDs, URLs, email addresses, and code remain LTR/isolate segments.
- User-entered Persian and English text must not reorder punctuation unexpectedly.
- Breadcrumbs, chevrons, and progressive arrows must follow direction.
- Do not translate identifiers, product SKUs, or exchange symbols.

# 28. LANGUAGE SWITCHER

Use two circular controls with Iran and UK flag imagery as the visual affordance requested by the product owner, but include accessible labels such as “فارسی” and “English.”

Behavior:

1. User activates a language control.
2. Interface switches language and direction.
3. Persist preference to profile when authenticated.
4. Persist a safe local preference for anonymous users.
5. Preserve the current route when a translation exists.
6. If a page has no translation, route to the closest valid localized parent and explain the fallback.
7. Never translate a URL slug if doing so breaks canonicalization; localized routing should be deterministic.

Language switching must not silently delete unsaved form data.

# 29. TEXT ALIGNMENT CONTROL

Where the product explicitly exposes alignment controls, support:

Left
Center
Right

The control is a presentation preference, not a substitute for RTL/LTR direction. Direction should be determined by language; alignment may be selected by the user only where it does not damage semantics or accessibility.

Do not expose alignment controls on dense data tables if they would create an inconsistent financial scan pattern.

# 30. ACCESSIBILITY CONTRACT

Target WCAG 2.2 AA as the practical baseline unless a stronger contractual standard is required. Validate actual contrast, keyboard access, focus visibility, semantics, status announcements, and target sizing rather than merely declaring compliance.

Requirements:

- visible keyboard focus;
- semantic headings;
- correct landmark structure;
- accessible names for icon-only buttons;
- error association with fields;
- non-color status differentiation;
- motion alternatives;
- adequate touch targets;
- logical reading order;
- no keyboard traps;
- dialogs that correctly manage focus;
- live-region announcements for meaningful asynchronous state changes;
- charts with textual summaries and accessible data alternatives;
- tables that preserve row/column headers;
- form validation that does not rely exclusively on red borders.

# 31. RESPONSIVE BREAKPOINT STRATEGY

Do not design around device names. Design around layout pressure.

Conceptual ranges:

- compact mobile;
- large mobile;
- tablet portrait;
- tablet landscape;
- compact desktop;
- wide desktop;
- ultra-wide.

At each breakpoint determine:

- navigation mode;
- column count;
- card density;
- typography scale;
- chart controls;
- sticky behavior;
- animation complexity;
- image crop.

On small screens, never preserve a desktop two-column design at the cost of unreadable text or inaccessible controls.

# 32. COMPONENT LIBRARY

Create reusable components at the appropriate semantic level.

Primitive components:
Button, IconButton, Link, Input, Select, Checkbox, Radio, Switch, Tooltip, Badge, Divider, Avatar, Spinner.

Layout:
Stack, Inline, Grid, Container, Section, StickyRegion, Modal, Drawer, Sheet.

Financial:
MarketBadge, Price, PnL, Exposure, RiskMeter, SignalCard, ExecutionState, ConnectionStatus, EntitlementCard, BillingState, AuditEvent.

Editorial:
ArticleCard, AuthorBadge, SourceList, TableOfContents, ReadingProgress.

Navigation:
Header, Sidebar, Breadcrumb, Tabs, MobileNav, LanguageSwitcher.

Do not let domain components own server state. They receive typed props or query state from a feature layer.

# 33. COMPONENT STATE CONTRACT

Every interactive component must be designed in these states where relevant:

Default
Hover
Focus
Pressed
Selected
Disabled
Loading
Success
Warning
Error
Read-only
Unavailable
Restricted
Expired
Offline
Stale

Financial actions also need:

Pending
Submitted
Acknowledged
Unknown
Rejected
Completed
Reversed
Cancelled

Design files must show all material states—not only the happy path.

# 34. DESIGN FILE / FIGMA ORGANIZATION

Recommended pages:

00 Cover
01 Foundations
02 Typography
03 Colors
04 Grid & Spacing
05 Motion
06 Icons
07 Components
08 Patterns
09 Public Website
10 Authenticated Web/PWA
11 Checkout
12 Admin App
13 Responsive
14 RTL
15 Content
16 Accessibility
17 Prototype Flows
18 QA / Dev Handoff

Every component gets:

- name;
- purpose;
- anatomy;
- variants;
- states;
- accessibility notes;
- content constraints;
- responsive behavior;
- implementation notes.

# 35. CONTENT DESIGN RULES

Human-language product explanations are mandatory.

Bad:
“Advanced hybrid adaptive execution architecture.”

Better:
“Use Spot, Futures, or a combined setup. The combined option can use both supported account modes under the risk rules you select.”

Every technical option should answer:

What is it?
Why would I choose it?
What does it require?
What can go wrong?
What does the platform control?
What do I still control?

Do not bury material restrictions in tooltip text.

# 36. TRUST LANGUAGE SYSTEM

Preferred phrasing:

- “Designed to…”
- “The system evaluates…”
- “The platform may block…”
- “Available where supported…”
- “Past performance does not guarantee future results.”
- “Execution depends on provider and market conditions.”
- “AI output is one input into the decision process.”

Avoid:

- “guaranteed profit”;
- “risk-free”;
- “never loses”;
- “100% accurate”;
- “AI knows where the market is going”;
- “hands-free guaranteed trading.”

The visual design should reinforce this linguistic honesty rather than undermine it.

# 37. ADMIN APP UX PRINCIPLES

The owner-only Android app is an operational control plane, not a copy of the consumer app.

Home screen priorities:

1. System health.
2. Active incidents.
3. Payment/activation anomalies.
4. Security alerts.
5. Trading-system status.
6. AI pipeline status.
7. Queue requiring human action.

Global admin controls should include clear blast-radius indicators.

Before applying a configuration change, display:

- affected environments;
- affected regions;
- affected products;
- affected users/segments;
- effective time;
- rollback method.

Never allow a single tap to silently change global trading behavior without a review/confirmation mechanism.

# 38. ADMIN COMMAND PALETTE

Provide fast search across users, orders, subscriptions, incidents, audit events, feature flags, configuration, and content.

Every destructive or high-impact command requires:

- typed confirmation or equivalent step-up interaction where appropriate;
- scope confirmation;
- reason capture;
- actor identity;
- timestamp;
- audit event.

The app must never display API secrets or allow exporting decrypted credentials.

# 39. REMOTE CONFIGURATION UX

Admin can change configurable product behavior, but not all values should be remotely mutable.

Classify configuration as:

Safe Content
UX Configuration
Operational Configuration
Risk Configuration
Security Configuration
Compliance Configuration
Immutable / Deployment-Time Configuration

Risk and security values should require stricter permissions and audit trails than marketing copy.

Remote configuration must use versioning, staged rollout, validation, and rollback.

# 40. FEATURE FLAGS

Every market-sensitive or distribution-sensitive capability should have feature-flag support.

Flag dimensions:

- environment;
- region;
- platform;
- user cohort;
- product;
- subscription tier;
- eligibility state;
- experiment group.

A flag must not be the sole enforcement mechanism for security or payment entitlement. Server-side authorization remains mandatory.

# 41. PERFORMANCE BUDGET

Premium visual quality must not become a slow website.

Establish explicit budgets for:

- initial HTML/document response;
- critical CSS;
- JavaScript required for first interaction;
- hero assets;
- above-the-fold image weight;
- total route weight;
- animation frame rate;
- memory usage on mid-range mobile devices.

Use lazy loading, responsive images, modern formats where appropriate, route-level code splitting, preloading only for critical assets, and progressive hydration/rendering strategies appropriate to the chosen framework.

The 360-degree visual should not force hundreds of full-resolution images into the initial payload. Prefer a frame strategy, sprite/video/sequence format, compressed derivatives, or generated frames according to actual performance testing.

# 42. 360-DEGREE PRODUCT VISUAL IMPLEMENTATION

Create the illusion of a premium physical object rotating through a complete loop.

Asset pipeline:

1. Master model or source images.
2. Clean studio render.
3. Frame extraction.
4. Multiple responsive resolutions.
5. Compression.
6. CDN distribution.
7. Preload strategy.
8. Runtime frame selection.
9. Caching strategy.
10. Fallback still image.

Do not render every frame at maximum resolution. Use device-aware quality tiers.

Desktop:
- high-resolution sequence;
- pinned stage;
- synchronized copy.

Mobile:
- reduced frame count or video sequence;
- simpler parallax;
- reduced memory footprint.

Low-power/reduced-motion:
- static hero plus user-triggered gallery.

# 43. DATA VISUALIZATION SYSTEM

Charts must be designed for decision support, not decoration.

Every chart needs:

- clear units;
- timeframe;
- data timestamp;
- data-source context;
- empty/error/stale state;
- accessible text summary.

Do not place glowing gradients or decorative motion over critical price data if they reduce precision.

Use animation only for entering/update transitions and provide stable endpoints.

# 44. NOTIFICATION CENTER

Categories:

Trading
Signals
Risk
Subscription
Payment
Connection
Security
System
Content

Priority classes:

Critical
High
Normal
Low

Critical security and trading-control notices must not look identical to marketing notifications.

Users should be able to distinguish:

informational update;
action required;
warning;
emergency.

# 45. SEARCH

Global search may cover:

- markets;
- symbols;
- blog articles;
- help content;
- platform settings available to the user.

Search results must respect authorization and region restrictions.

Do not surface hidden administrative entities through a shared search index.

# 46. ONBOARDING

Onboarding should be progressive.

Stage 1:
Account creation.

Stage 2:
Language and preferences.

Stage 3:
Product selection.

Stage 4:
Market/operation selection.

Stage 5:
Risk disclosure acknowledgement.

Stage 6:
Subscription/payment.

Stage 7:
Connection setup if applicable.

Stage 8:
Verification.

Stage 9:
First-use dashboard tutorial.

Do not ask for trading API credentials before the user has a legitimate activated entitlement and understands why the permission is needed.

# 47. CHECKOUT MICROCOPY EXAMPLES

Automated Trading:
“Choose how the platform should connect to your supported market. Your selection changes which setup steps appear next.”

Spot:
“Spot trading uses the underlying asset rather than a leveraged derivative. Availability depends on the connected provider.”

Futures/Perpetuals:
“Derivatives can use leverage and can amplify losses as well as gains. Your provider, account type, and configured limits determine what is available.”

Hybrid:
“Use a supported combination of Spot and derivative execution. The platform applies separate eligibility and risk rules to each.”

Forex:
“Forex execution depends on your broker/account setup and the supported execution bridge. MetaTrader-compatible workflows may use an Expert Advisor or other integration path.”

Signals:
“Signals provide decision-support information. You remain responsible for deciding whether and how to act unless a separately enabled automated workflow is active.”

# 48. VISUAL HIERARCHY FOR RISK

Risk content should not be visually hidden at the bottom of long purchase flows.

Use a dedicated “Risk & Availability” section immediately adjacent to material choices.

Risk indicators should explain severity without theatrical color coding.

Recommended visual hierarchy:

Level 1: legal/financial material disclosure.
Level 2: operational limitation.
Level 3: caution.
Level 4: informational note.

A user should never have to open a tooltip to discover that a selected market is unavailable in their region.

# 49. PLATFORM AVAILABILITY UX

Create a unified availability state:

Available
Limited
Needs verification
Not available in this region
Not available on this platform
Provider unavailable
Temporarily paused

Each restriction should have a reason and an alternative action when possible.

Example:
“Binary-options-related automated execution is unavailable in this distribution channel. You can continue with eligible educational content or supported signal products.”

Do not suggest bypasses, sideloading, VPN manipulation, or policy circumvention.

# 50. APP STORE / WEB CONSISTENCY

The web, PWA, iOS, Android, macOS, and Windows clients should share:

- design tokens;
- copy principles;
- domain concepts;
- state names;
- accessibility labels;
- localization keys;
- analytics events.

They may differ in navigation patterns and platform conventions. Do not force identical UI where native interaction standards differ.

# 51. FRONTEND ARCHITECTURE CONTRACT

Suggested layers:

Presentation
→ UI Components
→ Feature Modules
→ Application Services
→ Domain Adapters
→ API Client
→ Server

Rules:

- API types are generated or centrally typed where possible.
- Domain state does not live in visual components.
- Server errors are normalized into user-safe error categories.
- Sensitive values are never put in analytics payloads.
- Client feature flags are advisory; backend authorization is authoritative.
- All asynchronous actions have cancellation and stale-result handling where relevant.

# 52. ROUTING CONTRACT

Public routes should be stable, localized, and SEO-friendly.

Example conceptual routes:

`/`
`/en/...`
`/fa/...`
`/products`
`/pricing`
`/blog`
`/blog/[slug]`
`/login`
`/signup`
`/app`
`/app/signals`
`/app/trading`
`/app/connections`
`/app/subscription`
`/app/settings`
`/admin/*`

Do not expose internal IDs in URLs when a public slug is safer and more stable.

# 53. AUTHENTICATION TRANSITION UX

Login states:

idle;
submitting;
MFA required;
verification pending;
success;
error;
rate-limited;
locked;
maintenance.

After login, preserve the user’s intended safe destination while respecting entitlement and permission checks.

Logout should be explicit and should revoke or invalidate session state according to backend policy.

# 54. FORM DESIGN SYSTEM

Inputs should show:

- label;
- optional/required status;
- helper text;
- current value;
- validation;
- error;
- success when meaningful;
- secure input mode where appropriate.

Do not use placeholder text as the sole label.

For API key entry, separate key and secret fields visually and explain what permission each credential grants. Avoid showing secrets in URL query strings, logs, analytics, or support diagnostics.

# 55. TABLE DESIGN

Use tables when exact comparison matters.

Rules:

- sticky headers only when they materially help;
- right-align numeric data in LTR and use logical numeric alignment in RTL where appropriate;
- preserve symbol and unit clarity;
- add row detail drawers on mobile;
- use horizontal scroll only as a last resort and provide context;
- do not hide critical status fields solely on mobile.

# 56. MOBILE NAVIGATION

Authenticated mobile navigation should prioritize the most frequent operations.

Suggested bottom navigation:

Overview
Signals
Trading
Activity
More

The exact labels can change after usability testing. Admin app should have its own operational navigation.

# 57. MODALS, DRAWERS, AND SHEETS

Use:

- modal for high-consequence confirmation;
- drawer for contextual detail on desktop;
- bottom sheet for mobile contextual detail;
- full-screen flow for multi-step high-complexity tasks.

Never place critical legal disclosures inside a hover-only tooltip.

# 58. DATA FRESHNESS

Financial information must expose freshness when delay could matter.

Use badges:

LIVE
RECENT
STALE
DELAYED
UNKNOWN

The exact thresholds belong to the backend/provider specification. Frontend should not invent them.

# 59. OFFLINE / DEGRADED MODE

PWA can continue to show cached public content and non-sensitive shell data where appropriate.

Trading actions must not be silently queued while offline unless a separately specified execution queue has explicit safety semantics.

For safety-critical functions, offline mode should default to “view-only” and clearly explain that execution is unavailable.

# 60. ANALYTICS UX EVENTS

Instrument product analytics around user intent and completion, not around sensitive trading credentials or secrets.

Event families:

marketing CTA;
product selection;
pricing selection;
checkout started;
payment outcome;
entitlement activated;
connection attempted;
connection verified;
signal viewed;
automation enabled/paused;
control changed;
help content viewed.

Analytics must minimize personal and sensitive financial information.

# 61. QA ACCEPTANCE MATRIX

Every screen must pass:

Visual QA
Functional QA
Responsive QA
Accessibility QA
RTL QA
Localization QA
Security QA
Performance QA
Error-state QA
Analytics QA
SEO QA where public

Each defect must have severity, reproducibility, environment, route, state, expected behavior, actual behavior, and screenshot/video evidence where safe.

# 62. DESIGN HANDOFF ACCEPTANCE

A screen is not “done” when the happy-path mockup looks beautiful.

Done requires:

- all important states documented;
- content final or clearly marked;
- RTL reviewed;
- responsive behavior documented;
- interaction states documented;
- animation timing documented;
- accessibility labels defined;
- loading/error/empty states defined;
- analytics events named;
- backend data dependencies identified;
- security-sensitive fields identified.

# 63. USER JOURNEY — AUTOMATED TRADING

Journey:

Landing → Product selection → Market selection → Execution mode → Duration → Review → Payment → Entitlement confirmation → Connection setup → Permission review → Validation → Risk profile → Final readiness check → Dashboard.

At each transition, the UI must display a single next action and the current state.

A failed connection must not look like a failed payment.
A paid but inactive account must not look like an expired subscription.
A connected account with automation paused must not look broken.

# 64. USER JOURNEY — SIGNALS

Landing → Signals → Market selection → Duration → Review → Payment → Entitlement → Preferences → Signal Center.

Signals-only users must not be forced to connect a broker/exchange unless a specific downstream feature genuinely requires it.

# 65. USER JOURNEY — SUBSCRIPTION CHANGE

Current subscription → Compare eligible options → Review entitlement impact → Payment/credit handling → Confirmation → New entitlement state.

Never switch a user into a new financial execution mode merely because they changed duration.

Duration changes and product/mode changes are conceptually separate actions.

# 66. ADMIN JOURNEY — USER SUPPORT

Search user → Verify support context → View non-secret account state → View subscription/order/connection status → Diagnose → Apply least-privilege operational action → Capture reason → Audit event → Inform user.

Support staff should not need decrypted API credentials to troubleshoot normal connection problems.

# 67. ADMIN JOURNEY — GLOBAL UI CHANGE

Create draft config → validation → scope preview → reviewer approval when required → scheduled activation or immediate activation → monitoring → rollback option.

The consumer app must receive a configuration version identifier so incidents can be traced to the exact configuration presented to the user.

# 68. VISUAL QA BENCHMARKS

Compare key pages at:

- 320 px width;
- 375 px;
- 430 px;
- 768 px;
- 1024 px;
- 1280 px;
- 1440 px;
- 1920 px;
- ultra-wide test width.

Test with:

- English;
- Persian;
- long names;
- long numeric values;
- missing data;
- large font settings;
- reduced motion;
- slow network simulation.

# 69. COPY INTERNATIONALIZATION CONTRACT

Every user-facing string must use localization keys. No hard-coded English/Persian strings inside reusable components.

Localization records should support:

key;
default English;
Persian translation;
context;
maximum length;
pluralization notes;
numeric formatting notes;
review state.

Avoid concatenating sentences from fragments when grammar can change between languages.

# 70. FINANCIAL NUMBER FORMATTING

Define centralized formatting for:

currency;
percentages;
price;
quantity;
leverage;
margin;
P&L;
fees;
timestamps.

Never use browser locale blindly for financial identifiers. Symbol formatting, decimal precision, and negative-number conventions must be explicit.

Persian UI may use localized digits in ordinary prose if product policy chooses that behavior, but trading symbols, IDs, and machine identifiers should remain unambiguous.

# 71. SECURITY VISUAL CUES

Security-sensitive pages should use consistent but restrained cues:

- verified domain/connection indicator where applicable;
- lock/key icon only when its meaning is clear;
- explicit permission scope;
- recent activity;
- session controls;
- last verification timestamp.

Do not create fake browser-security imagery that users could mistake for a cryptographic guarantee.

# 72. USER FEEDBACK / TOASTS

Toasts are supplemental, not the only place status appears.

For critical financial events, persistent inline status is required.

Example:
“Automation paused at 14:08 UTC. No new entries will be opened until resumed.”

The toast may repeat this briefly, but the dashboard must retain the status.

# 73. DESIGN ANTI-PATTERNS

Reject designs that use:

- dense neon dashboards without hierarchy;
- constantly moving backgrounds;
- flashing P&L;
- fake AI brain animations as evidence of intelligence;
- unexplained “confidence” rings;
- meaningless 3D objects above content;
- fake exchange logos implying partnership;
- countdown timers without a legitimate business reason;
- misleading “live” labels for delayed data;
- hidden limitations behind hover-only interaction;
- inaccessible drag-only controls.

# 74. BRAND / LOGO DIRECTION

The logo should communicate:

precision;
control;
intelligence;
trust;
modernity;
calm strength.

Avoid cliché combinations of candlestick charts + robot head + dollar symbol + shield all in one mark.

Create a scalable symbol that works at 16 px, app-icon sizes, favicon size, and large hero treatment.

Provide:

- primary logo;
- monochrome logo;
- reversed logo;
- icon mark;
- favicon;
- app icon;
- safe-area specification;
- minimum size;
- background control.

The design should be distinctive enough for trademark review rather than assembled from generic financial clip-art.

# 75. IMAGE / VIDEO ART DIRECTION

Photography and renders should use controlled studio lighting, realistic materials, disciplined composition, and ample negative space. Product renders can be highly polished but must correspond to real product UI or clearly be labeled conceptual.

Do not use fabricated screenshots that look like real exchange confirmations or performance histories.

When AI-generated images are used for marketing, maintain a production register of generated assets and review them for visual artifacts, accidental logos, incorrect text, or misleading claims.

# 76. FINAL SCREEN INVENTORY

Public:
Home, Products, Product Detail, Pricing, Checkout, How It Works, AI Architecture, Risk & Transparency, About, Contact, Blog Index, Blog Category, Article, FAQ, Help Center, Privacy, Terms, Risk Disclosure, Availability, Security, Status, 404, Maintenance.

Authenticated:
Login, Signup, Verification, Onboarding, Dashboard, Signals, Signal Detail, Trading, Position Detail, Connection Center, Connect Provider, Connection Detail, Journal, Subscription, Billing, Notifications, Profile, Security, Preferences, Help, Support.

Admin:
Admin Login, Command Center, User List, User Detail, Order List, Order Detail, Subscription Control, Provider Connections, Signal Ops, Strategy Registry, AI Agents, Risk Engine, Content, Feature Flags, Remote Config, Regions, Store Distribution, Security Events, Audit Logs, System Health, Incident Detail, Support Queue, Staff & Roles, Emergency Controls.

# 77. DEFINITION OF DONE FOR PART 3

Part 3 is considered implemented only when:

1. The design system has semantic tokens.
2. English and Persian directions are native, not patched.
3. Every major flow has loading, empty, error, restricted, and success states.
4. The premium motion system has reduced-motion fallbacks.
5. The 360-degree scene has static and low-memory fallbacks.
6. Financial state labels are explicit and not color-only.
7. Subscription, entitlement, eligibility, and execution state remain visually distinct.
8. Connection screens explain permissions and never expose secrets.
9. Store/region restrictions can be represented without broken screens.
10. Admin controls expose scope and auditability.
11. Every public page can be localized without layout collapse.
12. The mobile experience is designed independently from desktop constraints.
13. Performance budgets are measurable.
14. Accessibility acceptance criteria are testable.
15. Components have documented states and implementation contracts.
16. Blog/article pages have clear editorial provenance.
17. Error states preserve transaction ambiguity rather than inventing certainty.
18. All critical actions have explicit confirmations and server-authoritative status.

# 78. IMPLEMENTATION PROMPT FOR THE FRONTEND AGENT

You are the principal product designer + staff frontend architect for this project.

Before writing code:

- read Part 1 and Part 2;
- resolve contradictions in favor of security, truthfulness, accessibility, and server-authoritative behavior;
- identify missing backend data dependencies;
- create a route map;
- create a component inventory;
- create token files;
- create localization keys;
- create an animation budget;
- create a test matrix.

Then implement in this order:

Phase A — tokens and typography
Phase B — shell and navigation
Phase C — public landing system
Phase D — pricing and checkout
Phase E — authentication/onboarding
Phase F — authenticated dashboard
Phase G — signals/trading/connection UX
Phase H — profile/security/billing
Phase I — admin shell
Phase J — admin operational screens
Phase K — blog/editorial
Phase L — RTL/LTR and localization hardening
Phase M — responsive/accessibility/performance hardening
Phase N — visual regression and end-to-end QA

Do not build all pages as static screenshots. Use real state machines, typed models, component variants, and reusable primitives.

# 79. RESEARCH AND VERIFICATION RULE FOR FUTURE PARTS

Any recommendation in this master prompt that depends on a changing external service, SDK, app-store policy, browser capability, exchange API, payment provider, regulatory rule, AI model/version, or publishing requirement must be re-verified against current primary/official sources before implementation.

Current web-platform references support use of scroll-driven animations and View Transition APIs as progressive-enhancement technologies, and Apple’s current design guidance emphasizes materials as a hierarchy tool rather than a generic effect. These references inform the motion/material principles here; they do not require copying any vendor’s visual identity. citeturn655264search3turn655264search5

Never write implementation assumptions as facts when they have not been verified.

# 80. MASTER QUALITY BAR

The final product should make a sophisticated user think:

“This feels precise.”
“This explains what is happening.”
“I know what I am authorizing.”
“I can see what is active.”
“I can stop it.”
“I can understand the limitations.”
“I can find the information I need.”

It should not make a user think:

“This is flashy.”
“This promises easy money.”
“I do not know whether this trade really happened.”
“I cannot tell what the AI actually controls.”
“I cannot find how to disable automation.”
“I have no idea why this feature is unavailable.”

The design objective is not maximal visual spectacle. It is maximal perceived quality produced by clarity, coherence, craft, and responsible interaction design.

END OF MASTER PROMPT — PART 3

## APPENDIX A1: SCREEN QA SCENARIO SET 1

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A2: SCREEN QA SCENARIO SET 2

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A3: SCREEN QA SCENARIO SET 3

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A4: SCREEN QA SCENARIO SET 4

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A5: SCREEN QA SCENARIO SET 5

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A6: SCREEN QA SCENARIO SET 6

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A7: SCREEN QA SCENARIO SET 7

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A8: SCREEN QA SCENARIO SET 8

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A9: SCREEN QA SCENARIO SET 9

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A10: SCREEN QA SCENARIO SET 10

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A11: SCREEN QA SCENARIO SET 11

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A12: SCREEN QA SCENARIO SET 12

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A13: SCREEN QA SCENARIO SET 13

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A14: SCREEN QA SCENARIO SET 14

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A15: SCREEN QA SCENARIO SET 15

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A16: SCREEN QA SCENARIO SET 16

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A17: SCREEN QA SCENARIO SET 17

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A18: SCREEN QA SCENARIO SET 18

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A19: SCREEN QA SCENARIO SET 19

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.


## APPENDIX A20: SCREEN QA SCENARIO SET 20

For this scenario set, validate the current feature in English LTR, Persian RTL, narrow mobile, tablet, desktop, reduced-motion mode, slow network, and error injection mode. Verify that the interface preserves hierarchy, never exposes sensitive values, distinguishes pending from failure, retains user-entered non-sensitive form content, and provides a clear next action. Verify keyboard traversal, visible focus, screen-reader names, text wrapping, number formatting, and the absence of accidental horizontal overflow. Capture visual regression baselines for the key state permutations and record any deviation against the design-system tokens. For financial actions, verify that the UI cannot locally manufacture a success state when the server has not confirmed the operation.
