---
title: "TryHackMe: Data Encoding — Learning Notes"
description: "My notes on the Data Encoding room — ASCII, Unicode, and UTF-8/16/32, and why an encoding mismatch is both the cause of gibberish text and a real security surface."
date: 2026-07-19 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, ascii, unicode, encoding, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab lookup values, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Data Encoding — [link](https://tryhackme.com/room/dataencoding)
- **Difficulty:** Info / Easy
- **What it teaches:** How text is stored as numbers — ASCII, its limits, Unicode, and the UTF-8 / UTF-16 / UTF-32 encodings — and what causes the "gibberish characters" you sometimes see when a file opens wrong.

This is the direct sequel to the Data Representation room, and it pays off a Revisit I wrote at the end of that post: how letters and emojis get their byte patterns, which is the same "group the bits" idea applied to text instead of colour. The one sentence that carries the whole room: characters are just numbers with an agreed meaning, and that agreement is called an encoding.

The mechanics were familiar to me, so the value here was formalising them and looking at what happens — for security — when the agreement breaks.

## ASCII

ASCII uses **7 bits** to cover **128 characters** (= 2⁷): English letters, digits, punctuation, and some control characters. That's enough for English. Any character maps to a number, which you can write in binary, decimal, or hex.

A worked example with my own text. If I type `KEN1` and save it with ASCII encoding, the computer stores these numbers:

- **Hexadecimal** (the common way to show it): `4B 45 4E 31`
- **Decimal**: `75 69 78 49`
- **Binary**: `01001011 01000101 01001110 00110001`

All three are the same four numbers written three ways. One property worth remembering: consecutive characters get consecutive codes. If `A` is `41` in hex, then `B` and `C` are `42` and `43`. The same holds for `a`–`z` and `0`–`9`, which is why you can work out one from another.

## Why ASCII isn't enough

ASCII covers English. Other languages don't fit. Arabic needs more than 250 characters for its ligatures and diacritics; Japanese daily-use Kanji runs to a couple of thousand; Chinese standards define tens of thousands. Seven bits can't hold that.

The first patch was to use the eighth bit for another 128 characters, through regional standards like ISO-8859-1 (Western European) and ISO-8859-2 (Central/Eastern European). But that created a new problem: the *same* number means *different* characters in different standards. Save `Ø` in Latin-1 and open it as Latin-2, and it shows as `Ř`. The number didn't change; the map you read it with did. (More on that below — it's the heart of the room.)

## Unicode — and the distinction the room is really teaching

Unicode fixes the mess by being one map for every character in every language. It assigns each character a unique number called a **code point**:

- `A` → U+0041
- Greek Ω → U+03A9
- Japanese あ → U+3042

The current standard defines close to 157,000 characters, including several thousand emoji.

Here's the distinction I had to get precise, because it's easy to blur: **Unicode is the map (character → code point). UTF-8, UTF-16 and UTF-32 are three different ways to store those code points as actual bytes.** They aren't "types of Unicode" — they're encodings *of* Unicode. Same relationship as a phone number versus how you write it down.

- **UTF-8** — the most common on the web. Variable width: 1 to 4 bytes per character, chosen by complexity. The first 128 characters (the ASCII ones) use exactly 1 byte and are byte-for-byte identical to old ASCII, so UTF-8 is backward-compatible with it. Ω uses 2 bytes; an emoji like 🔥 uses 4. No wasted space.
- **UTF-16** — 2 bytes for common characters, 4 for rarer ones (emoji, ancient scripts). The 4-byte case uses a pair of 16-bit units called a surrogate pair.
- **UTF-32** — always 4 bytes per character. Simplest, but the most wasteful.

## The core rule: encode and decode with the same map

The rule that makes the whole subject click: **use the same encoding to decode that you used to encode.** Save `ABC` as ASCII and the bytes on disk are `41 42 43`. Read those bytes back as ASCII and you get `ABC`. Read them with a different encoding and you don't.

