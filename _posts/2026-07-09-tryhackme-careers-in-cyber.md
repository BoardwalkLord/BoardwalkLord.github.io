---
title: "TryHackMe: Careers in Cyber — Learning Notes"
description: "My notes on the Careers in Cyber room — reading the main cybersecurity roles through the lens of someone thinking about building a business around them."
date: 2026-07-09 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [careers, blue-team, red-team, cybersecurity]
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

- **Room:** Careers in Cyber — [link](https://tryhackme.com/room/careersincyber)
- **Difficulty:** Info / Easy
- **What it teaches:** The main job roles in cybersecurity, what each one does, and where they sit on the offensive vs defensive divide.

I read this room a little differently from most people. The usual framing is "which career should I pick?" I read it as someone thinking about the field as a whole — which roles could be *services a business offers*, which I'd want to be strong in myself, and (my own added exercise) what kind of person I'd need to *hire* for each. That lens made a standard roles list more interesting to work through. Below, I've kept the room's actual description of each role separate from my own impressions, so I don't put words in the room's mouth.

## The roles

I've paraphrased what each role *is* in my own words rather than tracking the room's
descriptions, and kept my own reactions clearly marked as mine.

**Security Analyst.** A defensive, evaluation-focused role: study the organisation's
networks, work out where the weaknesses are, and turn that into concrete
recommendations for the engineers who'll act on them. A lot of it runs on talking to
people across the business to understand what's actually being protected. *My read:* the
role that has to be detail-oriented and personable at once, because so much of it depends
on getting information out of other people.

**Security Engineer.** The builders — they design and stand up the actual defences, and
have to account for a wide spread of attack types, from web-app exploits to network
threats to whatever's newly in fashion. *My read:* the most technically demanding role of
the set, and the one I'd expect to be hardest to hire well.

**Incident Responder.** A high-pressure, real-time role that lives or dies on having
plans and procedures ready *before* a breach, then executing them during and after one.
The room introduces the response-time metrics **MTTD, MTTA, and MTTR** — mean time to
*detect*, *acknowledge*, and *recover*. *My read:* suits someone who performs under
pressure on trained instinct and a clear procedure, not improvisation.

**Digital Forensics Examiner.** The detective role — gather and examine evidence while
staying inside proper legal process, whether supporting law enforcement or investigating
something internal like a policy breach. *My read:* fits someone observant, inquisitive,
and meticulous, with the discipline to follow correct procedure.

**Malware Analyst.** The role the room is most emphatic about on the technical side:
essentially a reverse-engineer, and it leans on serious low-level programming ability
(assembly, C). The work divides into picking apart the code without running it, and
running samples safely to watch what they do — the aim being to understand the malware,
detect it, and document it. *My read:* one of the deepest technical roles, not primarily
a communication one — though I'd add that reporting findings clearly (so a security
engineer can update defences) still matters.

**Penetration Tester.** Authorised, methodical hacking: sweep broadly across systems,
networks and web apps for flaws, then actually exploit them to gauge real risk, so the
organisation can fix things before a genuine attacker arrives. The emphasis is breadth —
find as many issues as possible. *My read:* the most independent, self-directed role of
the set.

**Red Teamer.** Related to pen testing but pointed at a different question. Where a pen
tester sweeps widely for vulnerabilities, a red teamer is there to test whether the
organisation can actually *detect and respond* — imitating a real adversary, staying
quiet, keeping access, avoiding notice, often over weeks and usually from outside. It
suits organisations whose security is already fairly mature. *My read:* the twist is that
this is the more patient, stealthier role — the goal isn't just to get in, it's to find
out whether anyone would even notice.

## What surprised me

Two things stood out once I'd been through the list.

First, the **Security Engineer** demands the highest raw technical proficiency of the set — more than I'd assumed going in.

Second — and this is my own inference, not something the room states — I noticed the engineer's suggested learning paths point toward *Junior Penetration Tester* and *Offensive Pentesting*. That made me think there's a natural progression from **engineer → pentester**: if you've spent time building defences and understanding systems deeply, you're well placed to then go looking for the ways *into* them. The builder's knowledge feeds the breaker's skill.

Third, the pentester-vs-red-teamer distinction wasn't what I'd assumed. I'd have guessed it was about solo vs team style; the room actually splits them on **scope and stealth** — broad vulnerability-hunting (pentest) versus targeted, stealthy adversary-emulation that tests whether an attack would even be detected (red team).

## Connects to my bigger goal

Two roles pulled at me, for different reasons.

The one I'm most genuinely **excited** by is **malware analysis** — and now that I've seen the room frame it as heavy reverse-engineering in low-level languages, I know it's also one of the more technically demanding paths to grow into. That's a challenge I find appealing rather than off-putting.

But I'm also strategically drawn to the **monitoring / analyst** side, for a business reason: a service built around monitoring clients' systems generates *stable, recurring monthly income* rather than one-off project fees. Those two pulls — the excitement of malware analysis and the stability of monitoring — aren't the same thing, and I'm noting honestly that both appeal to me for different reasons rather than pretending they resolve neatly.

## Lessons Learned

- **The reframe that stuck:** a list of job roles is also a list of *services a business can offer* and *functions you might hire for* — not just careers to choose between.
- **Corrected my own assumption:** pentester vs red teamer is about *scope and stealth* (broad sweep vs targeted, detection-evading emulation), not personality or team style.
- **Where I'm leaning:** excited by malware analysis (and now aware it's a deep, reverse-engineering-heavy path); strategically drawn to monitoring for its recurring-revenue model.
- **Hiring-lens thought (my own):** the personable analyst is probably the easiest hire; the scarce, highly technical engineer and the independent pentester, the hardest to staff and retain.

## References

- The room's guided tasks on TryHackMe
