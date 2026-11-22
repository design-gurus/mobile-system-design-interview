# Release and Feature Flags

A mobile release cannot be taken back. Once a version is installed, it stays on the user's device until the user updates, and a fix must pass store review before anyone can receive it. A mobile design therefore separates two events that are one event on a server: shipping the code, and turning the feature on.

## Why mobile releases are different

| | Server deploy | Mobile release |
|---|---|---|
| Time to reach users | Minutes | Hours to days, including store review |
| Rollback | Redeploy the old version | Not possible for installed copies |
| Versions in use at once | Usually one or two | Many, some of them years old |
| Who decides to update | The team | Each user |

## Feature flags

A **feature flag** is a value fetched from the server that turns a piece of the app on or off without a new release. The code for the feature ships inside the app in a disabled state, and the server enables it later.

| Use | How it works |
|---|---|
| Gradual launch | Enabled for 1 percent of users, then 10, then 50, then all |
| Kill switch | A flag that turns a broken feature off for everyone within minutes |
| Experiment | Half of the users see version A and half see version B, and a metric is compared |

A **kill switch** is the flag a design should always name. It is the mobile answer to the question "what if this is broken in production?"

Three details make flags safe:

- Each flag has a default in the app, used when the flag values cannot be fetched.
- Flag values are cached on the device, so the app does not wait for the network at launch.
- A user keeps the same value for the length of a session, so a screen does not change shape while it is in use.

## Staged rollouts

Both stores can release a new version to a share of users first: phased release on the App Store and staged rollout on Google Play. A typical sequence runs over about a week. The team watches crash rate and key metrics at each stage, and it pauses the rollout if they get worse. Pausing does not remove the version from devices that already have it, which is why the kill switch is still needed.

## Old versions and forced upgrade

The server must keep working for old clients. When that becomes impossible, for example after a security fix, the app needs a way to require an update.

| Mechanism | Behavior |
|---|---|
| Soft prompt | "A new version is available", and the user can dismiss it |
| Forced upgrade | A blocking screen with a link to the store |

The check is a small call at launch that compares the app's version with a minimum the server controls. This call has to exist from the first release, because it cannot be added to versions already installed.

API rules that support old versions are covered in [networking and API contracts](networking-and-api-contracts.md).

## Observability

A rollout decision needs data from devices. A design should name the numbers it watches:

- Crash-free sessions, compared with the previous version
- Time to the first useful screen
- The success rate of the feature's main action, such as "message sent"
- API error rate by app version

## The closing sentence of a round

Step 5 of [the five-step method](../framework.md) ends with shipping. A complete answer sounds like this: "The feature ships dark behind a flag. I enable it for 1 percent, watch crash rate and send success for a day, and then widen it. If it breaks, the kill switch turns it off without a release."

## What the interviewer probes

- The feature is crashing for 5 percent of users. What do you do in the next ten minutes?
- How does a version from two years ago handle the new API response?
- What does the app do if the flag service is unreachable at launch?
- How do you remove a flag once the feature is fully launched?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-release-and-feature-flags) covers releases, forced upgrades, feature flags, kill switches, and mobile observability, and designs a feature flag SDK.
- Lesson: [Releases, Forced Upgrade, and Versioning](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/releases-forced-upgrade-and-versioning?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-release-and-feature-flags), in Grokking Modern Mobile System Design Interview
- Lesson: [Feature Flags, Kill Switches, and Rollouts](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/feature-flags-kill-switches-and-rollouts?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-release-and-feature-flags), in Grokking Modern Mobile System Design Interview
- [Common mistakes](../cheat-sheets/common-mistakes.md) explains why skipping this step costs a signal.
