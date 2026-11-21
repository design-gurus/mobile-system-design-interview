# Four-Week Plan for the Mobile System Design Interview

This plan takes about six hours a week for four weeks, which is 24 hours in total. It suits an engineer who already ships mobile features and needs to turn that experience into a structured 45-minute answer. The largest share of the time goes to answering aloud against a clock, because pacing is what most candidates lack.

## How the hours are divided

| Activity | Hours | Share |
|---|---|---|
| Reading concepts | 7 | About 30 percent |
| Timed practice aloud | 11 | About 45 percent |
| Review of recordings | 4 | About 15 percent |
| Mock interviews with another person | 2 | About 10 percent |

## Week 1: the method and the architecture

Goal: answer any question in the correct order, even with thin content.

| Session | Work |
|---|---|
| 1 | Read [the five-step method](../framework.md) and [mobile vs backend system design](mobile-vs-backend-system-design.md) |
| 2 | Read [app architecture layers](../concepts/app-architecture-layers.md) and [networking and API contracts](../concepts/networking-and-api-contracts.md) |
| 3 | Answer [design a news feed app](../questions/design-a-news-feed-app.md) aloud in 35 minutes, and record it |
| 4 | Listen to the recording and mark each step's start time against the budget |

Finish condition: the recording reaches step 4, the deep dive, before design minute 25.

## Week 2: data on the device

Goal: make the local store the source of truth in every answer.

| Session | Work |
|---|---|
| 1 | Read [local storage](../concepts/local-storage.md) and [offline-first and sync](../concepts/offline-first-and-sync.md) |
| 2 | Read [pagination and feeds](../concepts/pagination-and-feeds.md) and [image loading and caching](../concepts/image-loading-and-caching.md) |
| 3 | Answer [design an offline notes app](../questions/design-an-offline-notes-app.md), recorded |
| 4 | Answer [design a photo sharing app](../questions/design-a-photo-sharing-app.md), recorded |

Finish condition: the conflict rule for notes can be stated in two sentences, with its cost.

## Week 3: real time, background, and performance

Goal: name the platform tool and its limit for every mechanism.

| Session | Work |
|---|---|
| 1 | Read [real-time updates](../concepts/real-time-updates.md) and [push notifications](../concepts/push-notifications.md) |
| 2 | Read [background work and battery](../concepts/background-work-and-battery.md) and [app performance](../concepts/app-performance.md) |
| 3 | Answer [design a chat app](../questions/design-a-chat-app.md), recorded |
| 4 | Answer [design a ride hailing app](../questions/design-a-ride-hailing-app.md), recorded |

Finish condition: each answer states what happens when the app is in the background and when a push never arrives.

## Week 4: shipping, libraries, and mocks

Goal: close every answer the same way, and handle a changed requirement.

| Session | Work |
|---|---|
| 1 | Read [security and privacy](../concepts/security-and-privacy.md) and [release and feature flags](../concepts/release-and-feature-flags.md) |
| 2 | Answer one library question from [the question bank](question-bank.md), such as an image loading library |
| 3 | Mock interview with another engineer, who adds one follow-up from the question bank table |
| 4 | Second mock interview, then read [common mistakes](common-mistakes.md) and [iOS vs Android differences](ios-vs-android-differences.md) |

Finish condition: both mocks end with a rollout plan, a kill switch, and three numbers to watch.

## How to review a recording

Each recording is checked against four questions:

1. Did each step start within two minutes of its budget?
2. Was every decision followed by a reason?
3. Did each main part of the design get one sentence on failure?
4. Were there at least five concrete numbers, such as a page size or a cache limit?

One missed item per recording is normal in week 1. By week 4 the target is zero.

## If there is only one week

Read the framework and the common mistakes on day 1. Then answer one question aloud on each of the next five days, in this order: news feed, chat, offline notes, photo sharing, and one library. Use the last day for a mock interview.

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-four-week-plan) follows the same order across ten modules, with assessments after each and a full mock interview at the end.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-four-week-plan) is worth adding when the loop also includes a backend design round.