In my notes I'd asked what actually happens if you decode with the wrong one — so I worked it through, because the answer is the interesting part.

Take the bytes `41 42 43`. Decoded as **UTF-16** instead of ASCII, UTF-16 reads **two bytes at a time**, not one. So `41 42` combine into a *single* character (a CJK character — U+4142 `䅂` or U+4241 `䉁`, depending on byte order), and the leftover `43` is a dangling half. You don't get "ABC" with wrong-looking letters — you get a *different number of characters entirely*, because the two encodings disagree about how many bytes make one character.

(That "depending on byte order" is a real second subtlety: UTF-16 can store the two bytes high-first or low-first, which is why files sometimes begin with a byte-order mark. I'm noting it rather than chasing it here.)

That disagreement — same bytes on disk, different grouping when read — is the mechanism behind everything else in the room. It's why a file saved in one encoding and opened in another shows gibberish (the term for it is *mojibake*). The bytes never changed; only the map used to read them did.

## Connects to my bigger goal

Two threads, one I'd expected and one that turned out to matter more.

**Malware analysis (the expected one).** The hex-editor thread from the last room continues here. Opening an executable in a hex editor shows bytes; some of those byte runs are text (embedded strings, error messages, hard-coded paths, signatures) and reading them means decoding with the right encoding. Recognising ASCII/UTF byte patterns in a hex dump is part of picking out the human-readable footprints inside a binary.

**Encoding as a security surface (the one worth more).** The room frames wrong-encoding as a display nuisance. From a security angle it's more than that:

- **Input validation and normalisation.** If a system filters out dangerous characters, but checks the input in a *different* encoding than the one the system later acts on, an attacker can slip a character past the filter by submitting it in an encoding the filter didn't expect. The lesson: normalise everything to one encoding *before* you validate, or your filter is inspecting a different string than the one that eventually runs. Same bytes, different grouping — the exact mechanism from above, turned into a bypass.
- **Homoglyphs — which already bit me.** A few rooms back I lost an hour to a filename that used a Unicode middle-dot (`·`) where I'd meant a full stop (`.`); it looked identical and Jekyll silently ignored the file. This room explains the machinery under that: Unicode contains many characters that *look* the same but *encode* differently. It's the same trick behind lookalike phishing domains (a Cyrillic `а` standing in for a Latin `a` in a URL). The thing that cost me an hour and the thing that fuels homograph attacks are the same underlying property.

## Where I got stuck

Nothing hard — this built directly on the previous room. The one part I slowed down on was proving to myself what the "wrong encoding" failure actually looks like at the byte level, rather than just repeating that it "shows gibberish." Working out that UTF-16 reads two bytes at a time — so the character *count* changes, not just their appearance — is what made the whole mojibake idea concrete instead of hand-wavy.

## Revisit

- **Overlong UTF-8 and encoding-bypass bugs** — the input-validation angle above, done properly: how filters get defeated by alternative encodings, and how normalisation is supposed to stop it.
- **Homograph / IDN attacks** — the phishing side of the homoglyph problem, and how browsers and registrars try to defend against lookalike domains.
- **Reading strings out of a binary** — using the encoding knowledge here to pull human-readable text out of an executable in a hex editor or with a `strings`-type tool.

## Lessons Learned

- **Characters are numbers with an agreed meaning.** That agreement is the encoding, and it's the whole subject in one line.
- **Unicode is the map; UTF-8/16/32 are ways to store it.** They're encodings of Unicode, not types of it.
- **Decode with the same map you encoded with.** Mismatch doesn't just mangle letters — because encodings disagree on how many bytes make a character, you can get a different number of characters entirely.
- **Encoding is a security surface, not just a display quirk:** input-validation bypasses and lookalike-character (homoglyph) attacks both come down to the same "same bytes, different reading" property.

## References

- The room's guided tasks and static-site character lookup on TryHackMe
