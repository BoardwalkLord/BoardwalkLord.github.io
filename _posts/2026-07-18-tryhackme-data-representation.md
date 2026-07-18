---
title: "TryHackMe: Data Representation — Learning Notes"
description: "My notes on the Data Representation room — binary, hex and RGB colour, and why hexadecimal is really just the actual bytes made legible, which is exactly why security tools speak it."
date: 2026-07-18 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, binary, hexadecimal, data-representation, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab conversion values, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Data Representation — [link](https://tryhackme.com/room/datarepresentation)
- **Difficulty:** Info / Easy
- **What it teaches:** How computers represent numbers and colours using only two digits — binary, hexadecimal, the octal system, bits and bytes, and RGB hex colour codes.

This is the first properly *technical* room in a while after a run of conceptual ones. I've handled binary and hex before — my background involves a fair bit of scripting and data work — so the raw mechanics weren't a shock. What the room actually gave me was two things worth having: it made me *formalise* something I'd only ever used operationally, and it clarified **why** hexadecimal is everywhere in security tooling rather than being some arbitrary cryptic convention. That second point is the lens I've written this up through.

## The one idea underneath everything: assigning human meaning to two states

The starting point that's easy to skip past: for a computer to be *useful* to a human, it has to represent the things humans care about — numbers, letters, colours — using only what it physically has, which is two electrical states. So the whole subject is really about **agreeing what those two states will mean**, and then building everything else on top.

- Humans default to **decimal (base-10)** for the plainest possible reason: ten fingers.
- Computers default to **binary (base-2)** for an equally physical reason: a transistor either passes current or it doesn't. There's no third option available at the hardware level.

Once it's said it's obvious, but I hadn't put it in exactly those terms before — our number system and the computer's are each just an accident of the hardware doing the counting.

## A bit really is only ever 0 or 1 — and here's why that's not a limitation

Working through this, I asked myself a question I think a lot of people quietly have: is a **bit** a slot that could hold any character — 0–9, a–z, a symbol — or is it genuinely limited to 0 and 1?

The answer is that it's **only ever 0 or 1**, and the reason is the transistor point above. A bit is defined as a single binary digit, and the physical thing storing it can only be in one of two states. Those aren't two separate facts — the definition and the hardware are the same fact seen from two sides.

So how do we ever get letters, colours and emojis? **We never store them in a single bit. We store them in *groups* of bits.** That realisation is the hinge the whole room turns on: one bit is trivially small, but bits combine, and the number of things you can represent grows fast as you add them:

- 1 bit → 2 states
- 2 bits → 4 states
- 8 bits → 256 states

Everything else in the room is just this idea applied — to colours, then to bigger numbers.

## Colour: the clearest worked example of "group the bits"

Colour made the grouping concrete for me. Screen colour (light, not paint — they mix differently) is built from three primaries: **red, green, blue**.

- **The 8-colour version.** Give each of R, G, B a single on/off bit and you get 2 × 2 × 2 = 8 colours. `111` is all three on (white); `100` is red only. Three bits, eight colours.
- **The 16-million version.** One on/off bit per colour is too coarse. Instead, give each colour a whole **byte** — 8 bits, so **256 intensity levels** (not just on/off). Now: 256 × 256 × 256 = 16,777,216 colours. That covers real life.

So a full-colour pixel is **3 bytes = 24 bits**. The room's example green comes out as `10100011 11101010 00101010`. Which is completely correct and completely unreadable — and that unreadability is the entire reason the next idea exists.

## Hexadecimal: the actual bytes, made legible

Here's the reframe that made this room worth doing, even knowing hex already.

24 bits of `10100011 11101010 00101010` is honest but unusable. The fix is to chunk it: **every 4 bits becomes a single hexadecimal digit** (0–9, then A–F for 10–15). Four bits have 16 possible patterns; a hex digit has 16 possible symbols; they line up perfectly, one-to-one.

Do that to the green above and you get **`A3EA2A`** — six hex digits for three bytes. And the accounting is clean:

- 4 bits = 1 hex digit
- 1 byte (8 bits) = exactly 2 hex digits
- 3 bytes (24 bits) = 6 hex digits

That's the whole trick. And it reframed hex for me completely. **Hexadecimal isn't a cryptic code layered *over* the data — it's the most compact honest view *of* the data.** Every pair of hex digits is one real byte, sitting there in the open. You're not reading a translation; you're reading the bytes themselves, just written so a human can hold them. Once I saw it that way, the fact that security tooling is *drenched* in hex stopped looking arbitrary (more on that below).

## The part I had to sit with: a general base-conversion formula

The mechanics I knew; what I hadn't done was formalise them into one rule that covers *every* base. Working it out on paper, it collapses to a single positional formula:

> **each digit × (base ^ its position from the right, counting from 0), then sum**

The insight is that decimal, binary, hex and octal are all the *same machine* — only the base changes. Decimal's 213 is `2×10² + 1×10¹ + 3×10⁰`; binary and hex are identical in structure, just with 2 or 16 where the 10 is.

A few worked examples using my *own* numbers (not the room's task values — go do those yourself):

- **Binary** `101` → 1×2² + 0×2¹ + 1×2⁰ = 4 + 0 + 1 = **5**
- **Hex** `2A` → 2×16¹ + 10×16⁰ = 32 + 10 = **42**  *(A = 10)*
- **Octal** `172` → 1×8² + 7×8¹ + 2×8⁰ = 64 + 56 + 2 = **122**

Having one formula instead of three separate procedures is the kind of thing the room means when it says these should become second nature. For me the value wasn't learning it — it was compressing what I already did into something I can't get wrong under pressure.

## Connects to my bigger goal

This is where a memory clicked into place, and it's the reason the room mattered to me.

As a teenager I used to open up the `.exe` files of games in a hex editor — often *after* an antivirus had flagged them — and stare at the insides written in hex. At the time I had a half-formed question I couldn't answer: *if a virus attaches itself to an executable, does the file's hex actually change?*

I can finally answer my own question. **Yes — and that's the entire basis of a whole layer of malware detection.** Hex is the bytes made legible, so the bytes on disk *are* what you're looking at in a hex editor. If something appends or injects code into an executable, the file's bytes change, and therefore its hex changes. Which means:

- A **hash** (MD5, SHA-256 — themselves written in hex) is a fingerprint of exactly those bytes; change one byte and the hash changes completely. That's how "this file no longer matches its known-good hash" works.
- A **signature** — a specific hex byte-pattern known to belong to a piece of malware — is something scanners look for *in that hex*. Reading a file at the byte level is reading it in hex.

So the thing I was squinting at as a teenager, with no framework for it, was the raw material of signature- and hash-based detection. The room's closing claim — that binary and hex fluency is a must in security — isn't a platitude once you've seen this. **You read hex in security for the same reason you read it for colour codes: it's the shortest honest way to look at the actual bytes.**

(One light touch on the compliance side, without forcing it: the password hashes I ran into while pulling apart a government risk-assessment workbook a while back were SHA-512 — long strings of hex. Now I can say precisely what I was looking at: a hex-written fingerprint of the original password's bytes. Same idea, different setting.)

## Where I got stuck

Not stuck so much as *slowed down deliberately* on the base-conversion formula — not because it was hard, but because I wanted to derive the single general rule rather than memorise three special cases. That was time well spent. Everything else in the room was familiar ground told clearly.

## Revisit

- **Hex dumps and file signatures in practice.** I want to actually open a file in a hex editor again — this time with the framework I was missing at 15 — and see a real signature or a real header (magic bytes) sitting in the hex.
- **Hashing properly.** The room made me realise a hash is a hex fingerprint of bytes; I want to come back to *how* the fingerprint is computed and why a one-byte change cascades through the whole thing.
- **Character encoding**, which the room says is next (the Data Encoding room) — how letters and emojis get their byte patterns, i.e. the same "group the bits" idea applied to text instead of colour.

## Lessons Learned

- **A bit is only ever 0 or 1** — that's the hardware (a transistor is on or off), and everything richer is built from *groups* of bits, never a single one.
- **Hex is the bytes made legible:** every byte is exactly two hex digits, so reading hex is reading the actual data, not a translation of it.
- **One formula covers every base:** digit × base^position, summed. Decimal, binary, hex and octal are the same machine with a different base.
- **This is why security speaks hex:** hashes, signatures and hex dumps are all just the real bytes of a file, written the shortest honest way — which is exactly what a teenager with a hex editor was looking at without knowing it.

## References

- The room's guided tasks and static-site converter on TryHackMe
