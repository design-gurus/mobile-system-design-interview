# Local Storage

A mobile app has five kinds of on-device storage, and a good design names which data goes in which. The choice depends on three properties of the data: how large it is, how it must be queried, and how secret it is.

## The five stores

| Storage kind | iOS | Android | What it holds | How it is queried |
|---|---|---|---|---|
| Key-value | UserDefaults | DataStore | Settings, a few KB | By key name |
| Relational database | Core Data or SwiftData (both over SQLite) | Room (over SQLite) | The app's records, up to hundreds of MB | Full queries |
| Object store | Realm | Realm | Records stored as objects | Filters; joins are weak |
| Files | FileManager | App-private storage | Photos, video, logs | Only by path |
| Secure store | Keychain | Keystore | Tokens and keys, a few KB | By name |

## Choosing in an interview

| The data | The store | The reason to say aloud |
|---|---|---|
| Feed posts, messages, notes | Relational database | The screen sorts, filters, and pages them |
| The dark mode setting | Key-value | One value, read at launch |
| Downloaded images and video | Files, with paths in the database | Large binary data slows a database |
| The session token | Secure store | It must stay protected even if the device files are copied |

## Common wrong choices

- **Tokens in key-value storage.** That store is a plain file. Secrets belong in the Keychain or behind the Keystore.
- **A whole list as one JSON value.** Every change rewrites the full value, and nothing can be queried.
- **Images inside database rows.** The database grows, and every read of a row pulls megabytes into memory.
- **Unbounded caches.** A cache with no size limit fills the device, and the user's response is to delete the app.

## The client data model

The tables on the device are not a copy of the server's tables. They hold what the screens read, plus fields only the client needs:

| Extra field | Purpose |
|---|---|
| `syncStatus` (pending, confirmed, failed) | Shows the state of an optimistic write |
| `fetchedAt` | Decides when a row is too old to trust |
| `localId` | Identifies a record created offline, before the server assigns an identifier |

## Cache limits and eviction

**Eviction** is the removal of old entries to make room. A common policy is least recently used, which removes the entries that were read longest ago. Every cache in a design should carry two numbers: its maximum size and its maximum age. For example, "the image disk cache is capped at 250 MB and entries expire after seven days."

## Schema migrations

A **migration** is the code that changes existing tables when a new app version needs a different schema. It runs on the user's device, on data the developer cannot inspect, and a failed migration can make the app crash at every launch. Three rules reduce the risk: prefer adding columns to changing them, test each migration against data from every older version, and treat cached server data as disposable, so that it can be dropped and fetched again. Data that exists only on the device, such as outbox rows, must be migrated with care.

## What the interviewer probes

- How large can this store grow, and what removes old data?
- Which fields does the screen read directly?
- What happens to stored data when the user logs out?
- How does this table change in the next version of the app?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-local-storage) covers on-device storage, the client data model, caching, and migrations in its storage module.
- Lesson: [On Device Storage Options](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/on-device-storage-options?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-local-storage), in Grokking Modern Mobile System Design Interview
- Lesson: [Local Schema Migrations](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/local-schema-migrations?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-local-storage), in Grokking Modern Mobile System Design Interview
- [Offline-first and sync](offline-first-and-sync.md) explains how these stores are kept in step with the server.
- [Security and privacy](security-and-privacy.md) covers the secure store in more detail.
