# App Performance

Mobile performance comes down to four measurements: how fast the app starts, how smoothly it scrolls, how much memory it holds, and how large it is to download. Each has a number a candidate can state, and each is controlled by a small set of design decisions.

## Startup time

| Kind of start | What already exists | Typical feel |
|---|---|---|
| Cold | Nothing; the process must be created | The slowest, often a second or more |
| Warm | The process and loaded code | Short but noticeable |
| Hot | The process and the screen objects | Immediate |

A cold start is slow because of work done before the first frame. The main fix is **lazy initialization**, which means creating an object when it is first needed and not at launch. An analytics library, a database, and an image pipeline that all start at launch can add hundreds of milliseconds between them.

A useful target to state is the time to the first useful screen, meaning real content from the local store and not a loading indicator. An [offline-first](offline-first-and-sync.md) design helps here, because the first screen does not wait for the network.

## The frame budget

A display that refreshes 60 times per second gives each frame about 16 milliseconds. A display at 120 frames per second gives about 8 milliseconds. Any work on the main thread that takes longer than the budget causes a dropped frame, which the user sees as a stutter.

| Work | Where it should run |
|---|---|
| Layout and drawing | Main thread |
| Network calls | Background |
| Database queries | Background |
| JSON parsing | Background |
| Image decoding | Background |

The **main thread** is the single thread that draws the screen and handles touches. The rule is that it does nothing else.

Long lists stay smooth for two further reasons: row views are reused instead of created for every item, and each row is cheap to lay out.

## Memory

The operating system stops apps that use too much memory, and it stops background apps first when the device runs short.

| Cause | Design answer |
|---|---|
| Full-size decoded images | Decode at the size of the view; see [image loading and caching](image-loading-and-caching.md) |
| Unbounded in-memory caches | A size limit, and clearing on a memory warning |
| Whole lists held in memory | Page from the database |
| Objects that are never released | Clear observers and callbacks when a screen closes |

Because the system can stop the process at any time, each screen should save enough state to restore itself, such as the scroll position and any unsent text.

## App size

A large download lowers the share of users who finish installing, especially on cellular networks and low-end devices. Common reductions are compressing images, removing unused code and resources, and downloading rarely used features on demand instead of shipping them in the first install.

## Measuring in production

A performance claim needs a number from real devices. Both platforms report launch time, slow frames, and crashes from the field: Xcode Organizer and MetricKit on iOS, and Android vitals in the Play Console on Android. A design should name two or three numbers it would watch after release, for example crash-free sessions, cold start time at the 90th percentile, and the share of slow frames.

## What the interviewer probes

- What runs before the first frame, and what could wait?
- Which thread does this work run on?
- How does the screen behave on a device with 2 GB of memory?
- How would you know that the new release made scrolling worse?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-app-performance) has a performance module covering startup, lists and memory, threading, and a capstone on fixing a slow feed.
- Lesson: [App Startup](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/app-startup?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-app-performance), in Grokking Modern Mobile System Design Interview
- Lesson: [Lists, Images, and Memory](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/lists-images-and-memory?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-app-performance), in Grokking Modern Mobile System Design Interview
- [Background work and battery](background-work-and-battery.md) covers the fifth measurement, energy use.
