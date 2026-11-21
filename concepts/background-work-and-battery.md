# Background Work and Battery

Once an app leaves the screen, the operating system decides whether its code runs at all. Both platforms restrict background work to protect the battery, so a design cannot say "the app syncs every five minutes" and be correct. It has to name the mechanism it uses and state what that mechanism does not promise.

## The four mechanisms

| Mechanism | iOS | Android | Used for | What it does not promise |
|---|---|---|---|---|
| Scheduled work | BGTaskScheduler | WorkManager | Refreshes and cleanup that can wait | A time. The system chooses when, and may skip it |
| A short window after leaving the screen | A background task of about 30 seconds | No direct equivalent | Finishing one small piece of work | Time for a large transfer |
| A transfer the system owns | A background URLSession | WorkManager, with a foreground service for long work | Large uploads and downloads | That the app's code runs during the transfer |
| Push wake-up | APNs | FCM | Telling the app that something changed | Delivery |

A **foreground service** on Android is work the user can see through a persistent notification, such as music playback or an active upload. The system lets it keep running because the user knows about it.

## What "every 15 minutes" means

A periodic job is a request, not a schedule. The system groups work from many apps to wake the radio less often, and it gives less time to apps the user rarely opens. On Android, Doze mode defers background work while the device is idle and unplugged. The practical rule for a design is this: background sync improves freshness, and correctness must never depend on it. The app must reach a correct state from a normal sync when it next opens.

## Large uploads

A video upload of 200 MB cannot finish inside a 30 second window. The design hands the transfer to the system, which continues even if the app's process is stopped.

| Requirement | Design answer |
|---|---|
| Survive a dropped connection | Upload in chunks and resume from the last confirmed chunk |
| Survive process death | A system-owned transfer, with progress saved in the local store |
| Respect the user's data plan | Wait for Wi-Fi when the file is large, with a visible setting |
| Show progress | The record in the local store carries the status, and the screen reads it |

## Where the battery goes

| Consumer | How a design reduces it |
|---|---|
| The radio | Batch requests; avoid frequent keepalive messages |
| Location | Request the lowest accuracy that works; stop updates when the screen closes |
| The CPU | Move work to scheduled jobs that run while charging |
| The screen | Outside the design's control, although fewer redraws help |

The radio stays in a high power state for several seconds after each use, so ten small requests spread over a minute cost far more than one batch of ten.

## Constraints worth naming

Scheduled work can carry conditions. Typical ones are "only on an unmetered network", "only while charging", and "only when storage is not low". Naming the condition shows the interviewer that the cost to the user was considered.

## What the interviewer probes

- What happens to this upload if the user closes the app at 60 percent?
- How fresh is the data when the app opens after eight hours in a pocket?
- What does your design do in low power mode?
- Which of these jobs could simply wait until the app is opened?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-background-work-and-battery) covers background execution in its storage module and battery, data, and low-end devices in its performance module.
- [Push notifications](push-notifications.md) covers the push wake-up in detail.
- [iOS vs Android differences](../cheat-sheets/ios-vs-android-differences.md) lists the platform tools side by side.
