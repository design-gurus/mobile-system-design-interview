# Concepts

These twelve pages cover the building blocks of a mobile system design answer. Each page states the idea, compares the options in a table, and ends with what an interviewer usually asks next.

| Page | What it answers |
|---|---|
| [App architecture layers](app-architecture-layers.md) | The four layers, and how MVC, MVVM, and MVI compare |
| [Networking and API contracts](networking-and-api-contracts.md) | What a mobile client needs from an API, and how a request should fail |
| [Offline-first and sync](offline-first-and-sync.md) | The local store as the source of truth, the outbox, and conflict rules |
| [Local storage](local-storage.md) | The five kinds of on-device store and what belongs in each |
| [Pagination and feeds](pagination-and-feeds.md) | Cursor paging, page size, and a list that changes under the user |
| [Image loading and caching](image-loading-and-caching.md) | The memory cache, the disk cache, and requests for rows that scrolled away |
| [Push notifications](push-notifications.md) | How the platform push services work and why push is not reliable delivery |
| [Real-time updates](real-time-updates.md) | Polling, server-sent events, WebSockets, and push compared |
| [Background work and battery](background-work-and-battery.md) | What the operating system allows after the app leaves the screen |
| [App performance](app-performance.md) | Startup time, the 16 millisecond frame, memory, and app size |
| [Security and privacy](security-and-privacy.md) | Token storage, transport security, and permissions |
| [Release and feature flags](release-and-feature-flags.md) | Staged rollouts, kill switches, and forced upgrades |

A reader who is new to the round should start with [the five-step method](../framework.md), then read the first three pages in order.

## Go deeper

[Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-readme) covers each of these topics as a full module with diagrams, worked designs, and assessments.
