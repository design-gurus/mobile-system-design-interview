# Design a Chat App

A chat client shows a list of conversations and, inside each one, a stream of messages that arrive in real time. The two hard parts are keeping messages in the correct order across devices, and letting the user send while the network is down.

## Requirements to clarify

- One-to-one chat only, or groups as well?
- Text only, or images and other attachments?
- Must old messages be readable offline, and must sending work offline?
- Are delivery and read receipts in scope? Typing indicators?
- Is end-to-end encryption required? (It changes the design enough that most interviewers leave it out.)

A reasonable agreed scope: one-to-one text chat, offline reading and queued sending, and delivery receipts.

## API and data model

| Channel | Use |
|---|---|
| WebSocket | New messages, receipts, and typing events while the app is open |
| `GET /v1/conversations/{id}/messages?before=&limit=30` | Older history, paged backward with a cursor |
| `GET /v1/sync?since={token}` | Everything missed while disconnected |
| Push notification | A signal to sync when the app is in the background |

On the device:

| Table | Key fields |
|---|---|
| `conversations` | id, title, last message preview, unread count |
| `messages` | server id, client id, conversation id, sender, body, server sequence, status |
| `outbox` | client id, payload, attempt count |

The message status moves through sending, sent, delivered, and read.

## Client architecture

The conversation screen observes the `messages` table. A connection manager owns the WebSocket and writes every incoming event into the database. A send worker drains the outbox. No screen talks to the socket directly, so the UI works the same whether a message arrived by socket, by sync, or after a push.

## The decisions that matter

**1. Two identifiers per message.** The client creates a unique client id when the user taps send. The server assigns the server id and a sequence number. The client id serves as the idempotency key, so a retried send does not create a duplicate, and it lets the client match the server's confirmation to the pending row.

**2. The server decides the order.** Device clocks can be wrong, so messages are sorted by the server's sequence number within a conversation. A pending message has no sequence yet and is shown at the bottom until it is confirmed.

**3. Catch-up after every gap.** A socket delivers nothing while it is closed. On reconnect, and whenever the app returns to the screen, the client calls sync with the last token it holds and writes the result into the store. Push carries only a signal, because push delivery is not guaranteed. See [real-time updates](../concepts/real-time-updates.md) and [push notifications](../concepts/push-notifications.md).

## Failure, offline, and shipping

- **Offline send:** the message appears at once with a "sending" mark and waits in the outbox.
- **Send fails after retries:** the message is marked as failed with a "tap to retry" action, and it is never removed silently.
- **Socket drops:** the client reconnects with a growing delay, then syncs.
- **Shipping:** a flag guards the new transport, and the numbers to watch are send success rate, time from send to delivered, and duplicate message count.

## What the interviewer probes

- Two messages are sent offline and the second reaches the server first. What order does the other person see?
- The user reads a message on a tablet. How does the phone clear its notification and unread count?
- How are 50,000 stored messages kept from slowing the conversation screen?
- What changes for a group of 500 members?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-chat-app) includes a complete chat app case study with ordering, receipts, and media.
- [Offline-first and sync](../concepts/offline-first-and-sync.md) explains the outbox this design depends on.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-chat-app) designs the chat backend.
