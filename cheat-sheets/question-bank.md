# Mobile System Design Question Bank

This page lists 40 questions that are commonly reported from mobile system design rounds for iOS and Android roles. They are grouped by family, and each family has one line on what the interviewer is measuring. Questions with a walkthrough in this repository are linked.

## Product screens

The interviewer measures scoping, the data model, and the layers of one screen.

1. [Design a news feed app.](../questions/design-a-news-feed-app.md)
2. [Design a chat app.](../questions/design-a-chat-app.md)
3. [Design a photo sharing app.](../questions/design-a-photo-sharing-app.md)
4. [Design a ride hailing app.](../questions/design-a-ride-hailing-app.md)
5. [Design a video streaming app.](../questions/design-a-video-streaming-app.md)
6. [Design a maps and location app.](../questions/design-a-maps-and-location-app.md)
7. Design an email client.
8. Design a food delivery app with live order tracking.
9. Design a stories feature with photos and short videos that expire.
10. Design a music player with playlists and background playback.
11. Design a calendar app with reminders.
12. Design a stock ticker screen with live prices.

## Flows

The interviewer measures state handling across several screens, and behavior when a step fails.

13. [Design a mobile payments flow.](../questions/design-a-mobile-payments-flow.md)
14. Design a booking and checkout flow for hotel rooms.
15. Design login with token refresh and "log out of all devices".
16. Design a sign-up flow that survives the app being stopped halfway.
17. Design in-app search with suggestions as the user types.
18. Design a comment thread with replies under a post.
19. Design the notification inbox and the path from a push to the right screen.

## Offline and sync

The interviewer measures the source of truth, queued writes, and the conflict rule.

20. [Design an offline notes app.](../questions/design-an-offline-notes-app.md)
21. Design a to-do list shared between two people.
22. Design photo backup that uploads the whole camera roll.
23. Design a field inspection app used for days without a network.
24. Design a reading app that downloads articles for later.
25. Design a contacts app that syncs across devices.

## Libraries and SDKs

The interviewer measures the public interface, threading, cancellation, and what happens inside the host app.

26. Design an image loading library.
27. Design a networking library with retries and request cancellation.
28. Design a file download and upload library with resume.
29. Design an analytics SDK that batches events.
30. Design a feature flag SDK.
31. Design a push notification SDK.
32. Design a crash reporting SDK.
33. Design a caching library with memory and disk levels.
34. Design an event bus for messages between modules.

## Performance and operations

The interviewer measures diagnosis with numbers, not a list of tips.

35. The feed scrolls badly on low-end devices. How do you find and fix the cause?
36. Cold start takes four seconds. What do you measure, and what do you move?
37. The app uses too much battery in the background. How do you investigate?
38. A release is crashing for 5 percent of users. What happens in the next hour?
39. The app has grown to 300 MB. How do you reduce it?
40. How would you split a large app into modules owned by different teams?

## Follow-ups that change the question

Interviewers often add a requirement after the first design is complete. These are the most common ones, and each tests whether the design can absorb a change:

| Follow-up | What it tests |
|---|---|
| "It must now work offline." | Whether the local store was already the source of truth |
| "Make it real time." | Transport choice and catch-up after a gap |
| "Support low-end devices in slow networks." | Payload size, image sizes, and memory limits |
| "Now sketch the backend." | A brief, correct server outline |
| "Ten teams will work on this." | Module boundaries and ownership |

## How to use this page

Pick one question from each family per week. Answer aloud in 35 minutes with [the five-step method](../framework.md), record the session, and check it against [common mistakes](common-mistakes.md).

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-question-bank) works through questions from every family above, including library and SDK design and the new-requirements follow-up.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-question-bank) covers the backend versions of the product questions.
- Free article: [Mobile System Design Interview: The 20 Most Asked Questions](https://www.designgurus.io/blog/mobile-system-design-interview-questions?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-question-bank), with notes on what each question tests.
- Free answer: [What Questions Are Asked in a Mobile System Design Interview?](https://www.designgurus.io/answers/detail/what-questions-are-asked-in-a-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-question-bank), a shorter list grouped by type.
