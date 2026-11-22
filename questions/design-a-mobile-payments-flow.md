# Design a Mobile Payments Flow

In this question the user sends money to another person or pays for an order from the app. The rule that shapes everything is that a payment must happen exactly once, even though mobile requests can time out, repeat, or lose their response. This is the one question where optimistic updates are the wrong default.

## Requirements to clarify

- Peer-to-peer transfer, or checkout for a purchase?
- Does the app handle card details itself, or use a payment provider's SDK or a platform wallet such as Apple Pay or Google Pay?
- Is a confirmation step with biometrics or a PIN required?
- Must the user see past transactions offline?
- What should happen when the result of a payment is unknown?

A reasonable agreed scope: a peer-to-peer transfer from a stored balance, with biometric confirmation and a transaction history.

## API and data model

| Call | Purpose |
|---|---|
| `POST /v1/transfers` with an `Idempotency-Key` header | Create a transfer |
| `GET /v1/transfers/{id}` | The current status of one transfer |
| `GET /v1/transfers?cursor=` | Transaction history, paged |

```http
POST /v1/transfers
Idempotency-Key: 7f3c9a2e-5b1d-4e8a-9c60-2d4f8b1a6e37

{ "to": "user_812", "amount": 2500, "currency": "USD" }
```

The amount is an integer in the smallest unit of the currency, here cents, because decimal fractions are not exact in floating point. A transfer has a status of pending, completed, or failed, and the server owns that status.

On the device, a `transfers` table caches the history. A small `attempts` table stores the idempotency key and the payload of any transfer whose result is not yet known.

## Client architecture

The flow has four screens: enter amount, review, confirm with biometrics, and result. A payment view model drives them as a state machine, so the flow is always in exactly one known state. A payments repository sends the request and records the attempt before the request leaves the device.

## The decisions that matter

**1. The idempotency key is created once and saved first.** The key is generated when the user reaches the review screen and is written to the `attempts` table before the request is sent. Every retry of that transfer reuses it, including a retry after the app was stopped and restarted. The server returns the original result for a repeated key and moves the money once.

**2. An unknown result is a state of its own.** If the request times out, the client does not know whether the money moved. It must not show "failed", because the user would send again. It shows "processing", asks the server for the transfer by its key, and repeats that check with a growing delay. Push can deliver the final status as a signal.

**3. No optimistic success.** The balance and the "sent" confirmation are shown only after the server confirms. The pay button is disabled after the first tap, which prevents a second submission from the same screen.

**4. The server enforces every rule.** The balance check, the transfer limit, and the recipient's validity are all verified on the server, because a modified client can skip any check in the app. Biometric confirmation releases a credential held in the secure store, and the app never stores the user's password or full card number. See [security and privacy](../concepts/security-and-privacy.md).

## Failure, offline, and shipping

- **Offline:** history is readable from the cache. New transfers are blocked with a clear message and are not queued, since a payment sent hours later may no longer be what the user wants.
- **App stopped mid-request:** on the next launch, saved attempts are checked with the server before anything else.
- **Sensitive screens:** hidden in the app switcher preview.
- **Shipping:** behind a flag to a small share of users, watching transfer success rate, the share of transfers that ended in the unknown state, and duplicate attempts caught by the server.

## What the interviewer probes

- The user taps pay, the screen freezes, and they close the app. What do they see when they reopen it?
- Why is the key created on the review screen and not at the moment of the tap?
- What does a compromised device let an attacker do, and what limits the damage?
- How would a card payment through a provider's SDK change the design?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow) uses a peer-to-peer payment flow as the capstone of its security and shipping module, and covers booking and checkout as a case study.
- Lesson: [Capstone: A P2P Payment Flow](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/capstone-a-p2p-payment-flow?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow), in Grokking Modern Mobile System Design Interview
- Lesson: [Booking and Checkout](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/booking-and-checkout?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow), in Grokking Modern Mobile System Design Interview
- Lesson: [Idempotency Keys](https://www.designgurus.io/course-play/grokking-modern-api-design-interview/doc/idempotency-keys?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow), in Grokking Modern API Design Interview
- [Grokking Modern API Design Interview](https://www.designgurus.io/course/grokking-modern-api-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow) covers idempotency and payments API design.
- [Networking and API contracts](../concepts/networking-and-api-contracts.md) covers retries and timeouts.
- Free article: [Designing an Online Payment Processing System](https://www.designgurus.io/blog/design-online-payment-processing-system?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow), the server side of the payment.
- Free article: [Grokking Idempotency in System Design](https://www.designgurus.io/blog/grokking-idempotency?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-mobile-payments-flow), which explains how a retried payment avoids a double charge.
