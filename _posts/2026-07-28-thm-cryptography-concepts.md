---
title: "TryHackMe: Cryptography Concepts — Learning Notes"
description: "My notes on the Cryptography Concepts room — plaintext vs ciphertext, symmetric vs asymmetric encryption, certificates, and how they combine to power HTTPS."
date: 2026-07-28 09:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [cryptography, encryption, symmetric, asymmetric, tls]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab drag-drop answers, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Cryptography Concepts — [link](https://tryhackme.com/room/cryptographyconcepts)
- **Difficulty:** Easy
- **What it teaches:** The core vocabulary of encryption, the split between symmetric and asymmetric approaches, and why every secure website ends up using both at once.

This is a *concepts* room — no target machine, just ideas built through analogies and one small cipher exercise, following the CIA Triad and Data Encoding rooms. It frames everything around a scenario I know from the inside: a clinic pushing patient files out to specialists and insurers over the open internet. My biggest takeaway wasn't the encryption — I'd met most of it — but a correction to my own model: **scrambling data keeps it secret, but that alone doesn't stop someone tampering with it.**

## Why cryptography matters (the padlock)

The padlock signals **TLS** (Transport Layer Security — the layer that turns `http` into `https`). Two things happen to the data. For **confidentiality**, it's encrypted into unreadable junk, crosses the network that way, and is only decrypted into a readable message at my browser. For **integrity**, TLS also attaches a tamper-check, so any change in transit is detected and rejected. Drop the *s* and a man-in-the-middle can both read the traffic and rewrite it.

## Symmetric encryption: one shared secret

Picture a lockbox and two identical keys. Person A drops a note in, locks it, and sends it; for the whole trip nobody along the route can open it; Person B unlocks it with a matching key. **The same key locks and unlocks.** The mapping to the text world:

| Term | In the lockbox | What it is |
|---|---|---|
| **Plaintext** | The note | The readable message (`HELLO`) |
| **Ciphertext** | The locked box in transit | The scrambled form (`KHOOR`) |
| **Algorithm** | How the lock works | The public method |
| **Key** | The exact cut of the key | The secret |

The point that trips people up: **the method is public, only the key is secret.** You don't hide how a padlock works to make it safe. A **cipher** — the recipe that turns plaintext into ciphertext and back — is published openly; AES, the common one, is picked apart worldwide, and none of that weakens it, because the secrecy lives in the key.

### The Caesar cipher and the game

The room's toy example: slide every letter a fixed number of places, and that number is the key (`A`→`D` at key 3, wrapping at the end so `Z`→`C`). Useless for real security — only 25 shifts to try — but it shows the pattern `algorithm + key + plaintext → ciphertext`. The task ends in a small browser game built on the same shift.

## The key distribution problem

Symmetric encryption assumes both sides already share the key. **How does that key get across in the first place?** Send it in the clear and an eavesdropper pockets it; encrypt it and you need another key to protect *that*, with no end. This is the **key distribution problem**, and it's why symmetric encryption can't stand alone.

## Asymmetric encryption: a linked pair of keys

The fix — and the phrase that made it click for me: **two mathematically-linked keys**, a **public key** anyone can hold and a **private key** kept by one person. The link runs both ways, each with a physical picture.

**Public locks, private opens** — sending a message *to* someone. Think of a **street postbox**: the slot is the public key (anyone can post), the locked hatch is the private key (one keyholder). Alice encrypts her message with Bob's public key; an attacker who knows exactly where the postbox is still can't open it to read Alice's message; only Bob's private key does.

**Private locks, public opens** — this *proves the message came from you*. You lock with your private key; anyone can unlock with your public key, and since only your private key could lock it, that proves you sent it. The physical version is a **wax seal**: only your signet ring presses it, but anyone who knows your seal can confirm the letter is yours, and nobody can forge it without the ring. That's the basis of digital signatures, which the room flags but leaves for later.

The first direction cracks the earlier problem: **no shared secret ever had to travel beforehand** — the only key in the open was Bob's public one, which was never secret. What keeps it safe is that working out the private key *from* the public one would tie up an ordinary computer for an impractical length of time; the reverse is much easier, and is just how the pair gets made.

> **Aside — a step off the main track: why only one direction is hard.**
>
> Both schemes rest on a *trapdoor*: easy forwards, difficult backwards, unless you hold the shortcut (the private key). **RSA** multiplies two large primes, `n = p × q` — quick, but recovering `p` and `q` from `n` alone has no known fast method. **ECC** uses `P = k × G`, "adding" a curve point `G` to itself `k` times: easy going forward, but you can't compute `k = P ÷ G` — dividing one point by another isn't a thing, so recovering `k` means counting hops, hopeless at scale.
{: .prompt-info }

## Certificates and Certificate Authorities

How does Alice know the public key she grabbed is really *Bob's*, and not one an attacker swapped in? **The short answer:** she doesn't check it herself — she trusts a third party that vouches the key belongs to that site. That voucher is a **Certificate Authority (CA)**, and its vouching comes as a **certificate** (a document binding a public key to a website, signed by the CA). The padlock means that check passed.

Worth being precise about what the CA checks, because it's narrower than it sounds. There's no registry of "the real Bob's key" to compare against — the key is just whatever Bob generated. The CA confirms two things: that the applicant (the site's operator applying for the certificate, not the visiting browser) **controls the domain**, and that they **hold the private key** matching the public one. It proves the domain part by having the applicant add a unique token to the site's records — something only the real owner could do. So a certificate really says *the holder of this private key controls this domain* — web identity is domain control, not a personal name. My browser comes with a group of trusted CAs; a certificate signed by one of those CAs, not expired or revoked, gets the padlock.

## The hybrid approach: what HTTPS does

Asymmetric encryption fixes key distribution but is slow, so real systems combine both: use asymmetric to agree a shared symmetric key, then switch to fast symmetric for the session.

| | Symmetric | Asymmetric |
|---|---|---|
| Keys | One shared key | A public/private pair |
| Sharing | Both need the same secret | Public half shared openly |
| Speed | Fast, even at volume | Slower; small payloads |
| Used for | Bulk traffic | Starting the connection, proving identity |

The same pairing runs behind HTTPS, VPNs, and encrypted messaging.

## Connects to my bigger goal

This is exactly the kind of nuts and bolts I need to get good at, and tracing the chain behind the padlock end to end is a useful way in: a website asks a CA to vouch for its public key; the CA checks domain control and signs a certificate; my browser checks the CA's signature (in the certificate) against the CA's public key (in its trusted-root store), confirms the certificate is valid, then browser and server agree a symmetric key and switch to it; the padlock appears and traffic flows encrypted.

The thing that struck me: the encryption is the strong part — nobody is breaking the maths. If something breaks, it breaks at a *trust* link before the cryptography — the CA doing the vouching, the domain-control check, the private key on the server, the trusted-root store. That changes how I'd size up a "secure" site: the questions aren't about the cipher but about those links — from "there's a padlock, so it's fine" to "where could this chain give way." The specific attacks on each link were new to me here, so they sit in Revisit.

## Where I got stuck

- **I assumed encryption also prevents tampering.** If an attacker can't *read* it, surely they can't *change* it — wrong. Encryption protects confidentiality, not integrity; ciphertext can still be altered in transit. Catching that takes a separate tool — a **message authentication code**, a short check value derived from the data and a shared secret, which the receiver recomputes to confirm nothing changed. TLS runs this alongside the encryption.
- **I tried to reverse ECC with plain division.** Seeing `P = k × G`, I reached for `k = P ÷ G`. It doesn't work — that `×` isn't ordinary multiplication and `G`, `P` are points, not numbers, so there's no dividing one by the other.

## Revisit

- **The internals the room skips** — how AES mixes a block, the real maths behind RSA/ECC key generation, and digital signatures done properly.
- **Certificate revocation** — how a browser learns a certificate was pulled *before* its printed expiry.
- **How each trust link gets attacked** — the trust links, not the cipher, are the real attack surface: CA breach or mis-issuance (defences: Certificate Transparency, CAA records), domain-control subversion, server key theft, trust-store poisoning, and HTTPS stripping (defended by HSTS).

## Lessons Learned

- **The algorithm is public; only the key is secret.** Security comes from protecting the key, not hiding the cipher.
- **Asymmetric encryption answers a named problem** — key distribution — by removing the need to share a secret in advance.
- **Encryption ≠ integrity.** Secrecy and tamper-proofing are two guarantees from two different mechanisms — and TLS provides both.
- **Real systems are hybrid:** asymmetric for the handover, symmetric for the bulk — the combination that runs behind every padlock.
- **The trust, not the maths, is the soft spot** — certificates, CAs, and trust stores are where security actually stands or falls.

## References

- [Cryptography Concepts — TryHackMe](https://tryhackme.com/room/cryptographyconcepts)
- Rooms this builds on: [The CIA Triad](https://tryhackme.com/room/theciatriad), [Data Encoding](https://tryhackme.com/room/dataencoding)
