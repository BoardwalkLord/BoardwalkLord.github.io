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

This is the first properly technical room after a run of conceptual ones. I've handled binary and hex before — my background includes a fair bit of scripting and data work — so the mechanics were familiar. The room gave me two useful things anyway: it made me formalise something I'd only ever used operationally, and it clarified why hexadecimal shows up everywhere in security tooling. I've written the notes up around that second point.

## The core idea: assigning human meaning to two states

For a computer to be useful to a human, it has to represent things humans care about — numbers, letters, colours — using only what it physically has, which is two electrical states. So the whole subject is about agreeing what those two states will mean, then building everything else on top.

- Humans use **decimal (base-10)** because we have ten fingers.
- Computers use **binary (base-2)** because a transistor either passes current or it doesn't. There's no third option at the hardware level.

I hadn't put it in those terms before: each number system is just a consequence of the hardware doing the counting.

## A bit is only ever 0 or 1

A question I had while working through this: is a **bit** a slot that could hold any character — 0–9, a–z, a symbol — or is it limited to 0 and 1?

It's only ever 0 or 1, for the transistor reason above. A bit is defined as a single binary digit, and the physical thing storing it can only be in one of two states. The definition and the hardware are the same fact from two sides.

So how do we get letters, colours and emojis? We never store them in a single bit — we store them in **groups** of bits. That is the key point. One bit is trivially small, but the number of things you can represent grows quickly as you add bits:

- 1 bit → 2 states
- 2 bits → 4 states
- 8 bits → 256 states

Everything else in the room is this idea applied — first to colours, then to larger numbers.

## Colour: grouping bits in practice

Screen colour (light, not paint — they mix differently) is built from three primaries: **red, green, blue**.

- **The 8-colour version.** Give each of R, G, B a single on/off bit and you get 2 × 2 × 2 = 8 colours. `111` is all three on (white); `100` is red only. Three bits, eight colours.
- **The 16-million version.** One on/off bit per **primary** colour is too coarse. Instead, give each **primary** colour a whole **byte** (equal to 8 bits), so that each primary colour can hold **256 intensity levels** (= 2×2×2×2×2×2×2×2) instead of just on/off. Then 256 × 256 × 256 = 16,777,216 colours, which covers real-life needs.

So a full-colour pixel is **3 bytes = 24 bits**. The room's example green comes out as `10100011 11101010 00101010`. That's correct but unreadable, which is the reason for the next idea.

## Hexadecimal: a shorter way to write bytes

Twenty-four bits of `10100011 11101010 00101010` is accurate but hard to use. Hexadecimal fixes this by grouping **every 4 bits into a single hex digit** (0–9, then A–F for 10–15). Four bits have 16 possible patterns; a hex digit has 16 possible symbols; they line up one-to-one.

```
10100011  11101010  00101010    = 24 bits
 1 byte    1 byte    1 byte      = 3 bytes
 H   H     H   H     H   H       = 6 hex digits
```

Do that to the green above and you get `A3EA2A` — six hex digits for three bytes. The accounting is clean:

- 4 bits = 1 hex digit
- 1 byte (8 bits) = 2 hex digits
- 3 bytes (24 bits) = 6 hex digits

This is the point worth holding onto: hexadecimal is not a separate code layered over the data. Each pair of hex digits is one actual byte. When you read hex, you're reading the real bytes, written so a human can read them — not a translation of them. That is why security tools use hex so heavily (more below).

## A general base-conversion formula

I knew the mechanics; what I hadn't done was reduce them to one rule that covers every base. Worked out on paper, it's a single positional formula:

> **each digit × (base ^ its position from the right, counting from 0), then sum**

Decimal, binary, hex and octal are the same procedure with a different base. Decimal's 213 is `2×10² + 1×10¹ + 3×10⁰`; binary and hex have the same structure, with 2 or 16 in place of the 10.

A few worked examples using my own numbers (not the room's task values — go do those yourself):

- **Binary** `101` → 1×2² + 0×2¹ + 1×2⁰ = 4 + 0 + 1 = **5**
- **Hex** `2A` → 2×16¹ + 10×16⁰ = 32 + 10 = **42**  *(A = 10)*
- **Octal** `172` → 1×8² + 7×8¹ + 2×8⁰ = 64 + 56 + 2 = **122**

Having one formula instead of three separate procedures is what the room means about these becoming second nature. The value for me wasn't learning it — it was compressing what I already did into something I won't get wrong under pressure.

## Connects to my bigger goal

This room connected to an old memory. As a teenager I used to open the `.exe` files of games in a hex editor — often after an antivirus had flagged them — and look at the insides written in hex. I had a question I couldn't answer at the time: *if a virus attaches itself to an executable, does the file's hex actually change?*

I can now answer it: yes, and it's the basis of a whole layer of malware detection. Hex is the bytes made readable, so the bytes on disk are what you see in a hex editor. If something appends or injects code into an executable, the file's bytes change, and its hex changes with them. Which means:

- A **hash** (MD5, SHA-256 — themselves written in hex) is a fingerprint of those exact bytes. Change one byte and the hash changes completely. That's how "this file no longer matches its known-good hash" works.
- A **signature** — a specific hex byte-pattern known to belong to a piece of malware — is something scanners look for in that hex. Reading a file at the byte level is reading it in hex.

So the thing I was looking at as a teenager, without a framework for it, was the raw material of signature- and hash-based detection. The room's closing claim — that binary and hex fluency is a must in security — makes sense once you've seen this. You read hex in security for the same reason you read it for colour codes: it's the shortest way to see the actual bytes.

(One light note on the compliance side: the password hashes I ran into while pulling apart a government risk-assessment workbook a while back were SHA-512 — long strings of hex. I can now say precisely what those were: a hex-written fingerprint of the original password's bytes. Same idea, different setting.)

## Where I got stuck

Not stuck — I slowed down deliberately on the base-conversion formula, because I wanted to derive one general rule rather than memorise three special cases. That was time well spent. The rest was familiar ground told clearly.

## Revisit

- **Hex dumps and file signatures in practice.** I want to open a file in a hex editor again — this time with the framework I was missing at 15 — and find a real signature or a real header (magic bytes) in the hex.
- **Hashing properly.** A hash is a hex fingerprint of bytes; I want to come back to how the fingerprint is computed and why a one-byte change cascades through the whole thing.
- **Character encoding**, which the room says is next (the Data Encoding room) — how letters and emojis get their byte patterns, i.e. the same "group the bits" idea applied to text.

## Lessons Learned

- **A bit is only ever 0 or 1** — that's the hardware (a transistor is on or off). Everything richer is built from groups of bits, never a single one.
- **Hex is a short way to write bytes:** every byte is exactly two hex digits, so reading hex is reading the actual data.
- **One formula covers every base:** digit × base^position, summed. Decimal, binary, hex and octal are the same procedure with a different base.
- **This is why security uses hex:** hashes, signatures and hex dumps are all the real bytes of a file, written the shortest way — which is what a teenager with a hex editor was looking at without knowing it.

## References

- The room's guided tasks and static-site converter on TryHackMe
