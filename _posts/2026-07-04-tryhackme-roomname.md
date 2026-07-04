---
title: "TryHackMe: [Offensive Security Intro] — Learning Notes"
description: "My notes working through the [Offensive Security Intro] room: enumeration, foothold, and what I learned."
date: 2026-07-04 08:30:00 +0800
categories: [Writeups, TryHackMe]
tags: [offensive security, hacking, careers]   # lowercase; 3–6 is plenty
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

- **Room:** [Offensive Security Intro] — [link](https://tryhackme.com/room/offensivesecurityintro)
- **Difficulty:** Easy
- **What it teaches:** Act like an ethical hacker and hack a fake bank.

A 2–3 sentence summary of what I did and the biggest thing I took away. *(Write this last.)*
Used Gobuster CLI to scan for hidden files and folders (that were not made private), then 

## Reconnaissance

Gobuster scans a website for files or folders by referring to a wordlist

```bash
gobuster -u http://fakebank.thm -w wordlist.txt dir
```

What stood out, and which thread I pulled on first — and *why* I chose it over the others.

## Exploitation / Foothold

How I got initial access — the commands *and* the reasoning behind each, not just the one that worked.

```bash
/images (Status: 301)
/bank-transfer (Status: 200)
```
Gobuster tells me which pages (Status:200)

## Privilege Escalation

I then enter the URL into the browser's address bar and access the admin page

## Lessons Learned

- **New to me:** Gobuster can scan for hidden admin pages
- **Where I got stuck:** Typing the commands for Gobuster
- **Do differently:** Understand that Gobuster needs you to specify the website and a wordlist
- **Revisit:** -

## References

- 
