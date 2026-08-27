# TRADING PLATFORM MASTER PROMPT — PART 16
# COMPLIANCE, JURISDICTION, FINANCIAL PRODUCT GOVERNANCE & LEGAL-READY PRODUCT DESIGN

CONTINUATION OF ALL PREVIOUS PARTS

This document is cumulative and implementation-oriented. It does not override applicable law, security controls, provider contracts or platform policies.

## 16.001 REQUIREMENT

Treat compliance as a product dependency, not a footer.

## 16.002 REQUIREMENT

Create a jurisdiction matrix.

## 16.003 REQUIREMENT

Classify every product as supported, restricted, review-required or unavailable per jurisdiction.

## 16.004 REQUIREMENT

Determine the exact legal characterization of each service with qualified counsel.

## 16.005 REQUIREMENT

Do not assume signals, advice, portfolio management, execution or brokerage are legally equivalent.

## 16.006 REQUIREMENT

Obtain professional legal review before launch in regulated markets.

## 16.007 REQUIREMENT

Maintain license/authorization records where applicable.

## 16.008 REQUIREMENT

Version regulatory assumptions and legal decisions.

## 16.009 REQUIREMENT

Create product-specific risk disclosures.

## 16.010 REQUIREMENT

Create age/eligibility checks where required.

## 16.011 REQUIREMENT

Implement KYC/AML when legally required.

## 16.012 REQUIREMENT

Implement sanctions/blocked-country screening where applicable.

## 16.013 REQUIREMENT

Separate KYC storage from ordinary application data.

## 16.014 REQUIREMENT

Audit KYC access.

## 16.015 REQUIREMENT

Define complaint and escalation procedures.

## 16.016 REQUIREMENT

Maintain legal-document versions.

## 16.017 REQUIREMENT

Record acceptance timestamps and locale.

## 16.018 REQUIREMENT

Never call the product regulated unless that statement is accurate.

## 16.019 REQUIREMENT

Never claim a license that has not been obtained.

## 16.020 REQUIREMENT

Never claim certification without certification.

## 16.021 REQUIREMENT

Never claim partnership without an actual partnership.

## 16.022 REQUIREMENT

Never claim security audit without completed audit evidence.

## 16.023 REQUIREMENT

Create compliance decision records.

## 16.024 REQUIREMENT

Use server-side eligibility checks.

## 16.025 REQUIREMENT

Use server-side platform gating.

## 16.026 REQUIREMENT

Use server-side region gating.

## 16.027 REQUIREMENT

Use provider eligibility rules.

## 16.028 REQUIREMENT

Use conservative behavior when compliance status is unknown.

## 16.029 REQUIREMENT

Do not permit ordinary admins to disable compliance gates.

## 16.030 REQUIREMENT

High-impact policy changes require elevated approval and audit.

## 16.031 REQUIREMENT

For EU crypto services, assess applicable MiCA requirements where relevant.

## 16.032 REQUIREMENT

For CFDs/FOREX/derivatives, assess local licensing and distribution rules.

## 16.033 REQUIREMENT

For binary-options-like products, apply strongest restriction and legal review.

## 16.034 REQUIREMENT

Google Play currently prohibits apps providing binary-options trading.

## 16.035 REQUIREMENT

Apple currently prohibits apps facilitating binary-options trading and requires appropriate licensing for CFDs/FOREX/derivatives.

## 16.036 REQUIREMENT

Never attempt store-review circumvention.

## 16.037 REQUIREMENT

Do not bypass jurisdiction using misleading UI.

## 16.038 REQUIREMENT

IP alone is not sufficient compliance evidence.

## 16.039 REQUIREMENT

Use declared residence and other lawful signals as appropriate.

## 16.040 REQUIREMENT

Define data-processing purposes.

## 16.041 REQUIREMENT

Define retention periods.

## 16.042 REQUIREMENT

Define deletion/anonymization rules.

## 16.043 REQUIREMENT

Define data-subject request workflows where applicable.

## 16.044 REQUIREMENT

Define tax and billing legal responsibilities.

## 16.045 REQUIREMENT

Define consumer cancellation/refund rules.

## 16.046 REQUIREMENT

Create regulatory change monitoring.

## 16.047 REQUIREMENT

Review provider terms before integration.

## 16.048 REQUIREMENT

Review store policies before submission.

## 16.049 REQUIREMENT

Review marketing claims before publication.

## 16.050 REQUIREMENT

Create a legal approval gate for regulated capabilities.

## 16.051 REQUIREMENT

Maintain evidence for every material public compliance statement.

## 16.052 REQUIREMENT

Keep compliance state auditable.

## 16.053 REQUIREMENT

Allow lawful product unavailability without code rewrites.

## 16.054 REQUIREMENT

Do not use disclaimers as a substitute for authorization.

## 16.055 REQUIREMENT

Create compliance runbooks.

## 16.056 REQUIREMENT

Create restricted-country handling.

## 16.057 REQUIREMENT

Create manual-review states.

## 16.058 REQUIREMENT

Create policy expiry/review dates.

## 16.059 REQUIREMENT

Document ambiguous areas instead of guessing.

## 16.060 REQUIREMENT

Never silently convert an unknown legal state to ALLOW.

## 16.061 REQUIREMENT

Compliance requirements override growth and visual preferences.


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


END OF PART 16