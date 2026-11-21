# Pagination and Feeds

Pagination is the practice of loading a long list in pages instead of all at once. On a mobile feed the correct default is cursor paging, because new items arrive while the user scrolls, and page numbers then show the same item twice. Interviewers ask about this choice in almost every feed, chat, and search question.

## Offset paging and cursor paging

| | Offset paging | Cursor paging |
|---|---|---|
| The request | "Skip 40 items, return 20" | "Return 20 items after this cursor" |
| When the list changes | Items shift, so one repeats or is skipped | Stable, because the cursor marks a position |
| Jump to page 7 | Possible | Not possible |
| Server cost on deep pages | Grows with the offset | Constant |
| Fits | Search results with page numbers | Feeds, chats, comment threads |

A **cursor** is an opaque value the server returns with each page that marks where the page ended. The client sends it back to get the next page and never reads its contents.

## Why page numbers fail on a feed

Suppose the client has loaded page 1, which holds posts 1 to 20. Three new posts are then published at the top. The client asks for page 2 and expects posts 21 to 40, but the server now counts from the new top and returns posts 18 to 37. Posts 18, 19, and 20 appear twice. A cursor that says "after post 20" has no such problem.

## Choosing the page size

The page size follows from the screen and the network, and the arithmetic is worth saying aloud.

| Input | Example |
|---|---|
| Rows visible at once | 3 |
| Rows to keep ahead of the scroll | About 2 screens, so 6 more |
| Text per row | 2 KB |
| Chosen page size | 20 rows, or about 40 KB of text |

A page of 100 would waste data on rows most users never reach. A page of 5 would cause a request on almost every scroll gesture.

## When to fetch the next page

The client should request the next page before the user reaches the end, for example when five rows remain. Three details keep this correct:

- Only one request for the next page may be in flight at a time.
- A failed page load shows a retry row at the bottom and keeps the loaded rows.
- The end of the list is signaled by a missing cursor, not by an empty page.

## Paging through the local store

In an [offline-first](offline-first-and-sync.md) design, the list reads from the local database, and the network fills the database page by page. This gives offline scrolling over everything already fetched. It also raises one question the candidate should answer: what happens to stored pages on pull-to-refresh? A common answer is to replace the first page, keep older rows, and remove rows that are older than a set age.

## New items while the user is reading

Inserting new posts at the top moves the content under the user's finger. The usual solution is to hold the new items and show a "new posts" control, which the user taps to scroll up and reveal them.

## What the interviewer probes

- What does the user see when page 3 fails to load?
- How do you avoid duplicate rows if the same post arrives in two pages?
- How does the list restore its scroll position after the process is stopped and restarted?
- What is in the cursor, and who is allowed to read it?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-pagination-and-feeds) covers pagination in its networking module and applies it in the news feed case study.
- [Design a news feed app](../questions/design-a-news-feed-app.md) uses this page in a full question.
- [Grokking Modern API Design Interview](https://www.designgurus.io/course/grokking-modern-api-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-pagination-and-feeds) covers pagination from the server side.
