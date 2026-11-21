# App Architecture Layers

A mobile application is usually divided into four layers: UI, presentation, domain, and data. The layers exist so that a change in one place, such as a new API response format, does not force changes in every screen. In an interview, the layers are step 3 of [the five-step method](../framework.md), and what is written on each box is graded more than the boxes themselves.

## The four layers

| Layer | What lives there | What must never live there |
|---|---|---|
| UI | Views, lists, buttons, animations, layout | Network calls, JSON parsing, product rules |
| Presentation | Screen state and tap handling | SQL, HTTP calls, JSON models |
| Domain | Product rules, use cases, plain models | Platform types, JSON, SQL |
| Data | Repository, local database, network client, model mapping | Screen state, view types, navigation |

A **repository** is the single object in the data layer that the rest of the app asks for data. It decides whether the answer comes from the local store or from the network, so no screen has to know.

The domain layer is optional in a small design. A candidate can say "the rules are thin here, so I am folding the domain layer into the repository," and that sentence is itself a trade-off with a reason.

## The rule that matters most

Dependencies point one way: the UI knows the presentation layer, the presentation layer knows the domain, and the domain knows the data layer through an interface. Nothing in the data layer knows that a screen exists. This is the property that allows each layer to be tested alone.

The second rule is the single source of truth. The screen reads from the local store, and the network only writes into that store. See [offline-first and sync](offline-first-and-sync.md).

## Presentation patterns compared

A presentation pattern decides how the screen and its state are separated. It does not decide storage, networking, or sync.

| Pattern | What it separates | What it costs | When it is worth it |
|---|---|---|---|
| MVC (Model View Controller) | Data from drawing | Rules collect in the controller, which grows large | Small apps with fixed screens |
| MVVM (Model View ViewModel) | State from drawing | One extra class per screen | Almost every screen on both platforms |
| MVI (Model View Intent) | Every event onto one path, all state into one object | More code to edit for each change | Screens where many values change together |

In MVVM, a **view model** holds the state of one screen and exposes it as a stream the view observes. In MVI, every user action becomes an event, a single function turns the old state and the event into a new state, and the view draws that state. This is called unidirectional data flow, because data moves in one direction only.

## One tap, traced

A good way to end the architecture step is to trace one action aloud:

1. The user taps "like".
2. The view model writes the like to the local store, and the heart fills at once.
3. The repository adds a row to the outbox, which is a table of writes waiting to be sent.
4. A background worker sends the row when the network is available.
5. The store row becomes confirmed, and the screen redraws from the store.

## What the interviewer probes

- Why this pattern for this screen, and what would make you choose another?
- Which box holds the truth for this piece of data?
- Where does the mapping from the API model to the screen model happen?
- How would you test the presentation layer without a device?

Two habits lose this step: a template diagram that would fit any app, and a vague "Manager" box that has no stated job.

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-app-architecture-layers) has a full module on client architecture, including modules, navigation, dependency injection, and VIPER and Clean Architecture.
- [Mobile vs backend system design](../cheat-sheets/mobile-vs-backend-system-design.md) shows where these layers sit beside a server design.
