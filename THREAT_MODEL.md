# Threat Model

This document describes what XTime protects against today, what it doesn't, and what's planned. It's written to be read honestly, not to make the software look better than it is. XTime is alpha software and has not had a professional security audit. Treat every claim here as "true as far as we know," not "guaranteed."

If you find something here that's wrong, incomplete, or outdated, please open an issue. This document is a living one and gets less accurate the longer it goes unchallenged.

---

## What XTime is trying to protect

- **Message content** — what you actually said, and to whom.
- **Message integrity and authenticity** — that a message really came from who it claims to, unmodified.
- **Identity** — who you are on the network, decoupled from a phone number, email, or any other real-world identifier.
- **Metadata** — as much as reasonably possible, who's talking to whom, and when.
- **Local data at rest** — your message history and keys, if your device is lost, stolen, or seized.

## Who we're assuming might attack this

- A passive network observer (ISP, Wi-Fi operator, anyone watching traffic) trying to see who you're talking to or what you're saying.
- A malicious or compromised peer, trying to impersonate someone else or tamper with messages.
- Someone with temporary or permanent physical access to your device, trying to read stored messages or steal your identity key.
- An entity trying to compel a "provider" to hand over data. (There isn't one to compel, that's the point, but see the caveats below.)

We are **not** currently modeling nation-state-level global traffic analysis, and we're not claiming protection against an adversary who controls a large fraction of the Tor network. If your threat model includes that level of adversary, XTime in its current alpha state is not the right tool yet, full stop.

---

## Current protections

### No central server
Messages route peer-to-peer over Tor Hidden Services v3. There's no server-side component that stores messages, metadata, or account information, because there's no server at all. This removes an entire category of risk: there's nothing to breach, no company that can be compelled to hand over a database, no policy that can quietly change.

**Caveat:** this protects against *server-side* compromise. It does not protect you if your own device is compromised, or if the person you're talking to shares your conversation with someone else.

### Identity via Ed25519 keypairs, not accounts
Your identity is a cryptographic keypair generated locally on first launch. There's no sign-up step, no phone number, no email tied to it. Nothing to leak in a breach because there's no account database anywhere.

### Signed messages
Every message is signed with the sender's private key and verified by the recipient using their public key. This closes a specific, real vulnerability that existed earlier in development: a sender identity field that could be spoofed by a malicious or compromised peer. Signing means a message claiming to be from someone can be cryptographically checked, not just trusted at face value.

### TOFU identity pinning, shown explicitly
The first time you connect to a contact, XTime shows you their key fingerprint and asks you to confirm it, rather than silently trusting whatever key shows up. This is Trust On First Use: it protects you *after* that first verified connection, assuming you actually checked the fingerprint against something you trust (in person, a separate channel, etc.). It does not protect the very first exchange if that exchange itself is intercepted or spoofed, verification is only as strong as the channel you used to confirm it.

### Encryption at rest
Local message history is encrypted on disk rather than stored as plaintext, so a lost or stolen device doesn't hand over your conversation history for free.

---

## Known limitations (current status → planned resolution)

| Area | Current status | Planned |
|---|---|---|
| **Retry queue storage** | `retry_queue.json`, used for messages pending delivery, is currently stored in plaintext. This is inconsistent with the encrypted-at-rest design used elsewhere and is a known gap. | Bringing retry queue storage in line with the rest of the encrypted-at-rest model. Tracked as an open issue, contributions welcome. |
| **Windows path handling** | Runtime paths aren't yet using Flet's proper app storage location, which affects reliability of a packaged Windows build. | Migrating to `FLET_APP_STORAGE_DATA` for runtime paths as part of the Windows build work ahead of beta. |
| **Tor binary discovery on Windows** | Locating `tor.exe` isn't yet robust for a packaged executable context. | Resolving Tor binary discovery relative to the running executable, part of the same pre-beta packaging effort. |
| **Independent security audit** | Has not happened. Everything in this document reflects our own understanding of the code, not third-party verification. | Planned after beta, once the codebase and packaging are stable enough for an audit to be worth the cost. Will be linked here when complete, along with whatever it finds, good or bad. |
| **Traffic analysis resistance** | XTime relies on Tor's protections. It does not currently add anything beyond that (e.g. cover traffic, timing obfuscation) to resist a well-resourced adversary doing traffic correlation. | Not currently planned for the alpha/beta timeline. Would require significant design work and is being tracked as a longer-term research question, not a near-term fix. |
| **Device compromise** | If your device is compromised (malware, physical access while unlocked, etc.), XTime cannot protect your keys or message content. No software can fully solve this. | Out of scope for the app itself. Guidance on device hygiene may be added to documentation, but this isn't something XTime can fix in code. |
| **Metadata from Tor circuit behavior** | Standard Tor metadata exposure applies (e.g. that you're using Tor at all may be visible to a local network observer, even if content and destination aren't). | No change planned beyond what Tor itself provides; using bridges is recommended in networks where Tor usage itself needs to be concealed. |
| **First-contact verification** | TOFU pinning depends entirely on the user actually checking the fingerprint through a trusted out-of-band channel. If skipped, there's no protection against a spoofed first contact. | Considering clearer in-app warnings when a fingerprint is confirmed without cross-checking, no hard technical fix exists for this without adding friction elsewhere. |

---

## What we deliberately chose not to do (and why)

- **No recovery mechanism for lost keys.** If you lose your device or your key, there's no "forgot password" flow, because there's no server that could offer one. This is a direct consequence of the no-server design, not an oversight. Back up your keys if losing access would matter to you.
- **No usage analytics or crash telemetry.** We'd genuinely find bug reports easier with automatic crash logs. We're not adding it, because it's exactly the kind of quiet trust-requiring feature this project exists to avoid. Bug reports have to come from you, manually, on purpose.

---

## Reporting a gap in this document

If you think something here is wrong, missing, or overstates what XTime actually protects against, please open an issue (or use the process in [SECURITY.md](./SECURITY.md) if it's an active vulnerability rather than a documentation gap). This document is only useful if it stays honest.
