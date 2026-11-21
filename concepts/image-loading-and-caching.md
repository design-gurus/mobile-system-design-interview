# Image Loading and Caching

An image pipeline has four jobs: fetch the right size, cache it at two levels, decode it off the main thread, and cancel work for rows that have scrolled away. Images are usually the largest part of a screen's data and memory, so this topic appears in feed, chat, and gallery questions, and it is also a common library question ("design an image loading library").

## The path of one image

1. The row asks for an image by URL and target size.
2. The **memory cache** is checked. A hit returns a decoded image at once.
3. The **disk cache** is checked. A hit is decoded on a background thread.
4. On a miss, the image is downloaded, written to disk, decoded, and placed in memory.
5. The image is set on the row, but only if the row still shows the same item.

## Two cache levels

| | Memory cache | Disk cache |
|---|---|---|
| Holds | Decoded images, ready to draw | Compressed files as downloaded |
| Speed | Immediate | Milliseconds, plus decoding |
| Survives process death | No | Yes |
| Typical limit | A share of available memory | A fixed size, such as 250 MB |
| Cleared when | The system reports memory pressure | The size or age limit is reached |

## Why decoded size matters

A compressed photo of 300 KB is small on disk and large in memory. Decoded, each pixel takes 4 bytes, so an image of 3,000 by 4,000 pixels takes 48 MB. A row that is 400 pixels wide does not need that image. The pipeline should request a size close to the view from the server, and downsample while decoding when the server cannot resize. Downsampling means decoding the image at a lower resolution than the file holds.

## Requesting the right size

| Technique | What it saves |
|---|---|
| Size parameters in the URL, served by an image service or CDN | Download bytes and memory |
| A modern compressed format such as WebP or HEIC | Download bytes |
| A small placeholder or dominant color sent with the list data | The blank space before the image arrives |

A **CDN** (content delivery network) is a set of servers that keep copies of files close to users.

## Cancellation and row reuse

Lists reuse row views. If a user scrolls fast, a row may be assigned a new item before the image for its old item arrives. Two rules prevent wrong images and wasted work:

- When a row is reused, the request for its old image is cancelled or lowered in priority.
- When an image arrives, the pipeline checks that the row still shows the item the image belongs to.

Requests for the same URL from two rows should also be joined into one download.

## Prefetching

The list can report which rows are about to appear, and the pipeline can start those downloads early. Prefetching should stop on a metered connection or in low data mode, which is a user setting on both platforms.

## What the interviewer probes

- How large is the memory cache, and what happens on a memory warning?
- What stops a fast scroll from starting 200 downloads?
- How does a changed profile photo replace the cached one? (A new URL per version is the simple answer.)
- Which thread does decoding run on, and why?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-image-loading-and-caching) covers lists, images, and memory in its performance module, and designs an image loading library in its libraries module.
- [App performance](app-performance.md) covers the frame budget that image decoding can break.
- [Design a photo sharing app](../questions/design-a-photo-sharing-app.md) applies this page.
- Free article: [CDN Architecture in System Design: How Content Delivery Networks Work](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-image-loading-and-caching), the server side of image delivery.
