# Design a Video Streaming App

A video client plays content that is far too large to download before playback begins. The design centers on three things: how the player adapts quality to the network, how the next video is made ready before the user asks for it, and how downloads for offline viewing are stored and protected.

## Requirements to clarify

- Long videos chosen from a catalog, or a vertical feed of short videos? (The two lead to different deep dives.)
- Is offline download in scope?
- Is live streaming in scope? (Usually not.)
- Must playback continue in the background or in picture-in-picture?
- Three numbers: the typical video length, the bitrates offered, and the storage available for downloads.

A worked estimate: at 2.5 megabits per second, one minute of video is about 19 MB, so a 40 minute episode is about 750 MB. That number decides the download and cache design.

## API and data model

| Call | Purpose |
|---|---|
| `GET /v1/videos/{id}` | Title, duration, thumbnail, and the manifest URL |
| `GET {manifestUrl}` | The list of quality levels and their segments |
| `GET {segmentUrl}` | One segment of a few seconds, served by a CDN |
| `PUT /v1/videos/{id}/progress` | Save the playback position |

A **manifest** is a small file that lists the available quality levels and the address of each segment. The two common formats are HLS and DASH.

On the device, a `videos` table holds metadata and the resume position, and a `downloads` table holds the file path, the size, the status, and an expiry time.

## Client architecture

A player screen and its view model sit above a playback controller. The controller wraps the platform player (AVPlayer on iOS, ExoPlayer in Media3 on Android), which already implements segment fetching and quality switching. Beside it are a preload manager, a download manager, and a progress reporter. A candidate should use the platform player and design around it, since rebuilding it is not the point of the round.

## The decisions that matter

**1. Adaptive bitrate.** The video is encoded at several quality levels and cut into segments. The player measures download speed and buffer length and picks the level for each next segment. The design choices are the starting level (low, for a fast first frame) and limits such as a quality cap on cellular data.

**2. Time to first frame.** For a short video feed, the client preloads the first segments of the next one or two videos while the current one plays. Preloading more wastes data on videos the user skips. Only one player decodes at a time, and thumbnails cover the others.

**3. Downloads for offline viewing.** A download is hundreds of megabytes, so it is a system-owned background transfer, restricted to Wi-Fi by default, with progress stored in the `downloads` table. Protected content needs a license from a DRM (digital rights management) system, and the license has an expiry the app must respect. Storage use is shown to the user, and expired downloads are removed.

**4. Saving progress.** The position is written locally every few seconds and sent to the server in batches, so playback can resume on another device without a request per second.

## Failure, offline, and shipping

- **Network drops during playback:** the player uses its buffer, then shows a retry state and keeps the position.
- **Offline:** downloaded titles play; everything else is shown as unavailable instead of failing on tap.
- **Low storage:** new downloads are refused with a clear message.
- **Shipping:** behind a flag, watching time to first frame, rebuffering ratio (stalled time divided by watch time), and playback failures.

## What the interviewer probes

- The user scrolls quickly through ten short videos. What is downloaded, and what is cancelled?
- How does the app decide the quality of the first segment?
- What happens to a download when the license expires while the device is offline?
- How does battery use change the design for a two hour film?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-video-streaming-app) covers stories and short video, and live streams and tickers, as case studies.
- [App performance](../concepts/app-performance.md) covers the memory and frame limits a player must respect.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-video-streaming-app) designs the video backend and delivery network.
- Free article: [Designing a Scalable Video Streaming Platform Like YouTube and Netflix](https://www.designgurus.io/blog/design-video-streaming-platform?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-video-streaming-app), the encoding and delivery pipeline.
- Free answer: [Netflix System Design: Design a Streaming Service](https://www.designgurus.io/answers/detail/designing-netflixlike-streaming-service?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-video-streaming-app), a worked interview answer.
