# Security and Privacy

Mobile security starts from one assumption: the device and the app are in the hands of the attacker. Anyone can download the app, read its code, watch its network traffic, and copy its files from a compromised device. A design therefore keeps secrets off the device where it can, protects the ones that must be there, and lets the server make every final decision.

## A short threat model

A **threat model** is a list of who might attack, what they want, and how they could get it.

| Threat | Example | Main defense |
|---|---|---|
| Stolen or shared device | Someone opens the banking app on an unlocked phone | Biometric or passcode check before sensitive actions |
| Files copied from the device | Malware or a backup exposes stored data | Secrets in the secure store; sensitive files encrypted |
| Network interception | A hostile Wi-Fi network reads traffic | TLS on every request |
| Reverse engineering | An API key is read from the app binary | No long-lived secrets in the app |
| A modified client | A changed app sends false prices or scores | The server validates everything |

## Storing secrets

| Data | Where it belongs |
|---|---|
| Access and refresh tokens | Keychain on iOS; encrypted with a Keystore key on Android |
| The user's password | Nowhere. The app exchanges it for tokens and discards it |
| API keys for third-party services | On the server, which calls the service for the app |
| Cached personal data | The app's private storage, cleared on logout |

Both secure stores can keep keys in hardware, so that the key material never enters the app's memory.

## Authentication on a client

Most apps use two tokens. The **access token** is sent with each request and expires quickly, often within an hour. The **refresh token** lives longer and is used only to get a new access token. Three details matter in a design:

1. When ten requests fail at once with an expired token, only one refresh should run, and the others wait for it.
2. When the refresh fails, the user is logged out and local data is cleared.
3. The server can revoke a refresh token, which is how "log out of all devices" works.

## Transport security

Every request uses **TLS** (Transport Layer Security), the protocol that encrypts traffic between the app and the server. Both platforms block plain HTTP by default. **Certificate pinning** goes one step further: the app accepts only certificates it was built to trust. Pinning stops interception by a network that controls a trusted certificate, but it has a cost. If the server's certificate changes and old app versions hold the old pin, those versions stop working until the user updates. A design that pins should say how the pins are rotated.

## The server decides

Any check made only in the app can be removed by a modified app. Prices, permissions, balances, and limits are verified on the server. The client's checks exist to give fast feedback, not to enforce rules.

## Privacy

| Practice | What it means in a design |
|---|---|
| Ask for the least | Approximate location if exact is not needed; a photo picker instead of full library access |
| Ask at the moment of use | The camera permission appears when the user taps "scan", with a reason |
| Work when denied | The feature degrades; the app does not block |
| Keep personal data out of logs | Analytics events and crash reports carry identifiers, not names or message text |

## What the interviewer probes

- Where is the token stored, and what happens on logout?
- What stops a modified client from sending a lower price?
- What is the cost of certificate pinning, and how do you rotate a pin?
- Which permissions does this feature need, and what happens when each is denied?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-security-and-privacy) covers threat models, secure storage, transport security, and authentication, with a peer-to-peer payment flow as the capstone.
- Lesson: [Threat Model and Secure Storage](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/threat-model-and-secure-storage?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-security-and-privacy), in Grokking Modern Mobile System Design Interview
- Lesson: [Transport Security and Authentication](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/transport-security-and-authentication?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-security-and-privacy), in Grokking Modern Mobile System Design Interview
- [Design a mobile payments flow](../questions/design-a-mobile-payments-flow.md) applies this page.
- [Local storage](local-storage.md) compares the secure store with the other stores.
- Free answer: [What Is TLS/SSL and How Does HTTPS Secure Data in Transit?](https://www.designgurus.io/answers/detail/what-is-tlsssl-and-how-does-https-secure-data-in-transit-in-a-system?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=concepts-security-and-privacy), background for the transport security section.
