---
title: "TryHackMe: Offensive Security Intro — Learning Notes"
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

- **Room:** Offensive Security Intro — [link](https://tryhackme.com/room/offensivesecurityintro)
- **Difficulty:** Easy
- **What it teaches:** Got into the shoes of an ethical hacker and performed a simple hack of a fake bank. Got to know a few possible career branches in the field of offensive security.

## Scan

Used Gobuster to scan a website for hidden admin pages by referring to a wordlist

```bash
gobuster -u http://fakebank.thm -w wordlist.txt dir
```

The results as below:

```bash
/images (Status: 301)
/bank-transfer (Status: 200)
```
The result with "Status:200" are the hidden admin pages

## Exploit

I can then enter the full path of the admin page in the browser address bar to access the admin page, and perform the hack.

## Careers

I'm introduced to 3 available careers in the field of offensive security: Penetration Tester, Red Teamer, Security Engineer.


## Lessons Learned

- **New to me:** Use Gobuster to scan for hidden admin pages
- **Where I got stuck:** Getting the CLI correct for Gobuster
- **Do differently:** Understand that Gobuster needs you to specify the website and a wordlist
- **Revisit:** -

## References

- 
