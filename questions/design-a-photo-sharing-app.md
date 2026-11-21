# Design a Photo Sharing App

In this question the user picks a photo, adds a caption, and posts it for others to see. Viewing is a feed, which has [its own page](design-a-news-feed-app.md). The part that makes this question distinct is the upload: a large file must reach the server over a mobile network, even if the connection drops or the user leaves the app.

## Requirements to clarify

- Is the focus the upload flow, the viewing feed, or both? (Propose the upload flow, with the feed assumed.)
- One photo per post, or several? Is video in scope?
- Must the upload continue after the user leaves the app?
- Is editing, such as filters and cropping, in scope?
- Three numbers: the size of an original photo, the size after compression, and the upload speed to plan for.

A worked estimate: a 12 megapixel original is about 4 MB. Resized to 2,048 pixels on the long side and compressed, it is about 500 KB. On a slow connection of 1 megabit per second, that is about 4 seconds instead of 32.

## API and data model

| Call | Purpose |
|---|---|
| `POST /v1/uploads` | Start an upload session; returns an upload id and a URL |
| `PUT {uploadUrl}` with a byte range | Send one chunk |
| `GET /v1/uploads/{id}` | Ask how many bytes the server already holds |
| `POST /v1/posts` | Create the post from the finished upload id and the caption |

On the device, a `pending_posts` table holds a local id, the path of the prepared file, the caption, the upload id, the bytes confirmed, and a status.

## Client architecture

The compose screen writes a row to `pending_posts` and closes at once. An upload manager in the data layer prepares the file, hands the transfer to the operating system, and updates the row as it progresses. The feed observes both `posts` and `pending_posts`, so the new post appears at the top immediately with a progress indicator.

## The decisions that matter

**1. Prepare the file on the device.** Resizing and compressing before upload cuts the transfer by about 8 times in the estimate above. The work runs on a background thread, and the prepared file is written to the app's storage so that it survives a restart.

**2. Resumable upload.** The file is sent in chunks. After an interruption, the client asks the server how many bytes arrived and continues from that point. Without this, a dropped connection at 90 percent starts again from zero.

**3. Let the operating system own the transfer.** The app gets only a short window after it leaves the screen, so the upload is handed to a system-owned transfer that continues even if the process is stopped. See [background work and battery](../concepts/background-work-and-battery.md).

**4. Create the post once.** The final `POST /v1/posts` carries the local id as an idempotency key, so a retry after a lost response cannot publish the photo twice.

## Failure, offline, and shipping

- **No network:** the post stays in the list marked "waiting for connection" and starts when the network returns.
- **Upload rejected:** the row shows the reason, with retry and discard actions. The user's photo and caption are never lost silently.
- **Low storage:** prepared files are deleted once the post is confirmed.
- **Shipping:** behind a flag, watching upload success rate, median time to publish, and the share of uploads that needed a resume.

## What the interviewer probes

- The user posts ten photos in a carousel. Are they uploaded one at a time or in parallel, and why?
- The user edits the caption while the upload is running. What happens?
- How does the design respect a metered connection or low data mode?
- What changes when the file is a 200 MB video?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-photo-sharing-app) covers photo backup and sync as a case study and designs a file transfer library.
- [Image loading and caching](../concepts/image-loading-and-caching.md) covers the viewing side.
- [Grokking Modern API Design Interview](https://www.designgurus.io/course/grokking-modern-api-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-photo-sharing-app) covers upload API design in depth.
- Free article: [How to Design Instagram: A System Design Interview Walkthrough](https://www.designgurus.io/blog/how-to-design-instagram-in-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-photo-sharing-app), the backend that stores and serves the photos.
