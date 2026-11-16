# ADR-005: Payment Abstraction Layer

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

AutoLanka needs to support multiple payment methods in Sri Lanka:
- Cash on completion (primary for v1)
- Card payments (Visa/Mastercard via local gateway)
- Local wallets (e.g., Genie, FriMi, eZ Cash — phase 2)
- Commission calculation and provider payout ledger

Must not couple business logic to any single gateway.

## Decision

Implement a **payment abstraction layer** with a provider interface:

```typescript
interface PaymentProvider {
  id: string;                    // 'cash' | 'stripe' | 'genie' | ...
  name: string;
  supportsRefund: boolean;
  createPaymentIntent(input: PaymentInput): Promise<PaymentIntent>;
  confirmPayment(intentId: string, details: any): Promise<PaymentResult>;
  calculateFee(amount: number): number;
  webhookHandler(payload: any, sig: string): PaymentWebhookResult;
}
```

### V1 providers

| Provider | Implementation | Status |
|---|---|---|
| `cash` | No-op; marks job payable on completion | v1 |
| `card` | Stripe-compatible interface (local gateway later) | v1 stub |
| `wallet` | Interface only; returns `NOT_IMPLEMENTED` | phase 2 |

### Commission & payout

- **Commission**: Configurable % per service category (admin-editable)
- **Ledger**: `Payout` entries created on job completion; batched weekly
- **Reconciliation**: Daily job vs. payout report; admin dashboard

## Consequences

**Positive:**
- Swap gateways without touching job/booking logic
- Cash-first matches Sri Lankan market reality
- Stripe-compatible interface = easy local gateway swap
- Ledger provides audit trail for disputes

**Negative:**
- Abstraction adds indirection (mitigated by clear interface)
- Local gateway integration still needed for real card payments
- Wallet providers have different APIs (unified in phase 2)

## Alternatives considered

| Option | Rejected because |
|---|---|
| Direct Stripe integration | Locks to Stripe; Sri Lanka needs local gateway |
| Single provider with config | Can't handle cash + card + wallet cleanly |
| Outsource to payment orchestrator | Cost; overkill for v1 scope |

## References

- Stripe PaymentIntents API (reference interface)
- Sri Lanka payment gateway landscape (Genie, FriMi, eZ Cash, PayHere)
- PCI DSS SAQ-A for card handling (delegate to gateway)