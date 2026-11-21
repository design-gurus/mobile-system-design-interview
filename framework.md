# The Five-Step Method for a Mobile System Design Round

A mobile design round should be spent in five steps, in this order: requirements, API and data model, client architecture, deep dive, then failure and shipping. The order matters as much as the content, because most candidates who fail this round run out of time before they reach the part that is graded for depth.

## The time budget

A round booked for 45 minutes holds about 35 minutes of design. Introductions take the first few minutes, and the last five belong to the candidate's own questions. The budget below counts design minutes, and the clock starts when the question is given.

| Step | Minutes | Finished when the candidate has |
|---|---|---|
| 1. Requirements | 5 | Three to five features, three constraints, and one line on what is out of scope |
| 2. API and data model | 8 | Two or three calls with real fields, a paging choice, and the local schema |
| 3. Client architecture | 10 | One diagram, with each box named for its job and its data |
| 4. Deep dive | 17 | Two or three hard parts, taken down to the mechanism |
| 5. Failure, offline, and shipping | 5 | Failure behavior, offline behavior, and a release plan |

If the round starts late, the lost minutes should come from the API step and from the second deep dive. They should never come from the last step.

## Step 1: Requirements

The first question is about scope: does the design cover the application only, the application and the API, or the server as well? The second task is to narrow the product. "Design Instagram" cannot be answered in 45 minutes, while "design the feed screen, with posting and liking" can.

Three constraints change a mobile design, and each deserves one question.

- Must the feature work with no network?
- Must anything update in real time?
- Must it run on low-end devices and slow networks?

Three numbers are also worth asking for: the size of one image, the number of items in a page, and how often the list changes while the user is reading it.

## Step 2: API and data model

The data comes before the architecture. The **schema** is the list of tables and fields the device stores, and once the shape of one page of results is known, the schema and the layers are mostly decided.

The candidate should name the calls the screen needs, the fields in each response, the paging style, and the tables on the device. A feed example: 20 posts per page at about 2 KB of text each is about 40 KB per page, with about 6 MB of images behind it.

## Step 3: Client architecture

The **source of truth** is the one place a piece of data is held, so that every screen reads from it. In most mobile designs it is the local store. The screen never reads from the network directly. The network writes into the local store, and the screen redraws from the store.

The step should end with one user action traced aloud from the tap to the pixel. See [app architecture layers](concepts/app-architecture-layers.md).

## Step 4: Deep dive

This is the largest step. The candidate should pick the part with the most ways to fail and say why, then give up that choice without argument if the interviewer prefers another part.

| Question | The two hard parts |
|---|---|
| Feed | Paging with changing data, and offline reads |
| Chat | Message ordering, and sending with no network |
| Photo or file upload | Resuming an interrupted upload, and background limits |
| Maps or live tracking | Location updates against battery, and stale positions |
| Any library | The public interface, and cancellation |

Depth means naming the mechanism, the data, the thread each piece of work runs on, and the failure. It does not mean adding boxes.

## Step 5: Failure, offline, and shipping

The same three parts close every round:

1. **Failure.** For each main part, one sentence on what breaks, what the user sees, and what happens to unfinished work.
2. **Offline.** What the user can see and do with no network.
3. **Shipping.** A mobile release cannot be taken back, so the feature goes to a small share of users first, behind a switch the server can turn off.

The final sentence names the numbers to watch after launch, such as crash rate and time to the first useful screen. See [release and feature flags](concepts/release-and-feature-flags.md).

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=framework) teaches this method with three graded sample answers and a full mock interview.
- [Common mistakes](cheat-sheets/common-mistakes.md) lists the errors that break the time budget.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=framework) teaches the matching method for the backend round.
