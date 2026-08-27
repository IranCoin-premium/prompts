# TRADING PLATFORM MASTER PROMPT — PART 18
# QUALITY ENGINEERING, TEST AUTOMATION, SECURITY TESTING, PERFORMANCE, CHAOS & RELEASE GATES

CONTINUATION OF ALL PREVIOUS PARTS

This document is cumulative and implementation-oriented. It does not override applicable law, security controls, provider contracts or platform policies.

## 18.001 REQUIREMENT

Create a test pyramid spanning unit, integration, contract, end-to-end, security, accessibility and performance.

## 18.002 REQUIREMENT

Test happy paths and failure paths.

## 18.003 REQUIREMENT

Create explicit state-machine tests.

## 18.004 REQUIREMENT

Create API authorization tests.

## 18.005 REQUIREMENT

Create object-level authorization tests.

## 18.006 REQUIREMENT

Create idempotency tests for financial mutations.

## 18.007 REQUIREMENT

Create webhook replay tests.

## 18.008 REQUIREMENT

Create payment race-condition tests.

## 18.009 REQUIREMENT

Create entitlement race-condition tests.

## 18.010 REQUIREMENT

Create order timeout tests.

## 18.011 REQUIREMENT

Create unknown-order tests.

## 18.012 REQUIREMENT

Create reconciliation tests.

## 18.013 REQUIREMENT

Test decimal arithmetic.

## 18.014 REQUIREMENT

Test position sizing invariants.

## 18.015 REQUIREMENT

Test risk limits.

## 18.016 REQUIREMENT

Test provider precision.

## 18.017 REQUIREMENT

Test provider rate limits.

## 18.018 REQUIREMENT

Test provider outages.

## 18.019 REQUIREMENT

Test stale market data.

## 18.020 REQUIREMENT

Test AI schema failures.

## 18.021 REQUIREMENT

Test prompt injection.

## 18.022 REQUIREMENT

Test secret isolation.

## 18.023 REQUIREMENT

Test admin cannot reveal secrets.

## 18.024 REQUIREMENT

Test support cannot reveal secrets.

## 18.025 REQUIREMENT

Test analytics cannot receive secrets.

## 18.026 REQUIREMENT

Test client cannot forge entitlement.

## 18.027 REQUIREMENT

Test client cannot forge pricing.

## 18.028 REQUIREMENT

Test client cannot forge risk approval.

## 18.029 REQUIREMENT

Test platform/region gating.

## 18.030 REQUIREMENT

Test binary-options restrictions where required.

## 18.031 REQUIREMENT

Test CSRF where applicable.

## 18.032 REQUIREMENT

Test XSS.

## 18.033 REQUIREMENT

Test SSRF.

## 18.034 REQUIREMENT

Test injection vulnerabilities.

## 18.035 REQUIREMENT

Test file upload security.

## 18.036 REQUIREMENT

Test open redirects.

## 18.037 REQUIREMENT

Test deep-link security.

## 18.038 REQUIREMENT

Test session revocation.

## 18.039 REQUIREMENT

Test MFA/passkeys.

## 18.040 REQUIREMENT

Test role escalation.

## 18.041 REQUIREMENT

Test privilege separation.

## 18.042 REQUIREMENT

Test localization.

## 18.043 REQUIREMENT

Test RTL/LTR.

## 18.044 REQUIREMENT

Test reduced motion.

## 18.045 REQUIREMENT

Test screen-reader basics on critical flows.

## 18.046 REQUIREMENT

Test responsive layouts.

## 18.047 REQUIREMENT

Test offline mode.

## 18.048 REQUIREMENT

Test stale state.

## 18.049 REQUIREMENT

Test Core Web Vitals.

## 18.050 REQUIREMENT

Load-test public endpoints.

## 18.051 REQUIREMENT

Load-test authenticated APIs.

## 18.052 REQUIREMENT

Load-test signal services.

## 18.053 REQUIREMENT

Load-test notifications.

## 18.054 REQUIREMENT

Use provider sandboxes for integration load tests.

## 18.055 REQUIREMENT

Do not conduct uncontrolled live-money load tests.

## 18.056 REQUIREMENT

Run chaos tests for provider failure.

## 18.057 REQUIREMENT

Run chaos tests for database failure.

## 18.058 REQUIREMENT

Run chaos tests for queue failure.

## 18.059 REQUIREMENT

Run chaos tests for AI outage.

## 18.060 REQUIREMENT

Run deployment failure tests.

## 18.061 REQUIREMENT

Run backup restore tests.

## 18.062 REQUIREMENT

Run disaster-recovery drills.

## 18.063 REQUIREMENT

Run dependency vulnerability scans.

## 18.064 REQUIREMENT

Run secret scans.

## 18.065 REQUIREMENT

Run SAST.

## 18.066 REQUIREMENT

Run DAST.

## 18.067 REQUIREMENT

Generate SBOM.

## 18.068 REQUIREMENT

Verify artifact integrity.

## 18.069 REQUIREMENT

Block critical security findings.

## 18.070 REQUIREMENT

Use canary releases for high-risk changes.

## 18.071 REQUIREMENT

Verify post-deploy business metrics.

## 18.072 REQUIREMENT

Maintain rollback procedures.

## 18.073 REQUIREMENT

Create release evidence.

## 18.074 REQUIREMENT

No release is complete until monitoring and rollback have been validated.


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


END OF PART 18