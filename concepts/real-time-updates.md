# Real-Time Updates

A mobile app has five ways to learn that something changed on the server: polling, long polling, server-sent events, WebSockets, and push notifications. The right choice depends on how fresh the data must be, which direction messages flow, and what the battery can afford. Most real products combine two of them: one transport while the app is on screen and push when it is not.

## The five transports

| Transport | How it works | Direction |
|---|---|---|
| Polling | The client asks on a timer | Client to server |
| Long polling | The server holds the request open until it has news | Server to client, one message per request |
| Server-sent events (SSE) | One HTTP response that stays open and streams events | Server to client only |
| WebSocket | One connection that carries messages both ways | Both |
| Push notification | The platform service delivers through the system connection | Server to client |

## Comparing them on a phone

| Transport | Battery cost | Infrastructure cost | When the app is in the background |
|---|---|---|---|
| Polling | High, because the radio wakes on every timer | Low, plain HTTP | Stops |
| Long polling | Medium | Medium, one held connection per user | Stops |
| Server-sent events | Medium | Medium | Stops, the connection is closed |
| WebSocket | Depends on how often keepalive messages are sent | High, open connections and routing between servers | Closes shortly after the app leaves the screen |
| Push notification | Low, one connection shared by all apps | Low, the platform carries it | The only one that still works |

## Choosing

| Requirement | Reasonable choice |
|---|---|
| A dashboard that may be 30 seconds old | Polling every 30 seconds while the screen is visible |
| Live scores or a price ticker, read-only | Server-sent events |
| Chat, typing indicators, live location | WebSocket in the foreground, push in the background |
| "Your order has shipped" | Push only |

The decision should be stated with its reason: "Messages flow both ways and must arrive within a second, so I use a WebSocket while the chat is open. In the background the socket closes, so a push tells the app to sync."

## Reconnection

Mobile connections drop often, for example when the device moves from Wi-Fi to cellular. A design needs three parts for this:

1. **Detect.** Keepalive messages reveal a dead connection that the system has not reported.
2. **Reconnect.** The client retries with a growing delay and some randomness, so that 100,000 clients do not reconnect in the same second after an outage.
3. **Catch up.** The client sends the identifier of the last event it received, and the server returns what was missed.

The third part is the one candidates forget. A socket delivers nothing while it is closed, so a design without a catch-up step loses messages.

## Returning to the screen

When the app comes back to the foreground, it should not trust what is on screen. It reconnects, runs the catch-up request, writes the result into the local store, and lets the screen redraw. This is the same path as an ordinary sync, which is why the local store as the source of truth simplifies real-time features too.

## What the interviewer probes

- What happens to messages sent while the socket was closed?
- How does the client know its connection is dead?
- How many open connections does this need on the server, and is that acceptable?
- Would polling be good enough here? (Often it is, and saying so shows judgment.)

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-real-time-updates) compares these transports in its networking module and applies them to chat, ride hailing, and live streams.
- [Push notifications](push-notifications.md) covers the background channel.
- [Design a chat app](../questions/design-a-chat-app.md) uses a WebSocket with push as the fallback.
- Free answer: [What Is Polling vs Long-Polling vs Webhooks?](https://www.designgurus.io/answers/detail/what-is-polling-vs-long-polling-vs-webhooks?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-real-time-updates), a comparison of three delivery methods.
- Free answer: [What Is HTTP Long Polling?](https://www.designgurus.io/answers/detail/what-is-http-long-polling?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-real-time-updates), a closer look at one of them.
