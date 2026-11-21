# iOS vs Android Differences

The method for a mobile design round is the same on both platforms, and most of the design is shared. The differences are in the names of the tools and in a few limits, mainly around background work, push, and distribution. A candidate should name the tools of their own platform, state each tool's limit, and recognize the other platform's equivalent.

## The translation table

| Job | iOS | Android |
|---|---|---|
| Language | Swift | Kotlin |
| Declarative UI framework | SwiftUI | Jetpack Compose |
| Older UI framework | UIKit | Views with XML layouts |
| Screen state holder | An observable view model object | ViewModel from Jetpack |
| Asynchronous work | Swift concurrency (async and await) | Kotlin coroutines and Flow |
| Relational storage | Core Data or SwiftData | Room |
| Settings storage | UserDefaults | DataStore |
| Secret storage | Keychain | Keystore |
| Scheduled background work | BGTaskScheduler | WorkManager |
| Large background transfer | Background URLSession | WorkManager with a foreground service |
| Networking foundation | URLSession | OkHttp, often with Retrofit |
| Video playback | AVPlayer | ExoPlayer in Media3 |
| Push service | Apple Push Notification service (APNs) | Firebase Cloud Messaging (FCM) |
| Field performance data | Xcode Organizer and MetricKit | Android vitals in the Play Console |
| Staged release | Phased release on the App Store | Staged rollout on Google Play |

A **declarative UI framework** is one where the code describes what the screen should look like for a given state, and the framework updates the screen when the state changes.

## Differences that change a design

| Area | iOS | Android |
|---|---|---|
| Device range | A small set of devices from one maker | Thousands of models, including low-memory devices |
| OS versions in use | Most users update within months | Several major versions remain in wide use |
| Background work | Strict; the system decides when scheduled tasks run | Strict, with Doze mode; some phone makers add their own limits |
| Long visible work | Limited background modes, such as audio and location | Foreground services with a persistent notification |
| Memory management | Automatic reference counting | Garbage collection |
| Screen lifecycle | The process may be stopped in the background | The same, and an activity may also be recreated on rotation unless handled |
| Distribution | The App Store only, in most regions | Google Play, other stores, and direct installation |
| Deep links | Universal Links | App Links |

**Automatic reference counting** frees an object when nothing refers to it any longer. **Garbage collection** finds and frees unused objects in periodic passes, and a long pass can cause a dropped frame.

## What the differences mean in an answer

- **Low-end Android devices** make memory limits and image sizes a first-class topic. A design should say how it behaves with 2 GB of memory.
- **Old OS versions** mean an Android design should state its minimum supported version when it relies on a newer platform feature.
- **Background limits on both platforms** mean no design may depend on background work for correctness. See [background work and battery](../concepts/background-work-and-battery.md).
- **Configuration changes on Android** are the reason state lives in a view model and not in the view.

## Cross-platform frameworks

Frameworks such as React Native, Flutter, and Kotlin Multiplatform share code between the two platforms. The trade-off to state is simple: shared code lowers the cost of building a feature twice, while platform features such as background work, push, and secure storage still need native code on each side. An interviewer who asks about this wants the reasoning, not a favorite.

## Questions to expect about the other platform

- How would this background upload work on the other platform?
- Which parts of this design are identical on both, and which are not?
- Would you share this module across platforms? Why?

A short, honest answer is enough: "I have less depth there, but the equivalent is WorkManager, and the same rule holds that the system chooses the time."

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-ios-vs-android-differences) names the iOS and Android tool for every mechanism it teaches and has a lesson on native versus cross-platform.
- [Grokking React Native](https://www.designgurus.io/course/grokking-react-native?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-ios-vs-android-differences) is the course for the most widely used cross-platform framework.
- [Local storage](../concepts/local-storage.md) compares the storage tools in more detail.
