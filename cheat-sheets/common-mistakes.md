# Common Mistakes in the Mobile System Design Round

Most candidates who fail this round do not fail on knowledge. They lose signals through pacing, vague diagrams, and unstated decisions. A **signal** is a piece of evidence the interviewer writes down, and a decision that is not said aloud produces no signal.

## The signals and how each is lost

| Signal | Where it is recorded | The mistake that loses it |
|---|---|---|
| Scoping | Requirements | Jumping to the screen or the code |
| The big picture | Architecture | A template diagram that would fit any app |
| Modular thinking | Architecture | Vague "Manager" boxes |
| Trade-offs with reasons | Every decision | Naming a pattern without saying why |
| Platform depth | Deep dive | Inventing backend detail instead of client detail |
| Failure awareness | Failure and shipping | No failure modes at all |
| Communication | The whole round | Waiting to be asked, and indecision |

## The ten mistakes

**1. Spending 15 minutes on requirements.** The budget is 5. A candidate who starts the diagram at minute 30 has no time for the deep dive, where depth is graded.

**2. Designing the whole product.** "Design Instagram" must be narrowed to one screen and two actions within the first two minutes, and the candidate should propose the smaller scope.

**3. Drawing the backend.** Load balancers and database shards earn nothing unless the interviewer asked for them. The scope question comes first: the app only, the app and the API, or the server as well?

**4. Drawing layers before data.** The layers depend on what moves through them. Stating the calls, the page size, and the local tables first prevents redrawing the diagram later.

**5. The screen reads from the network.** Without a local store as the source of truth, offline reads, refresh, and error handling each need separate handling, and the follow-up "make it work offline" breaks the design.

**6. A pattern with no reason.** "I will use MVVM" is a label. "MVVM, because the screen state must survive rotation and be testable without a device" is a decision.

**7. Ignoring the process lifecycle.** The operating system can stop the app at any time. A design should say what is saved, and what the user sees on return.

**8. Assuming background work runs on schedule.** Scheduled work runs when the system allows it. A design that needs a sync every five minutes to be correct is incorrect.

**9. Treating push as reliable.** A push can be delayed or dropped. It should signal the app to sync and should not carry the data.

**10. No shipping plan.** A mobile release cannot be taken back. Ending without a staged rollout, a kill switch, and two or three numbers to watch leaves the last signal empty.

## Weak and strong versions of the same sentence

| Weak | Strong |
|---|---|
| "We cache the data." | "The posts table holds 24 hours of feed, capped at 500 rows, and the screen reads only from it." |
| "We use pagination." | "Cursor paging with 20 items, because new posts arrive while the user scrolls." |
| "We handle errors." | "A failed page load keeps the loaded rows and adds a retry row at the bottom." |
| "We retry." | "Three retries with a growing delay, and the write carries an idempotency key." |
| "It works offline." | "Reads come from the local store, and likes wait in an outbox until the network returns." |

The strong versions share one property: each contains a number, a mechanism, or a reason.

## Pacing checkpoints

| Design minute | The candidate should be |
|---|---|
| 5 | Finished with scope and constraints |
| 13 | Finished with calls and local tables |
| 23 | Finished with the diagram and one traced action |
| 40 | Starting failure, offline, and shipping |

If a checkpoint is missed, time should come from the API step or the second deep dive, never from the close.

## A recovery sentence

When a candidate notices a mistake in the middle of the round, saying so is a positive signal: "I drew the screen reading from the network. That breaks offline reads, so I am changing it to read from the local store."

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-common-mistakes) teaches the signals interviewers record and grades three sample answers against them.
- Lesson: [Interview Signals and Eight Common Mistakes](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/signals-and-the-eight-ways-to-lose-them?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-common-mistakes), in Grokking Modern Mobile System Design Interview
- Lesson: [Capstone: Grading Three Answers](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/capstone-grading-three-answers?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-common-mistakes), in Grokking Modern Mobile System Design Interview
- [The five-step method](../framework.md) gives the time budget these checkpoints come from.
