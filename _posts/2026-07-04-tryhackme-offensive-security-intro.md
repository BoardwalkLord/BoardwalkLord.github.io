---
title: "TryHackMe: Offensive Security Intro — Learning Notes"
description: "My notes on the Offensive Security Intro room: using Gobuster to find an unlinked page on a fake banking app, and why hidden-but-not-protected fails."
date: 2026-07-04 08:30:00 +0800
categories: [Writeups, TryHackMe]
tags: [gobuster, web, enumeration, offensive-security]   # lowercase; 3–6 is plenty
toc: true
comments: true
---

{% comment %}
==================== SELF-REMINDER — NOT SHOWN ON THE PUBLIC PAGE ====================
This is a Liquid comment. Jekyll strips it at build time — it won't appear
on the blog OR in the rendered page source. It's just for me.

DO:
  - Write in MY OWN WORDS. Describe my approach and reasoning.
  - Frame as personal LEARNING NOTES, not an authoritative guide.
  - Link back to the room on TryHackMe.
  - Prefer FREE rooms. Avoid live/competition rooms until they're retired.
  - Match my claims to my actual skill level (no confident-but-wrong advice).

DON'T:
  - NO flags, answer tokens, passwords, or cracked hashes. Ever.
  - DON'T reproduce THM's own content — room text, task wording, their
    images, VM contents. Paraphrase; narrate my own process instead.
  - DON'T put anything commercial on this post — no "hire me", no ads,
    no selling services. THM's terms restrict commercial use of their
    content/platform. Business pitches live ONLY on my own original-content
    pages (CSA 2024 guide, my own tools), never wrapped around THM writeups.
  - DON'T disparage TryHackMe.
  - (For Hack The Box later: only RETIRED machines, never active ones.)

If in doubt on commercial use: email support@tryhackme.com before publishing.
=====================================================================================
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Offensive Security Intro — [link](https://tryhackme.com/room/offensivesecurityintro)
- **Difficulty:** Info / Easy
- **What it teaches:** The attacker mindset, and directory brute-forcing — finding pages on a website that exist but aren't linked anywhere.

This was my first proper hands-on room. The scenario is a deliberately vulnerable online bank called "FakeBank." The point is to show that a page being hidden is not the same as a page being protected — and that a simple tool can find the hidden ones in seconds.

## Reconnaissance

The bank's website only links to the normal customer pages you'd expect. But websites often have extra pages — admin panels, internal tools — that aren't linked from anywhere and that the owner assumes nobody will find.

The tool for finding them is **Gobuster**. It takes a wordlist of common page and folder names and requests each one against the target, watching which ones actually exist instead of returning "not found." It's essentially guessing URLs at high speed from a dictionary.

```bash
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- `dir` — brute-force directories/files
- `-u` — the target URL
- `-w` — the wordlist to guess from

Reading the output, most guesses came back as misses, but a handful returned a status code showing the page really existed — including one directory that clearly wasn't meant to be reachable by a customer.

## Exploitation / Foothold

That unlinked directory turned out to be an internal bank-transfer page — a form that could move money between accounts, sitting there with no login and no access control in front of it. Because I could reach it directly by URL, I could just use it.

> I'm not printing the exact path here — the learning point is the technique (brute-forcing reveals unlinked pages), not the specific answer to this room. Anyone doing the room will find it in seconds with the scan above.
{: .prompt-tip }

The "attack" was simply: open the page the scan revealed, fill in the transfer form, submit. No exploit code, no clever trick — the vulnerability was entirely that a sensitive page was left unprotected on the assumption that being unlinked made it safe.

## Lessons Learned

- **New to me:** Gobuster, and the whole idea of directory brute-forcing. I hadn't realised how much of "hacking" at this level is just enumeration — methodically discovering what's there — rather than dramatic exploits.
- **The core concept that stuck:** "security through obscurity" is not security. Hiding a page (not linking to it) does nothing if the page itself has no authentication. If an attacker can reach it, they can use it.
- **Where I got tripped up:** I first pointed Gobuster at the wrong URL/port and got nothing but errors — a good reminder to confirm the target is actually up and I'm hitting the right address before assuming the tool is misbehaving.
- **Do differently / revisit:** I want to understand HTTP status codes properly (200 vs 301 vs 403 vs 404), because reading them is what tells you a "hidden" page exists. That's clearly foundational and I'll come back to it.

## References

- Gobuster documentation
- The room's own guided tasks on TryHackMe
