# TRADING PLATFORM MASTER PROMPT — PART 17
# EXCHANGE, BROKER, MT5, MARKET-DATA & EXECUTION ADAPTER ENGINEERING

CONTINUATION OF ALL PREVIOUS PARTS

This document is cumulative and implementation-oriented. It does not override applicable law, security controls, provider contracts or platform policies.

## 17.001 REQUIREMENT

Use provider adapters so exchange/broker semantics remain isolated.

## 17.002 REQUIREMENT

Each provider declares capabilities.

## 17.003 REQUIREMENT

Canonicalize symbols and instruments.

## 17.004 REQUIREMENT

Distinguish spot from derivative contracts.

## 17.005 REQUIREMENT

Distinguish crypto quantity from forex lots.

## 17.006 REQUIREMENT

Normalize precision and step size.

## 17.007 REQUIREMENT

Normalize minimum and maximum quantities.

## 17.008 REQUIREMENT

Normalize contract size.

## 17.009 REQUIREMENT

Normalize tick size.

## 17.010 REQUIREMENT

Normalize margin model.

## 17.011 REQUIREMENT

Normalize leverage semantics.

## 17.012 REQUIREMENT

Normalize funding/financing costs where applicable.

## 17.013 REQUIREMENT

Normalize market status.

## 17.014 REQUIREMENT

Normalize supported order types.

## 17.015 REQUIREMENT

Normalize reduce-only behavior.

## 17.016 REQUIREMENT

Normalize stop/TP behavior.

## 17.017 REQUIREMENT

Validate provider permissions.

## 17.018 REQUIREMENT

Default away from withdrawal permissions.

## 17.019 REQUIREMENT

Prefer scoped trade-only credentials.

## 17.020 REQUIREMENT

Recommend provider-side IP allowlisting where available.

## 17.021 REQUIREMENT

Keep secrets in the secret-management boundary.

## 17.022 REQUIREMENT

Never reveal raw provider secrets to admins/support.

## 17.023 REQUIREMENT

Implement connect, validate, health, balances, positions, orders, fills, createOrder, cancelOrder and reconcile contracts.

## 17.024 REQUIREMENT

Use clientOrderId/idempotency where provider supports it.

## 17.025 REQUIREMENT

Persist intent before provider mutation when required.

## 17.026 REQUIREMENT

If a provider times out after submission, enter UNKNOWN.

## 17.027 REQUIREMENT

Never blindly retry UNKNOWN orders.

## 17.028 REQUIREMENT

Reconcile before creating another order.

## 17.029 REQUIREMENT

Handle partial fills.

## 17.030 REQUIREMENT

Record provider fill IDs.

## 17.031 REQUIREMENT

Record provider timestamps.

## 17.032 REQUIREMENT

Record fee and fee currency.

## 17.033 REQUIREMENT

Handle out-of-order provider events.

## 17.034 REQUIREMENT

Use periodic reconciliation in addition to streams.

## 17.035 REQUIREMENT

Treat streams as update mechanisms, not the sole truth.

## 17.036 REQUIREMENT

Compare internal and provider balances.

## 17.037 REQUIREMENT

Compare positions.

## 17.038 REQUIREMENT

Compare orders.

## 17.039 REQUIREMENT

Compare fills.

## 17.040 REQUIREMENT

Never fabricate provider state.

## 17.041 REQUIREMENT

Never delete provider evidence to make systems agree.

## 17.042 REQUIREMENT

Use compensating corrections for history.

## 17.043 REQUIREMENT

For MT5, use broker/account/symbol/lot/point/tick/margin terminology.

## 17.044 REQUIREMENT

Document EA/bridge/VPS dependencies.

## 17.045 REQUIREMENT

Do not store broker passwords in frontend code.

## 17.046 REQUIREMENT

Use official or provider-supported integration methods.

## 17.047 REQUIREMENT

Create sandbox fixtures.

## 17.048 REQUIREMENT

Test invalid credentials.

## 17.049 REQUIREMENT

Test permission mismatch.

## 17.050 REQUIREMENT

Test market closed.

## 17.051 REQUIREMENT

Test insufficient margin.

## 17.052 REQUIREMENT

Test invalid precision.

## 17.053 REQUIREMENT

Test minimum quantity.

## 17.054 REQUIREMENT

Test maximum quantity.

## 17.055 REQUIREMENT

Test timeout.

## 17.056 REQUIREMENT

Test duplicate event.

## 17.057 REQUIREMENT

Test out-of-order event.

## 17.058 REQUIREMENT

Test partial fill.

## 17.059 REQUIREMENT

Test cancel race.

## 17.060 REQUIREMENT

Test provider outage.

## 17.061 REQUIREMENT

Test rate-limit behavior.

## 17.062 REQUIREMENT

Test stale market data.

## 17.063 REQUIREMENT

Test symbol mapping failure.

## 17.064 REQUIREMENT

Monitor adapter latency.

## 17.065 REQUIREMENT

Monitor adapter error rate.

## 17.066 REQUIREMENT

Monitor provider health.

## 17.067 REQUIREMENT

Monitor provider API deprecations.

## 17.068 REQUIREMENT

Maintain a provider registry.

## 17.069 REQUIREMENT

Review adapter changes against current official provider documentation.

## 17.070 REQUIREMENT

Keep provider-specific semantics available for operations.

## 17.071 REQUIREMENT

Keep core risk logic independent of any one provider.

## 17.072 REQUIREMENT

Provider uncertainty must produce safe degradation.


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


END OF PART 17