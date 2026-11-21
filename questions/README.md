# Questions

These eight walkthroughs follow [the five-step method](../framework.md). Each one lists the requirements to clarify, sketches the API, the data, and the client, and then names the two or three decisions that carry the round. They stop at approach and trade-offs and are not full solutions.

| Question | The hard parts | Concepts used |
|---|---|---|
| [Design a news feed app](design-a-news-feed-app.md) | Paging with changing data, and offline reads | [Pagination](../concepts/pagination-and-feeds.md), [images](../concepts/image-loading-and-caching.md) |
| [Design a chat app](design-a-chat-app.md) | Message ordering, and sending with no network | [Real-time updates](../concepts/real-time-updates.md), [push](../concepts/push-notifications.md) |
| [Design a photo sharing app](design-a-photo-sharing-app.md) | Resuming an interrupted upload, and background limits | [Background work](../concepts/background-work-and-battery.md) |
| [Design a ride hailing app](design-a-ride-hailing-app.md) | Location updates against battery, and stale positions | [Real-time updates](../concepts/real-time-updates.md) |
| [Design a video streaming app](design-a-video-streaming-app.md) | Adaptive playback, preloading, and downloads | [App performance](../concepts/app-performance.md) |
| [Design an offline notes app](design-an-offline-notes-app.md) | Sync and conflict resolution | [Offline-first and sync](../concepts/offline-first-and-sync.md) |
| [Design a maps and location app](design-a-maps-and-location-app.md) | Map tiles, offline regions, and location accuracy | [Local storage](../concepts/local-storage.md) |
| [Design a mobile payments flow](design-a-mobile-payments-flow.md) | Idempotent requests, and an unknown payment result | [Security and privacy](../concepts/security-and-privacy.md) |

## How to practice with these pages

1. Read only the question title, set a timer for 35 minutes, and answer aloud.
2. Compare the answer with the page, and note which decision was missed.
3. Repeat the same question two days later with a changed requirement, such as "it must now work offline".

More questions without walkthroughs are in [the question bank](../cheat-sheets/question-bank.md).

## Go deeper

[Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-readme) works through complete app case studies, including a news feed, a chat app, an email client, photo backup, food delivery, booking and checkout, short video, ride hailing, and live streams.
