# TRADING PLATFORM MASTER PROMPT — PART 14
# MOBILE, DESKTOP, PWA, NATIVE BILLING & APP-STORE DISTRIBUTION ARCHITECTURE

CONTINUATION OF ALL PREVIOUS PARTS

This document is cumulative and implementation-oriented. It does not override applicable law, security controls, provider contracts or platform policies.

## 14.001 REQUIREMENT

Use a mature cross-platform strategy for Android, iOS, Windows and macOS while maintaining an SEO-first web.

## 14.002 REQUIREMENT

Evaluate the current Flutter, React Native and native options against actual requirements before freezing the stack.

## 14.003 REQUIREMENT

Use platform adapters for secure storage, notifications, billing and authentication.

## 14.004 REQUIREMENT

Treat PWA and native apps as clients of a shared backend.

## 14.005 REQUIREMENT

Keep all authorization server-side.

## 14.006 REQUIREMENT

Use OS secure storage for local credentials.

## 14.007 REQUIREMENT

Never embed exchange secrets in client binaries.

## 14.008 REQUIREMENT

Never embed production admin credentials.

## 14.009 REQUIREMENT

Use strict app/universal-link validation.

## 14.010 REQUIREMENT

Prevent open redirects.

## 14.011 REQUIREMENT

Keep push payloads minimal and non-sensitive.

## 14.012 REQUIREMENT

Support remote session revocation.

## 14.013 REQUIREMENT

Use device attestation as an additional signal where useful.

## 14.014 REQUIREMENT

Use separate admin API audience/permissions where appropriate.

## 14.015 REQUIREMENT

Use step-up authentication for destructive admin actions.

## 14.016 REQUIREMENT

Keep admin offline mode read-only for safe content.

## 14.017 REQUIREMENT

No unrestricted financial commands are allowed offline.

## 14.018 REQUIREMENT

Define a supported OS/version matrix.

## 14.019 REQUIREMENT

Use protected signing infrastructure.

## 14.020 REQUIREMENT

Never store mobile signing keys in source control.

## 14.021 REQUIREMENT

Keep production/staging packages separated.

## 14.022 REQUIREMENT

Do not let staging connect to production trading credentials.

## 14.023 REQUIREMENT

Control service-worker scope.

## 14.024 REQUIREMENT

Never cache exchange secrets.

## 14.025 REQUIREMENT

Be conservative caching authenticated financial data.

## 14.026 REQUIREMENT

Create OFFLINE and STALE states.

## 14.027 REQUIREMENT

Native billing must follow current target-store requirements.

## 14.028 REQUIREMENT

Create Android billing adapter.

## 14.029 REQUIREMENT

Create iOS billing adapter.

## 14.030 REQUIREMENT

Verify native purchases server-side where required.

## 14.031 REQUIREMENT

Normalize web, Android and iOS purchases into one entitlement system.

## 14.032 REQUIREMENT

Keep store-specific billing behind adapters.

## 14.033 REQUIREMENT

Keep web payment providers separate from native billing implementations.

## 14.034 REQUIREMENT

Maintain platform-region-product availability matrices.

## 14.035 REQUIREMENT

Use stable internal product IDs.

## 14.036 REQUIREMENT

Do not put translated display names in product IDs.

## 14.037 REQUIREMENT

Support purchase restoration where the store supports it.

## 14.038 REQUIREMENT

Handle renewal, grace, hold, pause and expiration states according to platform behavior.

## 14.039 REQUIREMENT

Review current Google Play Billing APIs before each material Android billing release.

## 14.040 REQUIREMENT

Review current Apple StoreKit and App Review requirements before each material iOS release.

## 14.041 REQUIREMENT

Google Play currently documents secure server-backend verification for subscriptions.

## 14.042 REQUIREMENT

Google Play currently disallows apps that enable binary-options trading.

## 14.043 REQUIREMENT

Apple currently disallows apps facilitating binary-options trading and requires appropriate licensing for CFDs/FOREX/derivatives where applicable.

## 14.044 REQUIREMENT

Never disguise prohibited functionality to pass review.

## 14.045 REQUIREMENT

Never use hidden webviews as a policy-bypass mechanism.

## 14.046 REQUIREMENT

Maintain store submission checklists.

## 14.047 REQUIREMENT

Maintain financial-feature declarations where required.

## 14.048 REQUIREMENT

Maintain privacy/support/terms URLs.

## 14.049 REQUIREMENT

Test native billing in sandbox/test environments.

## 14.050 REQUIREMENT

Never use customer accounts for automated real-money testing.

## 14.051 REQUIREMENT

Use controlled internal accounts for any production smoke tests.

## 14.052 REQUIREMENT

Desktop integrations must document MT5/VPS/bridge dependencies where applicable.

## 14.053 REQUIREMENT

Do not pretend a desktop shell is the trading engine unless it truly is.

## 14.054 REQUIREMENT

Minimize platform permissions.

## 14.055 REQUIREMENT

Use separate configuration by environment.

## 14.056 REQUIREMENT

Keep admin app package and signing identity distinct.

## 14.057 REQUIREMENT

Protect privileged mobile screens against casual local leakage where feasible.

## 14.058 REQUIREMENT

Test deep links against malicious parameters.

## 14.059 REQUIREMENT

Test notification routing.

## 14.060 REQUIREMENT

Test uninstall/reinstall and purchase restoration.

## 14.061 REQUIREMENT

Test account migration between devices.

## 14.062 REQUIREMENT

Test secure logout.

## 14.063 REQUIREMENT

Test compromised device scenarios.

## 14.064 REQUIREMENT

Test revoked-session scenarios.

## 14.065 REQUIREMENT

Test offline behavior.

## 14.066 REQUIREMENT

Test poor network behavior.

## 14.067 REQUIREMENT

Test high-DPI and tablet behavior.

## 14.068 REQUIREMENT

Test mobile accessibility.

## 14.069 REQUIREMENT

Keep app capability matrix synchronized with backend policy.

## 14.070 REQUIREMENT

A native client may expose fewer capabilities than the web client when policy requires.

## 14.071 REQUIREMENT

Never promise unconditional store approval.


## FINAL ACCEPTANCE GATE

[ ] Requirements mapped to implementation tickets
[ ] Security review completed
[ ] Compliance review completed where applicable
[ ] Accessibility reviewed
[ ] RTL/LTR reviewed
[ ] Failure states implemented
[ ] Observability implemented
[ ] Audit requirements implemented
[ ] Documentation updated
[ ] Current official external policies verified
[ ] Production owner assigned


END OF PART 14