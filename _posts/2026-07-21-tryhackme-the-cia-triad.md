---
title: "TryHackMe: The CIA Triad — Learning Notes"
description: "My notes on the CIA Triad room — confidentiality, integrity, availability, and the real mechanism that defends each one."
date: 2026-07-21 09:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, cia-triad, confidentiality, integrity, availability, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab drag-drop answers, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** The CIA Triad — [link](https://tryhackme.com/room/theciatriad)
- **Difficulty:** Info / Easy
- **What it teaches:** The three things cyber security actually protects — Confidentiality, Integrity, and Availability — and using them as a mindset for assessing any security incident.

This is a short, conceptual room, and it's the first one in the path that's explicitly *about* security rather than the plumbing underneath it. The idea is simple enough to state in a sentence, so rather than pad it, I've used my own space to connect each pillar to the real mechanism that defends it — pulling in things I'd half-remembered from the Security+ syllabus.

## The one idea: what "secure" actually means

The reframe I got from this room: I'd carried a physical-world sense of "security" as *stopping attacks*. In cyber security, "secure" means something more specific — keeping digital data in three particular conditions. Everything else is downstream of those three.

The three pillars, and the plainest way I can put each:

- **Confidentiality** — the data stays **secret** (only authorised people can see it).
- **Integrity** — the data stays **unchanged** (nobody alters it without permission).
- **Availability** — the data stays **accessible** (it's there when authorised people need it).

Secret, unchanged, accessible. That's the whole triad, and the room's point is that every incident you'll ever meet is really a failure of one or more of these three.

## Each pillar, and the mechanism that defends it

The room explains *what* each pillar is with everyday analogies. What it deliberately leaves for later is *how* each one is enforced. That "how" is the part I already had fragments of, so I've filled it in — it's what made the room click for me rather than just being definitions.

### Confidentiality → encryption and access control

Keeping data secret. The room's example is credentials intercepted over a coffee-shop Wi-Fi.

The main mechanism is **encryption**, and specifically the public-key (asymmetric) kind is worth getting right because I'd half-remembered it. It uses a *pair* of keys: you encrypt a message with the recipient's **public** key (which anyone may have), and only the recipient's matching **private** key (which they alone hold) can decrypt it. So the important nuance is *which key does what* — you encrypt with the recipient's public key, they decrypt with their private one. Alongside encryption sits **access control** (only the right people are permitted in at all), reinforced by **2FA / MFA** — requiring a second factor beyond a password, sometimes a physical hardware key.

### Integrity → hashing

Keeping data unchanged. The room's example is a bank transfer whose destination account is altered mid-transaction.

The mechanism here is the one that connects straight back to my **Data Representation** notes. When you download software, the vendor often publishes a **hash** of the genuine file — that long hex string I met earlier (e.g. a SHA-256 value). A hash is a fingerprint of the exact bytes: change a single byte and the hash comes out completely different. So you can hash the file you downloaded and compare it to the published one — if they match, the file wasn't tampered with in transit; if they differ, something altered it (corruption, or a bad actor swapping in malware). That's integrity enforced in practice, and it's literally the "hex fingerprint of bytes" idea from two rooms ago, now with a name and a job. (In my notes I'd called it a "hashstring" — the actual term is just a *hash* or *hash value*.)

### Availability → redundancy

Keeping data and services accessible. The room's examples are a power failure locking you out of your own bank, and a flood of traffic knocking a website offline.

The general mechanism is **redundancy** — having spares so a single failure doesn't stop the service:

- **Storage:** identical copies across multiple disks, so one failing disk doesn't lose the data. The term I was reaching for is **RAID** (Redundant Array of Independent Disks). (My notes guessed "RAID? NSAID?" — NSAID is the painkiller. Easy to mix up at speed.)
- **Power:** backup generators and a **UPS** (uninterruptible power supply), exactly the "alternative power generator" the room describes for the bank.
- **Traffic floods (DDoS):** when attackers overwhelm a site with requests, the defence is **DDoS mitigation** — services (Cloudflare is the well-known one) that absorb or filter the flood so the site stays up. The category name is the bit I'd forgotten: *DDoS mitigation*.

## The security mindset

The room's closing point is the useful one: the CIA Triad isn't just three definitions, it's a **checklist for reading any incident**. When something happens, you ask: was data *exposed* (confidentiality), *modified* (integrity), or made *unavailable* (availability)? That tells you what was hit and shapes the response.

I can see why this is taught first. It's the frame every later topic hangs on — a breach, a ransomware attack, a defaced website each map cleanly onto one or more of the three. Ransomware, for instance, is interesting because it can hit *two* at once: it encrypts your files (attacking **availability** — you can't reach them) and sometimes threatens to leak them (attacking **confidentiality**).

## Connects to my bigger goal

Two things stood out for the direction I'm heading.

First, the triad is the vocabulary that the compliance frameworks I've been reading are built on. Risk assessments ask you to rate the impact of a threat on confidentiality, integrity, and availability — the same three, used as scoring axes. Having the plain-language version straight ("secret, unchanged, accessible") makes those frameworks read less like jargon.

Second, the "mechanism behind each pillar" exercise above is the useful bridge: a client doesn't want to hear "ensure availability," they want to know *how* — redundancy, backups, DDoS protection. Being able to move from the pillar to the concrete control is the difference between reciting a principle and giving advice.

## Where I got stuck

Nothing — it's a short, clearly written conceptual room. The only real work was on my side: turning "I vaguely remember a term for this" into the correct names (hash, RAID, UPS, DDoS mitigation, asymmetric encryption). That retrieval was the actual value of the room for me, more than the definitions themselves.

## Revisit

- **Asymmetric vs symmetric encryption** — I've got the public/private-key idea; I want the full picture of when each type is used and why.
- **Hashing in depth** — how a hash is actually computed, following on from the Data Representation room.
- **Beyond CIA** — I've seen mentions that the triad gets extended (with things like authenticity and non-repudiation); worth knowing what those add and why.

## Lessons Learned

- **"Secure" means three specific conditions:** data stays secret (confidentiality), unchanged (integrity), and accessible (availability) — not just "attacks stopped."
- **Each pillar has a real mechanism:** encryption and access control for confidentiality, hashing for integrity, redundancy (RAID, UPS, DDoS mitigation) for availability.
- **Integrity's mechanism is the hash from the Data Representation room** — a hex fingerprint of bytes, now with a job: change one byte, the hash changes, so a mismatch means tampering.
- **The triad is a mindset:** read any incident by asking which of the three was hit — and note some attacks (like ransomware) hit more than one.

## References

- The room's guided tasks and hands-on exercise on TryHackMe
