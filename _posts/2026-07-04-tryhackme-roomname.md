---
title: "TryHackMe: [Room Name] — Walkthrough"
description: "My notes working through the [Room Name] room on TryHackMe: enumeration, foothold, and what I learned."
date: 2026-07-04 20:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [linux, enumeration, nmap]   # lowercase; 3–6 is plenty
toc: true
comments: true
# image: /assets/img/posts/roomname/banner.png   # optional social-preview image
---

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

## Overview

- **Room:** [Room Name] — [link](https://tryhackme.com/room/xxxx)
- **Difficulty:** Easy / Medium / Hard
- **What it teaches:** One or two sentences on the room's focus.

A 2–3 sentence summary of what I did and the single biggest thing I took away. *(Write this part last, once you know how it ended.)*

## Reconnaissance

Started with a full service scan:

```bash
nmap -sC -sV -oN nmap/initial.txt <TARGET_IP>
```

What stood out:

| Port | Service | Notes                     |
|-----:|---------|---------------------------|
| 22   | SSH     | OpenSSH x.x               |
| 80   | HTTP    | Apache — worth enumerating|

Which thread I pulled on first, and *why* I chose it over the others.

## Exploitation / Foothold

How I got initial access — the commands *and* the reasoning behind each, not just the one that worked.

```bash
# the actual steps
```

> Whatever tripped me up here, and how I got unstuck.
{: .prompt-tip }

## Privilege Escalation

*(Delete this section if the room has no privesc.)* The enumeration that revealed the path from initial user to root, and the exploit itself.

## Lessons Learned

The most valuable section — keep it honest:

- **New to me:** a tool or concept I met for the first time and now understand.
- **Where I got stuck:** the snag, and what actually fixed it.
- **Do differently:** what I'd try earlier next time.
- **Revisit:** anything I want to come back and study properly.

## References

- Docs, hints, or other writeups that helped.
