# Mobile vs Backend System Design

In a backend round the candidate designs the servers behind a product. In a mobile round the candidate designs the application on one device, and the server is normally given as something that already works. One question separates the two: which side of the network does the candidate own?

## The two rounds side by side

| | Backend system design | Mobile system design |
|---|---|---|
| What is designed | Services, databases, queues, caches | The app: layers, local store, network client |
| The central difficulty | Many users at the same moment | One user on a weak network with a small battery |
| The main numbers | Requests per second, storage, replicas | Image size, items per page, memory, frame time |
| Source of truth | The server database | The server for shared data; the local store for what the screen reads |
| Failure to plan for | A machine or a region fails | The network drops, or the system stops the process |
| Scaling | Add servers | Not possible; the device is fixed |
| Deployment | Deploy and roll back in minutes | A release that cannot be taken back |
| State | Kept in databases, services are stateless | Kept on the device, and must survive process death |
| Resource limits | Cost | Battery, memory, storage, and the user's data plan |

## The same feature from both sides

Consider a screen that lists nearby orders for a delivery rider.

| The backend answer covers | The mobile answer covers |
|---|---|
| How 3,000 requests per second are spread over servers | What the screen shows in the first 200 milliseconds |
| Where the true copy of each order is stored | Which orders are kept on the device, and for how long |
| How replicas stay in agreement | How the list is paged, and how photos are loaded per row |
| What happens when a database node fails | What happens when the rider accepts an order with no signal |

Nothing on the server does the work in the right column. That work exists because a person is watching a screen while the network is slow.

## Estimation is different

A backend round estimates capacity: daily users, requests per second, and storage per year. A mobile round rarely needs those sums. The numbers worth estimating are the ones that set client decisions:

| Number | The decision it sets |
|---|---|
| Size of one image | The memory and disk cache sizes |
| Text size of one item | The page size and the response size |
| How often the list changes | The paging style and the refresh policy |
| Size of one upload | Whether the transfer must be resumable and system-owned |

## Skills that transfer, and skills that do not

| Transfers from backend preparation | New for the mobile round |
|---|---|
| API design, pagination, idempotency | Presentation patterns and unidirectional data flow |
| Caching and eviction | Process death and state restoration |
| Consistency and conflict resolution | Background execution limits |
| Retry with backoff | Push delivery, permissions, and deep links |
| Trade-offs stated with reasons | Staged rollouts, kill switches, and forced upgrades |

## A common error in each direction

A backend engineer in a mobile round tends to draw load balancers and database shards, which the interviewer did not ask for. A mobile engineer asked a short server follow-up tends to invent backend detail without confidence. In both cases the better answer is to ask the scope question first, and to state plainly where one's own depth ends.

## When the mobile round includes the server

Senior candidates are sometimes asked a full-stack follow-up, such as "now sketch the service behind this API." A brief, correct outline is enough: the service, its database, a cache, and a queue for slow work. The grading remains centered on the client.

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-mobile-vs-backend-system-design) opens with this comparison and covers the full-stack follow-up in its interview playbook.
- Lesson: [Mobile System Design vs Backend System Design](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/mobile-system-design-vs-backend-system-design?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-mobile-vs-backend-system-design), in Grokking Modern Mobile System Design Interview
- Lesson: [The Full Stack Follow Up](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/the-full-stack-follow-up?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-mobile-vs-backend-system-design), in Grokking Modern Mobile System Design Interview
- [Grokking the System Design Interview](https://www.designgurus.io/course/grokking-the-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-mobile-vs-backend-system-design) is the course for the backend round.
- [Grokking System Design](https://github.com/design-gurus/grokking-system-design) is the free sibling guide to the backend round.
- Free answer: [Mobile vs Backend System Design Interview: What Changes?](https://www.designgurus.io/answers/detail/mobile-vs-backend-system-design-interview-what-changes?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=cheat-sheets-mobile-vs-backend-system-design), the same comparison in article form.
