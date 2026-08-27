# TRADING PLATFORM MASTER PROMPT — PART 12
# AI / ML INTELLIGENCE, THREE-AGENT ORCHESTRATION & MODEL GOVERNANCE

CONTINUATION OF ALL PREVIOUS PARTS

This document is cumulative and implementation-oriented. It does not override applicable law, security controls, provider contracts or platform policies.

## 12.001 REQUIREMENT

AI is a bounded analytical subsystem and never receives unrestricted production execution authority.

## 12.002 REQUIREMENT

Define three primary agents: Strategy, Risk & Capital, and Fundamental/News Research.

## 12.003 REQUIREMENT

Version every model, prompt policy, tool policy, output schema and ensemble configuration.

## 12.004 REQUIREMENT

Create an AI gateway for routing, timeout, cost, rate-limit and fallback control.

## 12.005 REQUIREMENT

Use strict typed schemas for every agent output.

## 12.006 REQUIREMENT

Allow explicit abstention when evidence is insufficient or contradictory.

## 12.007 REQUIREMENT

Keep financial arithmetic deterministic and outside the language model.

## 12.008 REQUIREMENT

Never send exchange secrets, passwords, private keys or session tokens to AI.

## 12.009 REQUIREMENT

Treat external text as untrusted input and isolate it from system instructions.

## 12.010 REQUIREMENT

Protect against prompt injection across news, documents, webpages and user content.

## 12.011 REQUIREMENT

Use allowlisted read-only tools by default.

## 12.012 REQUIREMENT

Financial mutation tools are unavailable to AI by default.

## 12.013 REQUIREMENT

AI cannot change risk limits or compliance rules.

## 12.014 REQUIREMENT

AI cannot grant entitlements or approve refunds.

## 12.015 REQUIREMENT

AI cannot directly create exchange orders.

## 12.016 REQUIREMENT

Maintain a model registry with DRAFT, TEST, CANARY, ACTIVE, PAUSED and RETIRED states.

## 12.017 REQUIREMENT

Use shadow mode for candidate models before live influence.

## 12.018 REQUIREMENT

Use champion/challenger comparisons.

## 12.019 REQUIREMENT

Use canary rollouts for material model changes.

## 12.020 REQUIREMENT

Create model rollback criteria.

## 12.021 REQUIREMENT

Maintain fixed benchmark datasets.

## 12.022 REQUIREMENT

Include normal, edge, adversarial and degraded-data evaluation cases.

## 12.023 REQUIREMENT

Prevent look-ahead bias and future-data leakage.

## 12.024 REQUIREMENT

Use out-of-sample and walk-forward validation where applicable.

## 12.025 REQUIREMENT

Evaluate across multiple market regimes.

## 12.026 REQUIREMENT

Analyze calibration, not just raw accuracy.

## 12.027 REQUIREMENT

Track false positives and false negatives.

## 12.028 REQUIREMENT

Track abstention behavior.

## 12.029 REQUIREMENT

Track data quality sensitivity.

## 12.030 REQUIREMENT

Track latency, failure, cost and token usage.

## 12.031 REQUIREMENT

Enforce per-user, per-agent, per-strategy and global AI budgets.

## 12.032 REQUIREMENT

Cap AI tool calls and execution time.

## 12.033 REQUIREMENT

Implement AI-provider circuit breakers.

## 12.034 REQUIREMENT

Use approved fallback models only.

## 12.035 REQUIREMENT

If no safe fallback exists, abstain.

## 12.036 REQUIREMENT

Capture source provenance for research outputs.

## 12.037 REQUIREMENT

Separate facts from interpretations and predictions.

## 12.038 REQUIREMENT

Cluster duplicate news so one event is not counted repeatedly.

## 12.039 REQUIREMENT

Apply source-quality weighting.

## 12.040 REQUIREMENT

Detect conflicting evidence.

## 12.041 REQUIREMENT

Expire research and AI analysis using timestamps.

## 12.042 REQUIREMENT

Do not present stale analysis as current.

## 12.043 REQUIREMENT

If 16 analytical components are marketed, implement 16 real components.

## 12.044 REQUIREMENT

Use analytical dimensions such as trend, momentum, volatility, volume, structure, liquidity, order flow, support/resistance, multi-timeframe, regime, correlation, sentiment, fundamentals, news, risk and execution quality.

## 12.045 REQUIREMENT

Calculate consensus and disagreement explicitly.

## 12.046 REQUIREMENT

High consensus cannot override a hard risk rejection.

## 12.047 REQUIREMENT

Cap AI contribution to final signal scoring.

## 12.048 REQUIREMENT

Cap AI influence on capital recommendations.

## 12.049 REQUIREMENT

Use deterministic policy after AI.

## 12.050 REQUIREMENT

Use deterministic risk after policy.

## 12.051 REQUIREMENT

Use execution gates after risk.

## 12.052 REQUIREMENT

Keep a trace from data snapshot to final signal.

## 12.053 REQUIREMENT

Do not store hidden chain-of-thought as a product feature.

## 12.054 REQUIREMENT

Store concise evidence, reason codes and source references.

## 12.055 REQUIREMENT

Validate cited sources before publishing them.

## 12.056 REQUIREMENT

Reject malformed model output.

## 12.057 REQUIREMENT

Reject impossible numeric output.

## 12.058 REQUIREMENT

Reject unsupported instruments and actions.

## 12.059 REQUIREMENT

Use content isolation for uploaded files and web content.

## 12.060 REQUIREMENT

Scan untrusted documents before analysis where required.

## 12.061 REQUIREMENT

Test prompt injection continuously.

## 12.062 REQUIREMENT

Test secret-extraction attempts.

## 12.063 REQUIREMENT

Test tool-abuse attempts.

## 12.064 REQUIREMENT

Test malicious source content.

## 12.065 REQUIREMENT

Test hallucinated citations.

## 12.066 REQUIREMENT

Test hallucinated statistics.

## 12.067 REQUIREMENT

Test conflicting source scenarios.

## 12.068 REQUIREMENT

Test model provider outage.

## 12.069 REQUIREMENT

Test model timeout.

## 12.070 REQUIREMENT

Test schema failure.

## 12.071 REQUIREMENT

Keep model processing location documented.

## 12.072 REQUIREMENT

Review provider data-retention terms before production.

## 12.073 REQUIREMENT

Apply privacy minimization to AI context.

## 12.074 REQUIREMENT

Do not train on customer secrets.

## 12.075 REQUIREMENT

Version evaluation datasets.

## 12.076 REQUIREMENT

Maintain model cards.

## 12.077 REQUIREMENT

Maintain agent system cards.

## 12.078 REQUIREMENT

Create an AI incident runbook.

## 12.079 REQUIREMENT

Create drift monitoring for input features and outputs.

## 12.080 REQUIREMENT

Review every material model/provider change.

## 12.081 REQUIREMENT

Do not self-modify production models without controlled governance.

## 12.082 REQUIREMENT

Online adaptation requires simulation, evaluation, versioning and approval.

## 12.083 REQUIREMENT

Customer-facing language must never imply guaranteed profit or accuracy.

## 12.084 REQUIREMENT

A model confidence score is not automatically a probability of profit.

## 12.085 REQUIREMENT

The final AI safety rule is: uncertainty produces abstention, not invention.


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


END OF PART 12