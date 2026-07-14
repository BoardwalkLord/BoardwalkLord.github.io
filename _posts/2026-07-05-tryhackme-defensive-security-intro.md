---
title: "TryHackMe: Defensive Security Intro — Learning Notes"
description: "My notes on the Defensive Security Intro room: what blue teams actually do, inside a SOC, and the basics of DFIR."
date: 2026-07-05 10:25:00 +0800
categories: [Writeups, TryHackMe]
tags: [blue-team, soc, dfir, defensive-security]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }
> **On sources & TryHackMe's material:** These are independent learning notes in my
> own words. They describe *my* experience of the room and deliberately reproduce none
> of TryHackMe's room text, task content, screenshots, flags, or answers — go do the
> room to get those. Room names and the linked URL are used for reference only.
> TryHackMe and its content are the property of TryHackMe Ltd; this post is not
> affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Defensive Security Intro — [link](https://tryhackme.com/room/defensivesecurityintro)
- **Difficulty:** Info / Easy
- **What it teaches:** The defender's side of security — what a blue team does day to day, what happens inside a Security Operations Centre (SOC), and the basics of digital forensics and incident response.

After the offensive intro room, this was the other half of the picture: instead of *being* the attacker, you're the one who has to notice the attacker and respond. It reframed security for me as a two-sided contest rather than just "hacking."

## Offensive vs defensive — the mental model

The previous room was the **red team** view: find the way in. This room is the **blue team** view: assume someone is trying to get in, and build the ability to detect and respond when they do. The key shift is that defence is less about a single clever move and more about *continuous vigilance* — monitoring, noticing the abnormal, and reacting fast. A defender has to be right constantly; an attacker only has to be right once. That asymmetry is why so much of defence is about process and speed, not heroics.

## Inside a SOC (Security Operations Centre)

A SOC is the team (and the room) responsible for watching an organisation's systems for signs of trouble. From the room, the things a SOC keeps an eye on fall into a few buckets:

- **Vulnerabilities** — weaknesses that could be exploited. Where they can't be patched immediately, the SOC has to find other ways to reduce the risk.
- **Policy violations** — people doing things they're not supposed to (e.g. uploading company data somewhere it shouldn't go).
- **Unauthorised activity** — signs that a stolen login is being used, for instance.
- **Network intrusions** — catching a breach as early as possible to limit the damage.

One sub-area that stuck with me is **threat intelligence** — gathering information about who might attack you and how, so your defence is informed by the actual adversary rather than guesswork. Defence works better when it's *specific* to the threats you actually face.

## DFIR — digital forensics & incident response

The other big topic was DFIR, which is really two linked things:

- **Digital forensics** — the investigative side: examining evidence like system logs, files, and network traffic to reconstruct what happened after an incident.
- **Incident response** — the structured way an organisation reacts to a breach, so that a bad day doesn't turn into a catastrophe. The room framed it as phases (preparation, detection, response, recovery), which made it click that response is a *plan you build in advance*, not something you improvise mid-crisis.

**Malware analysis** sits under here too — studying malicious software to understand what it does, which feeds back into both cleaning up and defending against it next time.

> This connects straight to the compliance side I'm interested in: the incident-response phases here are basically what a business needs documented in advance to meet its obligations. Same idea, seen from the regulation side.
{: .prompt-tip }

## The hands-on part

The room finishes with an interactive exercise where you step into the SOC analyst's seat and work through a simulated situation — reading what the system is telling you and deciding how to react. I'm deliberately not walking through the specific steps or the answer here; the value for a reader is the *concept* (a SOC analyst investigates signals and responds), and anyone doing the room will work through the guided task themselves.

What it drove home: defence is an *active* job. You're not just waiting for an alarm — you're interpreting ambiguous information and making a call.

## Lessons Learned

- **New to me:** Think of a SOC as the always-on watch — its job is to keep looking for signs that someone's trying to get in, continuously rather than in bursts. DFIR takes action after an intrusion has occurred.
- **What surprised me:** Defence is a heavy responsibility — you have to be right all the time, at a moment when attackers keep getting more creative and only need to be right once.
- **Connects to my bigger goal:** DFIR and malware analysis are genuinely interesting, and the more I understand them, the better I can serve clients.
- **Revisit:** Malware analysis — I want to understand how malware actually works, and how it gets neutralised.

## References

- The room's guided tasks on TryHackMe
