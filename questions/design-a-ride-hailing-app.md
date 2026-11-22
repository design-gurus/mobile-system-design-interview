# Design a Ride Hailing App

A ride hailing product has two apps: one for riders and one for drivers. The interviewer usually asks for the rider app, with one screen in focus: the live trip, where a car moves on a map. The two hard parts are location updates against battery, and positions that are stale by the time they are drawn.

## Requirements to clarify

- The rider app, the driver app, or both? (Propose the rider app, with the driver's location stream described briefly.)
- Which part of the journey: requesting a ride, waiting for the driver, or the trip itself?
- How fresh must the car's position be? Every second, or every few seconds?
- Must the trip screen survive the app going to the background?
- Are payments and ratings out of scope?

A reasonable agreed scope: request a ride, then follow the assigned driver on a map until pickup.

## API and data model

| Channel | Use |
|---|---|
| `POST /v1/rides` | Request a ride, with an idempotency key |
| `GET /v1/rides/{id}` | The full current state of the ride |
| WebSocket or server-sent events | Ride state changes and driver positions while the screen is open |
| Push notification | "Your driver has arrived" when the app is in the background |

The ride is a **state machine**, which means it is always in exactly one of a fixed set of states and moves between them by defined events: requested, matched, driver arriving, in progress, completed, or cancelled. The server owns the state. The device stores the current ride record and the last known driver position with its timestamp.

## Client architecture

The trip screen observes one ride record in the local store. A ride repository writes every update into that record, whether it came from the socket, from a push-triggered fetch, or from the request made when the screen opens. A location provider wraps the platform location service, and the map view draws from the stored position.

## The decisions that matter

**1. The update rate is a battery decision.** In the driver app, the location is read every few seconds while a trip is active and much less often while idle. The rider app receives the position from the server every 3 to 5 seconds and does not need the rider's own location continuously after pickup is set.

**2. Smooth movement from sparse updates.** An update every 4 seconds would make the car jump. The client animates the marker from the old position to the new one over the interval, which is called interpolation. Each position carries a timestamp, and a position older than about 15 seconds is shown as stale, for example by dimming the marker, instead of being presented as current.

**3. Always be able to recover the full state.** Events can be missed. Whenever the screen opens, the app returns to the foreground, or the connection is restored, the client fetches the whole ride with `GET /v1/rides/{id}`. Events are an optimization, and the fetch is what makes the state correct.

**4. A ride request that is safe to retry.** The request carries an idempotency key. A retry after a timeout then returns the existing ride and does not book a second car.

## Failure, offline, and shipping

- **Connection lost mid-trip:** the last position stays visible with a "reconnecting" notice, and the state is fetched again on reconnect.
- **Process stopped:** on relaunch the app asks the server for an active ride and opens the trip screen directly.
- **Location permission denied:** the rider can type a pickup address.
- **Shipping:** behind a flag, watching request success rate, position age at the 90th percentile, and battery use per trip.

## What the interviewer probes

- The driver passes through a tunnel for 40 seconds. What does the rider see?
- How does the driver app keep reporting location with the screen off? (A visible, user-aware background location mode on both platforms.)
- How is the pickup pin kept accurate when the location reading is poor?
- What is different in the driver app's design?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app) includes a complete ride hailing case study in its real-time module.
- Lesson: [Ride Hailing](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/ride-hailing?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app), in Grokking Modern Mobile System Design Interview
- Lesson: [Real Time Transports](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/real-time-transports?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app), in Grokking Modern Mobile System Design Interview
- [Real-time updates](../concepts/real-time-updates.md) and [background work and battery](../concepts/background-work-and-battery.md) cover the two concepts this question depends on.
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app) designs the matching and location backend.
- Free article: [How to Design a Ride Sharing Service](https://www.designgurus.io/blog/ride-sharing-service?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app), a walkthrough of the server side.
- Free answer: [What Is Uber System Design Architecture Like?](https://www.designgurus.io/answers/detail/what-is-uber-system-design-architecture-like?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-ride-hailing-app), an overview of one production architecture.
