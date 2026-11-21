# Offline-First and Sync

An offline-first app treats the local store as the source of truth: every screen reads from the device, and the network is only a way to bring that store up to date. The design then needs three more parts, which are a queue for writes made offline, a way to fetch only what changed, and a rule for conflicts.

## Reads: the screen never waits for the network

1. The screen opens and draws what the local store holds.
2. The repository asks the server for newer data in the background.
3. The result is written into the store.
4. The screen redraws, because it observes the store.

With this flow, offline reading needs no extra code. The same path also handles refresh and errors, since a failed request simply leaves the stored data on screen.

## Writes: optimistic updates and the outbox

An **optimistic write** shows the result of an action before the server confirms it. The **outbox** is a local table of writes that have not been sent yet.

| Step | What happens |
|---|---|
| The user acts | The change is written to the local store with a "pending" status |
| Same transaction | A row describing the change is added to the outbox |
| Network available | A background worker sends outbox rows in order, each with an idempotency key |
| Server confirms | The row is removed and the record becomes "confirmed" |
| Server rejects | The local change is reversed, and the user is told why |

The idempotency key matters because the worker cannot tell a lost request from a lost response. See [networking and API contracts](networking-and-api-contracts.md).

## Fetching only what changed

Downloading the whole data set on every sync wastes data and battery. In a **delta sync**, the client sends a token that marks the last change it received, and the server returns only the records changed since then. Deleted records need a marker, often called a tombstone, or the client would keep them forever.

## Conflict resolution choices

A conflict occurs when two devices change the same record before either has synced.

| Approach | What it keeps | What it loses | When it fits |
|---|---|---|---|
| Last write wins | The newest whole record | Every other edit | Settings, data with one writer |
| Field-by-field merge | Both edits when they touch different fields | One edit on the same field | Contacts, profiles, tasks |
| Logical clock | A record of what each device had seen | Nothing; it only detects a real conflict | Deciding whether a merge is needed |
| CRDT | Both edits inside one field | Meaning, when two people rewrite the same line | Shared text and drawings |
| Ask the user | Whatever the person picks | The user's time | Rare conflicts on the user's own work |

A **CRDT** (conflict-free replicated data type) is a data structure built so that copies edited separately can always be merged without a conflict.

One caution applies to last write wins: the clock on a device can be wrong or changed by the user, so the server should assign the order, not the device.

## Saying the rule in two lines

An interviewer wants the rule and its cost, for example: "Notes merge field by field, so a title edit and a body edit both survive. If two devices edit the body, the later one wins and the other is saved as a copy."

## What the interviewer probes

- What does the user see when a queued write is rejected two hours later?
- How are deletes handled when one device was offline for a week?
- What happens to the outbox when the user logs out?
- How does the local schema change between app versions without losing queued writes?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-offline-first-and-sync) has a module on storage, caching, and offline behavior, ending with an offline notes design with sync.
- [Design an offline notes app](../questions/design-an-offline-notes-app.md) applies this page to one question.
- [Local storage](local-storage.md) covers where the store and the outbox live.
- Free article: [CRDTs Explained: Conflict-Free Replicated Data Types for Eventual Consistency](https://www.designgurus.io/blog/crdts-for-eventual-consistency?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-offline-first-and-sync), one approach to merging conflicting edits.
