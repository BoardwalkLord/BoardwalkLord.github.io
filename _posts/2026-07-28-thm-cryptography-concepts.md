---
title: "TryHackMe: Cryptography Concepts — Learning Notes"
description: "My notes on the Cryptography Concepts room — plaintext vs ciphertext, symmetric vs asymmetric encryption, certificates, and how they combine to power HTTPS."
date: 2026-07-28 08:00:00 +0800
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

The link runs in both directions, and each direction has a plain physical picture to go with it.

**Public key locks, private key opens** — this is how you send a secret *to* someone. Picture a **postbox on a street corner**: the slot you drop letters into is the public key, open to any passer-by; the locked hatch the owner opens to collect the post is the private key, held by one person only. Alice looks up Bob's public key (he can put it on his website — it was never meant to be hidden), encrypts her message with it, and sends it. An attacker who knows exactly where the postbox is still can't get inside; they don't hold Bob's private key. Bob opens the hatch with his and reads it.

**Private key locks, public key opens** — this one sounds pointless at first, until you realise it *proves the message came from you*. The physical version is a **wax seal**: only your signet ring can press it, but anyone who recognises your seal can confirm a letter is genuinely yours, and nobody can forge it without the ring. That's the basis of digital signatures, which this room flags but leaves for another day.

The first of those two directions is what cracks the earlier problem. **No shared secret ever had to travel across the network beforehand** — the only key that went out in the open was Bob's public one, which was never secret anyway. Key distribution problem solved: anyone can be a sender, only the recipient can decrypt.

What keeps it all safe is the direction of the difficulty. The pair is bound by heavy maths, and working out the private key *from* the public one would tie up an ordinary computer for an impractical length of time. That hardness only runs one way: the **public → private** direction is the infeasible one, while the reverse is much easier and is just part of generating the pair.

> **Aside — a step off the main track: why only one direction is hard.**
>
> Both of the common asymmetric schemes rest on a *trapdoor* function — something easy to run forwards but hard to reverse, unless you hold a secret shortcut, which is what the private key is. We have two examples of real-world trapdoor functions: RSA and ECC.
>
> **RSA** is the classic. Pick two large prime numbers, call them `p` and `q`, and multiply them together: `n = p × q`. Going forwards — `p` and `q` in, `n` out — is quick. Going backwards — handed only `n` and asked to recover `p` and `q` — has no known fast method (you're missing *both* factors, so there's nothing to divide `n` by), so for big enough numbers that search outlasts any practical timescale. The pair `(p, q)` is effectively the private side; `n` is public. (In full RSA you also derive a public exponent `e` and a private exponent `d` from those primes, then encrypt a message with `c = m^e mod n` and decrypt it with `m = c^d mod n` — but the security still rests entirely on `n` being hard to factor.)
>
> **ECC** (elliptic-curve cryptography) uses a different lopsided operation. Fix a starting point `G` on a curve, and let the private key be a whole number `k`. The public key is the point you land on after adding `G` to itself `k` times: `P = k × G`. The catch that trips everyone up: that `×` is *not* ordinary multiplication, and `G` and `P` aren't numbers — they're *points* on the curve, so `k × G` means "apply the curve's own addition rule to `G`, `k` times over." Forwards — `k` and `G` in, `P` out — is fast even for an enormous `k`. But backwards you can't just compute `k = P ÷ G`, because dividing one point by another isn't a thing that exists; the only route to `k` is to keep adding `G` and count the hops until you reach `P`, which is hopeless once `k` is hundreds of digits long. So `P = k × G` is cheap to compute, but recovering `k` from `P` and `G` is infeasible. In both schemes the forward step is a direct calculation and the reverse is a search nobody has found a quick route through, and that gap is the whole source of the one-way difficulty.
{: .prompt-info }

## Certificates and Certificate Authorities

That solution raises its own question straight away: how does Alice know the public key she grabbed is really *Bob's*, and not one an attacker swapped in?

**The short answer:** she doesn't check it herself. She offloads the trust to a third party that everyone already trusts, which vouches that this public key really does belong to Bob. That voucher is a **Certificate Authority**, and its vouching comes packaged as a **certificate**. When the padlock appears, it means that check quietly passed.

Now the mechanics. Two pieces do the work, and they're easy to blur together, so worth pulling apart:

- a **certificate** is the digital document itself — it carries a public key *and* a claim about which website owns it (say, `example.com`);
- a **Certificate Authority (CA)** is the trusted outside party that puts its signature on that document, vouching that the public key and the website really do go together.

It's worth being precise about what that "vouching" involves, because it's narrower than it sounds. There's no master registry of "the real Bob's key" for the CA to check against — the public key is simply whatever Bob generated himself, and the CA never had a prior copy. What the CA actually confirms is two things: that whoever requested the certificate genuinely **controls the domain** it's for (they're made to prove it — place a specific file on the site, or add a DNS record only the domain's operator could add), and that they **hold the private key** matching the public key being certified (the request is signed with that private key, so the CA can check the pair fits). So a standard certificate doesn't really say "this is Bob's key" — it says *the holder of this private key controls this domain*. Identity on the web is domain control, not a personal name. That narrowness is also exactly why the domain-control check is an attack surface, as I get into below.

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

