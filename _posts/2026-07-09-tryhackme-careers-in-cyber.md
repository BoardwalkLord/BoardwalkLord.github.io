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

**Security Analyst.** The room describes them as integral to building security measures — exploring and evaluating company networks to produce actionable data and recommendations *for engineers* to act on, and working with *various stakeholders* to understand the security landscape. *My read:* this is the role that needs to be detail-oriented and personable at once, because so much of it depends on getting information out of other people.

**Security Engineer.** They *develop and implement* the security solutions, working across a breadth of attacks — web application attacks, network threats, evolving tactics — to reduce the risk of attack and data loss. *My read:* the most technically demanding role of the set, and the one I'd expect to be hardest to hire well.

**Incident Responder.** A highly pressurised, real-time role built on plans, policies, and protocols enacted during and after a breach. The room introduces the response metrics **MTTD, MTTA, and MTTR** — mean time to *detect*, *acknowledge*, and *recover*. *My read:* this suits someone who performs under pressure through trained muscle memory and a clear SOP, not improvisation.

**Digital Forensics Examiner.** The room literally opens with "if you like to play detective" — collecting and analysing evidence while *observing legal procedures*, whether for law enforcement or for investigating incidents like policy violations inside a company. *My read:* fits someone observant, inquisitive, and meticulous, with the discipline to follow proper legal process.

**Malware Analyst.** This is the one the room is most technically emphatic about: a malware analyst is *sometimes called a reverse-engineer*, and the work *requires a strong programming background, especially low-level languages like assembly and C*. It splits into static analysis (reverse-engineering the code) and dynamic analysis (running samples in a controlled environment) to work out what the malware does, how to detect it, and how to report it. *My read:* this is one of the deepest technical roles, not primarily a communication one — though I'd add that clearly reporting findings (e.g. to a security engineer, so defences get updated) still matters.

**Penetration Tester.** The room frames this neutrally: systematic ("systemised") hacking to uncover flaws and vulnerabilities across systems, networks, and web apps, then exploiting them to evaluate risk so the company can fix issues before a real attack. It's about breadth — finding *many* vulnerabilities to keep defences in good standing. *My read:* this feels like the most independent, self-directed role of the set.

**Red Teamer.** The room draws a specific line here: red teamers are *similar to pentesters but more targeted*. Where a pentester sweeps broadly for vulnerabilities, a red teamer is enacted to test the company's **detection and response** — imitating real criminals, emulating attacks, *maintaining access and avoiding detection*. Engagements can run up to a month, usually by an *external* team, and suit organisations with *mature* security programs. *My read:* the interesting twist is that this is arguably the stealthier, more patient role — the goal isn't just to break in, but to see whether you'd even be noticed.

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
