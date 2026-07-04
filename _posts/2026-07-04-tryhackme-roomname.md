---
title: "TryHackMe: [Room Name] — Learning Notes"
description: "My notes working through the [Room Name] room: enumeration, foothold, and what I learned."
date: 2026-07-04 20:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [linux, enumeration, nmap]   # lowercase; 3–6 is plenty
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

## Overview

- **Room:** [Room Name] — [link](https://tryhackme.com/room/xxxx)
- **Difficulty:** Easy / Medium / Hard
- **What it teaches:** One or two sentences on the room's focus.

A 2–3 sentence summary of what I did and the biggest thing I took away. *(Write this last.)*

## Reconnaissance

Started with a service scan:

```bash
nmap -sC -sV -oN nmap/initial.txt <TARGET_IP>
```

What stood out, and which thread I pulled on first — and *why* I chose it over the others.

## Exploitation / Foothold

How I got initial access — the commands *and* the reasoning behind each, not just the one that worked.

```bash
# the actual steps, in my own words
```

## Privilege Escalation

*(Delete if the room has no privesc.)* The enumeration that revealed the path from user to root, and the exploit itself.

## Lessons Learned

- **New to me:** a tool or concept I met for the first time.
- **Where I got stuck:** the snag, and what actually fixed it.
- **Do differently:** what I'd try earlier next time.
- **Revisit:** anything to come back and study properly.

## References

- Docs, hints, or other writeups that helped.