## Connects to my bigger goal

The reason I'm working through these rooms is to be able to sit across from a business owner and turn this material into advice. This room handed me a good exercise for that: trace the whole chain behind the padlock, then think like the person whose job is to ask where it breaks.

The chain, start to finish:

1. A website generates a key pair and asks a **Certificate Authority** to vouch for its public key. The CA checks the applicant really controls the domain, then signs a **certificate** binding that public key to that website — signing with the CA's own private key.
2. The website installs the certificate and serves it to any browser that connects.
3. My browser requests the site, and the site presents its certificate.
4. My browser verifies the CA's signature on it — using the CA's public key, which already shipped in the browser's trusted-root store — and checks the certificate hasn't expired or been revoked.
5. Satisfied the public key genuinely belongs to the site, browser and server use asymmetric encryption to agree a shared symmetric key, then switch to fast symmetric encryption for the session.
6. The **padlock** appears, and the page flows across encrypted.

Now the adviser's question. The encryption itself is the strong part — nobody is breaking the maths. So where *would* an attacker actually go? Walking each link:

- **The CA is tricked or breached.** Any trusted CA can vouch for any domain, so the system is only as strong as its weakest CA. Compromise one — or socially-engineer a careless one — and you walk away with a *validly signed* certificate for a domain you don't own, which is roughly the shape of the DigiNotar breach. The defences worth knowing a client's exposure to: Certificate Transparency (every certificate logged publicly, so a domain owner can spot one they never asked for) and CAA records (a DNS entry naming which CAs are even allowed to issue for you).
- **The domain-control check is subverted.** The CA proves you own a domain through an automated DNS or HTTP challenge. An attacker who can hijack DNS or routing *during* that check can pass it and be handed a legitimate certificate — the trust broke upstream of the crypto entirely.
- **The site's own private key is stolen.** Then the attacker cryptographically *is* the site, no CA trickery required. Heartbleed leaked exactly this kind of key straight out of server memory.
- **The client's trust store is poisoned.** Slip a rogue root certificate onto someone's machine and every forged certificate looks valid to them. Lenovo shipped laptops in that state (Superfish); corporate proxies do it on purpose.
- **The connection is stripped before HTTPS begins.** If the first request leaves as plain `http`, a man-in-the-middle can hold it there. The fix is HSTS — the site telling browsers to only ever connect over HTTPS.

What this gives me for client work is the move from "you've got a padlock, you're fine" to a proper set of questions: where does the private key live and who can reach it, is the domain locked to specific CAs, is HSTS switched on, is anyone watching the transparency logs for mis-issued certificates. None of it is exotic — it's just knowing the chain well enough to find the weak link, rather than pointing at the padlock and calling it done.

## Where I got stuck

Two spots, both my own assumptions rather than the room being unclear:

- **I assumed encryption also prevents tampering.** My instinct was that if an attacker can't *read* something, they can't *change* it either. Wrong — scrambling data protects confidentiality, not integrity. Ciphertext can still be altered in transit; knowing that it *wasn't* is a separate guarantee (a message authentication code / authenticated encryption), and it's one of the jobs TLS quietly does on top of the encryption.
- **I tried to reverse ECC with plain division.** Seeing the public key written `P = k × G`, I reached for `k = P ÷ G`. It doesn't work, and seeing *why* was the valuable part: that `×` isn't ordinary multiplication and `G`, `P` aren't numbers — they're points on a curve, and you can't divide one point by another. The one-way-ness is built into the operation itself.

## Revisit

- **The internals the room skips** — how AES actually mixes a block, the real maths behind RSA/ECC key generation, and digital signatures done properly.
- **Certificate revocation** — how a browser learns a certificate was pulled *before* its printed expiry (the mechanics behind the "not revoked" check in the chain above).
- **Where the real attacks live** — the trust anchors of the certificate chain (CA mis-issuance, Certificate Transparency, trust-store integrity) look like the actual attack surface, not the cipher. Worth studying as a topic in its own right.

## Lessons Learned

- **The algorithm is public; only the key is secret.** Security doesn't come from hiding how the cipher works — ciphers like AES are published openly — it comes entirely from protecting the key.
- **Asymmetric encryption is the answer to a named problem.** Symmetric encryption can't solve *getting the shared key to the other side* — the key distribution problem — and a public/private pair is precisely what removes the need to share a secret in advance.
- **Encryption ≠ integrity.** Keeping data secret and proving it wasn't altered are two different guarantees from two different mechanisms.
- **Real systems are hybrid.** Asymmetric handles the awkward key handover, symmetric does the fast bulk work — and that combination is what runs behind every padlock.
- **The trust, not the maths, is the soft spot.** The cipher is the strong link; certificates, CAs, and trust stores are where security actually stands or falls.

## References

- [Cryptography Concepts — TryHackMe](https://tryhackme.com/room/cryptographyconcepts)
- Rooms this builds on: [The CIA Triad](https://tryhackme.com/room/theciatriad), [Data Encoding](https://tryhackme.com/room/dataencoding)
