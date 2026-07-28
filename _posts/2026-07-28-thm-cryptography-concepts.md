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
  no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

## Overview

- **Room:** [Cryptography Concepts](https://tryhackme.com/room/cryptographyconcepts)
- **Difficulty:** Easy
- **What it teaches:** The core vocabulary of encryption, the split between symmetric and asymmetric approaches, and why every secure website ends up using both at once.

This is a *concepts* room — no target machine, no `nmap`, just ideas built up through analogies and one small hands-on cipher exercise. It follows on from the CIA Triad and Data Encoding rooms, and it builds everything around a scenario I happen to know from the inside: a clinic that has to push patient files out to specialists and insurers across the open internet. The biggest thing I walked away with wasn't the encryption itself — I'd met most of that before — but a dent in my own mental model: **scrambling data keeps it secret, but that alone does not stop someone tampering with it.** I'll come back to that at the end.

## Why cryptography matters (and the padlock question)

The room opens by asking what the padlock in your address bar is actually doing for you.

My answer going in: the padlock signals **TLS** (Transport Layer Security — the layer that turns `http` into `https`). Whatever leaves the server is encrypted into unreadable junk, crosses the network in that state, and is only turned back into something my browser can use once it arrives. Strip the *s* and you have plain `http`: someone sitting on the path between the server and me can not only read the traffic but rewrite it on the way through.

That lines up with the CIA Triad from last room. Encryption is the main tool behind two of the three pillars:

- **Confidentiality** — turn it into junk so only the intended reader can recover it.
- **Integrity** — be able to tell if anyone altered it in transit.
- Availability is the third pillar, but it's more about keeping systems accessible than about cryptography.

The reason any of this is needed: traffic almost never runs point-to-point. It hops across a chain of machines you don't control, and each hop is a spot where an unprotected message could be read, changed, or dropped. Encryption closes that off by locking the content behind a secret that the eavesdroppers don't hold.

## Symmetric encryption: one secret, shared both ways

**Answering the opening question with a physical object:** picture a lockbox and two keys cut to the same shape. Person A drops a note inside and locks it. The box makes its way to Person B, and for the whole trip nobody along the route can get it open. Person B receives it, turns their matching key, and reads the note.

That's symmetric encryption in one line: **the same key that locks it is the one that unlocks it.**

Rereading the task, the thing worth nailing down is how the box maps onto the text-world terms:

| Term | In the lockbox | What it is |
|---|---|---|
| **Plaintext** | The note inside | The message as you'd read it (`HELLO`) |
| **Ciphertext** | The locked box mid-journey | The scrambled form (`KHOOR`) — ideally indistinguishable from noise |
| **Algorithm** | The mechanism of the lock | The publicly-known method; watching someone turn a key gives nothing away |
| **Key** | The exact cut of the key | The secret; only the right cut moves the lock |

The analogy holds up well, and it drives home the point that trips people up: **the method is public, the key is what's secret.** You don't keep the design of a padlock hidden to make it safe — its safety comes from you being the only one with the key. Encryption works the same way round. A **cipher** — the specific recipe that turns plaintext into ciphertext and back again — is published openly rather than hidden; AES, the one most systems use, has been picked apart by researchers everywhere, and none of that weakens it, because the secrecy lives entirely in the key.

### The Caesar cipher

To make it concrete the room reaches for the Caesar cipher: pick a number, and slide every letter that many places along the alphabet. That number *is* the key. With a key of 3, `A` lands on `D`, and the far end wraps around so `Z` comes back to `C`. Decrypting is the same move in reverse.

It's useless for anything real — there are only 25 possible shifts, so a machine simply tries them all and reads the message off in the blink of an eye. It earns its place here purely as a clear example of the pattern `algorithm + key + plaintext → ciphertext`. Serious ciphers keep that exact shape and just make the reversing step impractically hard without the key.

The task closes with a small in-browser exercise where you play a team on a monitored network, sliding the shift value to read intercepted Caesar messages and to prepare replies. It's a nice way to *feel* the key-and-algorithm split rather than just read about it.

## The catch: the key distribution problem

Here's where I had to stop and think. Symmetric encryption is quick and it handles bulk data happily — but it assumes both sides already share the secret key. So: **how does that key get from one person to the other in the first place?**

Send it across the network as-is and any eavesdropper simply pockets it, then reads everything from then on. Encrypt the key before sending? Now you need a *second* key to protect the first one — and a third to protect that — with no end in sight. This problem has a name, the **key distribution problem**, and it's the reason symmetric encryption can't stand entirely on its own.

## Asymmetric encryption: a linked pair of keys

This is the fix, and it's the part that actually landed for me. I'd read about asymmetric encryption before, but the phrase **"two mathematically-linked keys"** is what finally made it click:

- a **public key**, which you're free to hand to anyone, and
- a **private key**, which stays with exactly one person.

The link runs in both directions:

- lock something with a **public** key and *only* its paired **private** key can open it — that's how you send a secret *to* someone;
- lock something with your **private** key and anyone holding your **public** key can open it — which sounds pointless until you realise it *proves the message came from you*. The physical version is a **wax seal**: only your signet ring can press it, but anyone who recognises your seal can confirm a letter is genuinely yours, and nobody can forge it without the ring. That's the basis of digital signatures, which this room flags but leaves for another day.

What keeps it safe is the direction of the difficulty. The pair is bound by heavy maths, and working out the private key *from* the public one would tie up an ordinary computer for an impractical length of time. One thing I had to get straight for myself: that hardness only runs one way. The **public → private** direction is the infeasible one; the reverse is much easier, because producing the public key *from* the private key is just part of generating the pair.

### Aside: why only one direction is hard

Both of the common asymmetric schemes rest on a *trapdoor* function — something easy to run forwards but hard to reverse, unless you hold a secret shortcut, which is what the private key is. **RSA** is the classic: it multiplies two large prime numbers together, which is quick, but handed only the result you'd have to split it back into those primes, and there's no known fast way to do that — so for big enough numbers the search outlasts any practical timescale. The two primes are the private side; their product is the public side.

**ECC** (elliptic-curve cryptography) leans on a different lopsided operation: repeatedly "adding" a point to itself along a curve. Going forwards — adding it to itself some number of times — is fast; working out *how many times* it was added, given only the start and end points, is the hard direction, again with no known shortcut. In both schemes the forward step is a direct calculation and the reverse is a search nobody has found a quick route through, and that gap is the whole source of the one-way difficulty.

### The mailbox analogy

A postbox on a street corner:

- the **slot** you post letters into is the **public key** — open to any passer-by, no secret about it;
- the **locked hatch** the owner opens to collect the post is the **private key** — one keyholder, and that's it.

So Alice looks up Bob's public key (he can stick it on his website — it was never meant to be hidden), encrypts her message with it, and sends it off. An attacker who knows precisely where the postbox is still can't get inside; they don't hold Bob's private key. Bob opens the hatch with his and reads it.

And that's the point of the whole thing: **no shared secret ever had to travel across the network beforehand.** The only key that went out in the open was Bob's public one, which was never secret anyway. Key distribution problem solved — anyone can be a sender, only the recipient can decrypt.

## Certificates and Certificate Authorities

That solution raises its own question straight away: how does Alice know the public key she grabbed is really *Bob's*, and not one an attacker swapped in?

Two pieces answer it, and they're easy to run together, so worth pulling apart:

- a **certificate** is the digital document itself — it carries a public key *and* a claim about which website owns it (say, `example.com`);
- a **Certificate Authority (CA)** is the trusted outside party that puts its signature on that document, vouching that the key and the owner really do go together.

My browser and OS arrive with a set of CAs they already trust. When a site presents its certificate, the browser checks a trusted CA signed it and that it hasn't expired or been pulled. All good → I get the padlock. Something wrong — out of date, or signed by somebody the browser doesn't recognise — and I get a warning instead. You can see this yourself: click the padlock on any HTTPS site and the certificate details show who it was issued *to*, which CA issued it, and the dates it's valid between.

### But why trust the CA?

The room says my browser ships knowing which CAs to trust — which just moved my suspicion up a level. *Why* is that set trustworthy, and who chose it? What I dug up:

- That set is a **root store** (or trust store), and it's the **browser or OS vendor** who curates it — Mozilla, Apple, Microsoft, Google each run their own. So the honest answer to "who told my browser to trust this CA?" is: whoever built my browser did.
- **Getting into that store is not open to all comers.** You can stand up your own CA in an afternoon, but being trusted *publicly* means being admitted to those vendors' root programmes — which requires passing independent audits and sticking to an agreed industry rulebook (the CA/Browser Forum baseline requirements), under continuing scrutiny.
- The trust is **revocable**. CAs that have misbehaved or been compromised have been ejected from root stores before now. So a CA isn't safe *because* it's a CA — it's safe because a vendor vetted it and keeps watching, and that verdict can be reversed.

## The hybrid approach: what HTTPS actually does

Asymmetric encryption fixes key distribution but pays for it in speed, so no sensible system uses it for everything. Real ones marry the two:

1. the browser and the site use **asymmetric** encryption to safely settle on a shared symmetric key, with nobody in the middle able to see the agreement;
2. once that's done they drop to fast **symmetric** encryption for the rest of the conversation.

Each type does the job it's good at — asymmetric handles the awkward first handshake, symmetric carries the traffic. Laid side by side:

| | Symmetric | Asymmetric |
|---|---|---|
| Keys involved | One shared key does both jobs | A pair: one public, one private |
| Getting the key across | Both ends must already hold the same secret | The public half is handed out openly; nothing secret has to travel |
| Speed | Quick, even at volume | Heavier — kept for small payloads |
| Where it earns its keep | Encrypting the bulk of the traffic | Starting the connection and proving identity |
| Everyday picture | One key that both locks and opens a chest | A street postbox: anyone posts in, only the keyholder empties it |

The same pairing is what sits under HTTPS, VPNs, and encrypted messaging apps.

## Lessons learned

- **What finally clicked:** framing asymmetric encryption as "two mathematically-linked keys," and — more usefully — seeing it as the specific answer to a *named* problem (key distribution). That turned the whole room from "here are two methods" into "here's a problem, and here's the fix," which is far stickier.
- **Where I corrected myself:** I'd assumed that if an attacker can't *read* my encrypted data, they can't *change* it either. Wrong. Encryption buys **confidentiality**, not **integrity** — someone can still tamper with ciphertext (flip bits, cut it short, replay it) without ever reading it, and with some ciphers those changes land predictably on the decrypted result. Knowing a message *wasn't* altered is a separate guarantee, from a message authentication code / authenticated encryption. TLS carries both, which is a chunk of why the padlock means more than "it's scrambled."
  {: .prompt-warning }
- **A second thing I had to fix:** the hard maths only runs one way — deriving the private key from the public one is the infeasible direction; the reverse is much easier and is simply how the pair is made.
- **The question I chased past the room:** *why* the CA list is trustworthy at all (see above). Short of it: the trust lives with the browser/OS vendor and is earned through audits, not automatic.
- **To revisit:** the room skips all the internals on purpose, and I want to come back to how AES actually mixes a block, the real maths behind RSA/ECC key generation, digital signatures done properly, and certificate **revocation** — how a browser finds out a certificate was pulled *before* its printed expiry.

## References

- [Cryptography Concepts — TryHackMe](https://tryhackme.com/room/cryptographyconcepts)
- Rooms this builds on: [The CIA Triad](https://tryhackme.com/room/theciatriad), [Data Encoding](https://tryhackme.com/room/dataencoding)
