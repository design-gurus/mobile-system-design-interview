# Push Notifications

A push notification is a message that reaches a device through a connection the operating system keeps open for all apps. It is the only channel that can reach an app that is not running. It is also best-effort, which means delivery is not guaranteed, and a design that depends on every push arriving is wrong.

## The two platform services

| Platform | Service | Abbreviation |
|---|---|---|
| iOS | Apple Push Notification service | APNs |
| Android | Firebase Cloud Messaging | FCM |

FCM can also deliver to iOS devices, and it does so by passing the message to APNs.

## How a push travels

1. The app registers with the platform service and receives a **device token**, which is an address for this app on this device.
2. The app sends the token to its own backend, which stores it against the user's account.
3. When something happens, the backend sends a message and the token to APNs or FCM.
4. The platform service delivers the message over the system connection.
5. The operating system shows the notification, or wakes the app to handle it.

Tokens change, for example after a reinstall or a restore to a new device. The app should send its token to the backend at every launch, and the backend should remove tokens the platform service reports as invalid.

## Two kinds of message

| Kind | What it does | Limit |
|---|---|---|
| Visible notification | The system displays a title and body | The user may have denied permission |
| Silent or data message | Wakes the app briefly to fetch new data | The system may delay or drop it, especially when the battery is low |

## Push is a signal, not the data

Because a push can be delayed, dropped, or collapsed with others, it should carry a hint and not the content. The safe pattern is:

1. The push says "something changed in conversation 42."
2. The app wakes and runs its normal sync against the server.
3. The local store is updated, and the screen redraws from the store.

If the push never arrives, the same sync runs when the user next opens the app, so nothing is lost. A chat app that inserts message text straight from push payloads will show gaps and wrong ordering.

## Permission

On iOS the user must grant permission before any visible notification appears. Recent Android versions require a runtime permission as well. Three rules follow:

- Ask at a moment when the value is clear, such as after the user sends a first message, not at first launch.
- Design the feature to work when permission is denied, usually with an in-app inbox and a badge.
- Let the user choose categories, so that turning off marketing does not turn off messages.

## Tapping a notification

A notification should open the exact screen it describes. This is done with a **deep link**, which is a URL that maps to a screen inside the app. The design must handle the case where the app was not running: it starts, restores the session, fetches the item, and then shows the screen.

## What the interviewer probes

- What happens if the push for a new message never arrives?
- How do you avoid showing a notification for a message the user has already read on another device?
- How does the backend handle a user with four devices?
- What does the app do when the user has denied permission?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-push-notifications) covers push in its networking and background execution lessons and designs a push notification SDK.
- Lesson: [Push Notification SDK](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/push-notification-sdk?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-push-notifications), in Grokking Modern Mobile System Design Interview
- [Real-time updates](real-time-updates.md) compares push with the transports used while the app is open.
- [Background work and battery](background-work-and-battery.md) explains what a silent push is allowed to do.
