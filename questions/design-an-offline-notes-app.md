# Design an Offline Notes App

A notes app must work fully without a network: the user creates, edits, and deletes notes on a plane, and the changes reach their other devices later. This question is the purest test of sync. The hard parts are sending local changes safely, fetching only what changed, and deciding what happens when two devices edit the same note.

## Requirements to clarify

- One user with several devices, or notes shared between users? (Sharing raises the difficulty a great deal.)
- Plain text, or rich text and attachments?
- How should a conflict be resolved, and is losing an edit ever acceptable?
- How many notes, and how large is one?
- Is search in scope?

A reasonable agreed scope: one user, several devices, text notes of a few KB each, and no silent loss of edits.

## API and data model

| Call | Purpose |
|---|---|
| `GET /v1/notes/changes?since={token}` | Notes changed or deleted since the token, plus a new token |
| `POST /v1/notes/batch` | Send local changes, each with the version the device last saw |

On the device:

| Field in `notes` | Purpose |
|---|---|
| `id` | Created on the device as a UUID, so a note can exist before the server knows it |
| `title`, `body` | The content |
| `baseVersion` | The server version this device last received |
| `isDirty` | True when there are local changes not yet sent |
| `isDeleted` | A tombstone, so the delete can be synced |
| `updatedAt` | For sorting on screen only |

A **UUID** is an identifier generated at random that is unique in practice without asking a server.

## Client architecture

The editor and the list read and write the local database only. Every edit succeeds at once, because it is a local write. A sync engine in the data layer runs when the app opens, after edits (with a short delay to group keystrokes), and when a push signal arrives. It runs two phases in order: push local changes, then pull remote changes.

## The decisions that matter

**1. Versions detect conflicts.** Each change sent to the server carries the note's `baseVersion`. If the server's current version is the same, the change is accepted and the version increases. If it differs, another device changed the note first, and that is a conflict. This check does not depend on device clocks.

**2. The conflict rule.** The options are compared on the [offline-first and sync](../concepts/offline-first-and-sync.md) page. A sound answer for notes is to merge field by field, so that a title edit and a body edit both survive. When both devices changed the body, the app keeps the server's version and saves the local one as a separate "conflicted copy" note. No text is lost, and the user decides. A CRDT would merge text inside the body, at a much higher cost to build.

**3. Deletes are data.** A deleted note is kept as a tombstone until the server confirms it, and the server keeps its own tombstone so that a device offline for a month still learns of the delete. One case needs a stated rule: one device deletes a note while another edits it. Keeping the edit is the safer choice.

**4. Delta sync.** The change token means a device with 5,000 notes downloads only the three that changed.

## Failure, offline, and shipping

- **Offline:** everything works; a small indicator shows unsynced changes.
- **Sync interrupted:** both phases are safe to repeat, because each change carries its note id and base version.
- **Logout with unsynced notes:** the app warns the user before clearing local data.
- **Shipping:** the local schema will change in later versions, so migrations must preserve dirty notes. Watch sync success rate, conflict rate, and time from edit to visible on a second device.

## What the interviewer probes

- A device comes back after 60 days offline with 40 edited notes. What happens?
- How do you avoid syncing on every keystroke?
- How would the design change if two people could edit one note at the same moment?
- How is search implemented over notes stored on the device?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-an-offline-notes-app) ends its storage module with an offline notes design, after lessons on the outbox, delta sync, conflict resolution, and local schema migrations.
- [Local storage](../concepts/local-storage.md) covers the database and its migrations.
