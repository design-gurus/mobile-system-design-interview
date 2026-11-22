# Networking and API Contracts

A mobile client needs an API that is small per response, safe to retry, and tolerant of old app versions. Those three needs come from the mobile network, which is slower and less reliable than a data center link, and from the fact that users keep old versions of the app installed for months.

## What the mobile network does to a request

| Property | Effect on the design |
|---|---|
| High and variable latency | Fewer round trips per screen; one call that returns what the screen needs |
| Connections drop mid-request | Every write must be safe to send twice |
| Radio wake-ups cost battery | Batch small requests instead of sending many |
| Metered data plans | Compress responses and never download what the screen will not show |

## The contract for one call

In step 2 of [the five-step method](../framework.md), each call should be stated with its fields and its rough size. A feed call is a useful model:

```http
GET /v1/feed?cursor=abc123&limit=20
```

```json
{
  "items": [
    { "id": "p_81", "author": "Maya", "imageUrl": "https://cdn.example.com/p_81.jpg", "caption": "Harbor at dawn", "likeCount": 42 }
  ],
  "nextCursor": "def456"
}
```

At about 2 KB per post, a page of 20 is about 40 KB. Saying that number aloud shows that the response size was a decision.

## API styles for a mobile client

| Style | Strength for mobile | Cost |
|---|---|---|
| REST | Simple, cacheable with standard HTTP rules | A screen may need several calls |
| GraphQL | The client asks for exactly the fields a screen shows, in one call | Caching and server complexity |
| gRPC | Compact binary messages and streaming | Harder to inspect, and it needs more tooling |

REST is a safe default in an interview. GraphQL is worth naming when one screen combines data from many resources.

## Making writes safe to retry

An **idempotent** request produces the same result whether it is sent once or five times. Reads are idempotent already. For writes, the client creates a unique identifier, called an idempotency key, and sends it with the request. The server records the key and returns the saved result when the same key arrives again. Without it, a retried "send message" produces two messages.

## The request lifecycle

Every request in the design should have an answer for these five points:

1. **Timeout.** How long the client waits before it gives up.
2. **Retry.** Which errors are retried, with a growing delay between attempts and a limit on the count.
3. **Cancellation.** What happens to the request when the user leaves the screen.
4. **Authentication.** How an expired token is refreshed once, even when ten requests fail together.
5. **Error display.** What the user sees, and whether cached data stays on screen.

## Versioning

A mobile release cannot be recalled, so the server must keep serving old clients. Three rules keep the contract safe: add fields and never remove them, make the client ignore fields it does not know, and keep a minimum supported version that the server can raise. See [release and feature flags](release-and-feature-flags.md).

## What the interviewer probes

- What happens if this request is sent twice?
- What does the user see while this call is in flight, and when it fails?
- How does a two-year-old version of the app read this response?
- Why 20 items per page and not 100?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts) has a module on networking and APIs, with a comment thread API as its worked design.
- Lesson: [The Mobile Network](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/the-mobile-network?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts), in Grokking Modern Mobile System Design Interview
- Lesson: [The Request Lifecycle](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/the-request-lifecycle?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts), in Grokking Modern Mobile System Design Interview
- Lesson: [Designing for One Screen](https://www.designgurus.io/course-play/grokking-modern-api-design-interview/doc/designing-for-one-screen?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts), in Grokking Modern API Design Interview
- [Grokking Modern API Design Interview](https://www.designgurus.io/course/grokking-modern-api-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts) covers resource modeling, pagination, idempotency, and versioning in depth.
- [API Design Interview](https://github.com/design-gurus/api-design-interview) is the free sibling guide to the API round.
- Free article: [REST vs GraphQL vs gRPC: Differences, Performance, and When to Use](https://www.designgurus.io/blog/rest-graphql-grpc-system-design?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts), a comparison of the three API styles.
- Free article: [Grokking Idempotency in System Design](https://www.designgurus.io/blog/grokking-idempotency?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-networking-and-api-contracts), which explains why retried requests must be safe to repeat.
