# Mobile System Design Interview

> The free, open companion to **Grokking Modern Mobile System Design Interview** by [DesignGurus.io](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=readme), created by Arslan Ahmad.

[![GitHub stars](https://img.shields.io/github/stars/design-gurus/mobile-system-design-interview?style=social)](https://github.com/design-gurus/mobile-system-design-interview/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/design-gurus/mobile-system-design-interview)](https://github.com/design-gurus/mobile-system-design-interview/commits/main)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](LICENSE)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A backend system design round asks how servers handle many users at once. A mobile system design round asks how one application behaves on one device, where the network is slow, the battery is small, and the operating system can stop the process at any moment. The server is usually handed to the candidate as something that already works.

This repository is a free guide to that round for iOS and Android engineers. It holds a five-step method, twelve concept pages, eight question walkthroughs, and five cheat sheets. Every page is short, and every page ends with a link to the course lesson set that covers the topic in full.

## Start here

| Your situation | Read this first |
|---|---|
| The interview is in a few days | [The five-step method](framework.md), then [common mistakes](cheat-sheets/common-mistakes.md) |
| You know backend system design and are new to the mobile round | [Mobile vs backend system design](cheat-sheets/mobile-vs-backend-system-design.md) |
| You build on one platform and the interviewer may ask about the other | [iOS vs Android differences](cheat-sheets/ios-vs-android-differences.md) |
| You want practice questions | [The question bank](cheat-sheets/question-bank.md), then the [question walkthroughs](questions/README.md) |
| You have a month | [The four-week plan](cheat-sheets/four-week-plan.md) |

## The five-step method

A 45-minute booking leaves about 35 minutes of design time. The method below spends those minutes in a fixed order, and it places the data before the architecture, because the layers cannot be drawn until the candidate knows what moves through them. The full version is on the [framework page](framework.md).

| Step | Minutes | What the candidate produces |
|---|---|---|
| 1. Requirements | 5 | Three to five features, three constraints, and one line on what is out of scope |
| 2. API and data model | 8 | Two or three calls with real fields, a paging choice, and the local schema |
| 3. Client architecture | 10 | One diagram, with each box named for its job and its data |
| 4. Deep dive | 17 | Two or three hard parts, taken down to the mechanism |
| 5. Failure, offline, and shipping | 5 | Failure behavior, offline behavior, and a release plan |

## Concepts

| Page | What it answers |
|---|---|
| [App architecture layers](concepts/app-architecture-layers.md) | The four layers, and how MVC, MVVM, and MVI compare |
| [Networking and API contracts](concepts/networking-and-api-contracts.md) | What a mobile client needs from an API, and how a request should fail |
| [Offline-first and sync](concepts/offline-first-and-sync.md) | The local store as the source of truth, the outbox, and conflict rules |
| [Local storage](concepts/local-storage.md) | The five kinds of on-device store and what belongs in each |
| [Pagination and feeds](concepts/pagination-and-feeds.md) | Cursor paging, page size, and a list that changes under the user |
| [Image loading and caching](concepts/image-loading-and-caching.md) | The memory cache, the disk cache, and requests for rows that scrolled away |
| [Push notifications](concepts/push-notifications.md) | How the platform push services work and why push is not reliable delivery |
| [Real-time updates](concepts/real-time-updates.md) | Polling, server-sent events, WebSockets, and push compared |
| [Background work and battery](concepts/background-work-and-battery.md) | What the operating system allows after the app leaves the screen |
| [App performance](concepts/app-performance.md) | Startup time, the 16 millisecond frame, memory, and app size |
| [Security and privacy](concepts/security-and-privacy.md) | Token storage, transport security, and permissions |
| [Release and feature flags](concepts/release-and-feature-flags.md) | Staged rollouts, kill switches, and forced upgrades |

## Questions

Each walkthrough stays at the level of approach and trade-offs. It lists the requirements to clarify, sketches the client, and names the two or three decisions that carry the round.

| Question | The hard parts |
|---|---|
| [Design a news feed app](questions/design-a-news-feed-app.md) | Paging with changing data, and offline reads |
| [Design a chat app](questions/design-a-chat-app.md) | Message ordering, and sending with no network |
| [Design a photo sharing app](questions/design-a-photo-sharing-app.md) | Resuming an interrupted upload, and background limits |
| [Design a ride hailing app](questions/design-a-ride-hailing-app.md) | Location updates against battery, and stale positions |
| [Design a video streaming app](questions/design-a-video-streaming-app.md) | Adaptive playback, preloading, and downloads |
| [Design an offline notes app](questions/design-an-offline-notes-app.md) | Sync and conflict resolution |
| [Design a maps and location app](questions/design-a-maps-and-location-app.md) | Map tiles, offline regions, and location accuracy |
| [Design a mobile payments flow](questions/design-a-mobile-payments-flow.md) | Idempotent requests, and an unknown payment result |

## Cheat sheets

| Page | Use it for |
|---|---|
| [Mobile vs backend system design](cheat-sheets/mobile-vs-backend-system-design.md) | The differences between the two rounds in one table |
| [iOS vs Android differences](cheat-sheets/ios-vs-android-differences.md) | A translation table of platform tools and limits |
| [Question bank](cheat-sheets/question-bank.md) | 40 questions grouped by topic |
| [Common mistakes](cheat-sheets/common-mistakes.md) | The ten errors that cost the most signal |
| [Four-week plan](cheat-sheets/four-week-plan.md) | A study schedule of about six hours a week |

## FAQ

### What is a mobile system design interview?

It is a design round for iOS and Android engineers in which the candidate designs the client side of a product, such as a feed, a chat screen, or an upload flow. The interviewer grades the layers of the application, the data stored on the device, the API the client needs, and the behavior of the app when the network or the process fails.

### How is mobile system design different from backend system design?

A backend design is concerned with volume: many users, many servers, and copies of data that must agree. A mobile design is concerned with one device under bad conditions. The hard questions are offline behavior, battery, memory, the application lifecycle, and releases that cannot be taken back. The [comparison page](cheat-sheets/mobile-vs-backend-system-design.md) lists the differences.

### What questions are asked in a mobile system design interview?

Most questions belong to three families: a product screen (a feed, a chat app, a ride hailing app), a flow (photo upload, payments, offline notes), or a library (an image loader, a networking library, an analytics SDK). The [question bank](cheat-sheets/question-bank.md) holds 40 of them.

### Is the iOS system design interview different from the Android one?

The method and the signals are the same on both platforms. The names of the tools differ, and so do a few limits, mainly around background work and push delivery. A candidate is expected to name the tools of their own platform and to state each tool's limit.

### Do I need to design the backend in a mobile round?

Usually not. The first question to ask is whether the design covers the application only, the application and the API, or the server as well. Most rounds stop at the API contract, although senior candidates are sometimes asked a short follow-up about the server.

### How long does it take to prepare?

An engineer who ships mobile features every week usually needs about four weeks at six hours a week. Most of that time goes to speaking designs aloud against a clock, because the knowledge is already there and the pacing is not.

## Go deeper

This repository gives the short version of each topic. [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=readme) teaches the full version: the five-step method with graded sample answers, client architecture, networking, storage and sync, security and shipping, complete app case studies, library and SDK questions, and a full mock interview.

Related courses and guides:

- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=readme) covers the backend round, which many mobile loops also include.
- [Grokking Modern API Design Interview](https://www.designgurus.io/course/grokking-modern-api-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=readme) covers the API contract in depth.
- [Frontend System Design Interview](https://github.com/design-gurus/frontend-system-design-interview) is the sibling guide for web clients.
- [Grokking System Design](https://github.com/design-gurus/grokking-system-design) is the sibling guide for the backend round.

## Contributing

Corrections and new pages are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

The content is licensed under [CC BY 4.0](LICENSE). See [NOTICE.md](NOTICE.md) for what the license covers and how to attribute.
