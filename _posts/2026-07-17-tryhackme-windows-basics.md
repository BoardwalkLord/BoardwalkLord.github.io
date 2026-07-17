---
title: "TryHackMe: Windows Basics — Learning Notes"
description: "My notes on the Windows Basics room — read not as a beginner's tour but as an inventory of the security controls Windows already ships with, and how they map to the OS duties from the previous room."
date: 2026-07-18 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, windows, defender, firewall, least-privilege, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab values, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Windows Basics — [link](https://tryhackme.com/room/windowsbasics)
- **Difficulty:** Info / Easy
- **What it teaches:** A hands-on tour of the Windows desktop — logging in, File Explorer, the Settings app, Task Manager — and the built-in security tools (Windows Security and Windows Defender Firewall).

I'll be straight about my starting point: not much here was new to me. I've used Windows since my teens, including the parts of the experience nobody puts in a brochure — games on floppy disks that turned out to carry trojans, and a WordPress blog that got compromised back when that was almost a rite of passage. So rather than write this up as "how to use Windows," which would be dishonest coming from me, I read the room a different way: **as an inventory of the security controls Windows already ships with**, and treated my job as noting which ones actually matter to someone thinking about defence. That turned a familiar room into a genuinely useful exercise.

A note on format: this is a hands-on room with a lab, and I'm deliberately keeping to *what the tools are and what they're for* rather than any values the lab machine returns — go run it yourself for those.

## The framing: everything here is a security control if you look at it that way

The previous room ("Operating Systems: Introduction") left me with one idea I keep returning to: **the OS is a security layer in its own right, before any dedicated security software is installed.** Windows Basics is, in effect, the guided tour of that claim on a real system. Almost every "basic" feature in it is doing security work.

So here's my running inventory — the features I'd flag as most relevant to the cybersecurity side, and what each one is really doing.

### Login and authentication — the OS asks "who are you?" first

Sitting one layer above the firmware, the login screen is one of the earliest security gates a user actually meets. Before you touch anything, the OS is already doing two distinct jobs: **authentication** (prove who you are) and then **authorisation** (hand you the permission set that identity is entitled to). That's the "user management" duty from the last room, made concrete — and it's happening before the desktop even appears.

### Three account types — least privilege with Windows names on it

Windows splits accounts into three levels, and read as a security control this is simply **least privilege** made visible:

- **Guest** — minimal permissions, temporary access, can't change system settings.
- **Standard** — everyday use: run apps, change your own preferences, but no system-wide changes.
- **Administrator** — full control: install software, change configuration, manage other users.

The principle underneath is that the *least* powerful account goes to the *most casual* use, and admin rights are held back for when they're genuinely needed. Which is exactly why one detail in the room is worth naming out loud: the lab has you running as **Administrator the whole time**. That's fine for a sandbox built for learning — but it's precisely the habit security guidance warns against for daily work, because if malware lands while you're running as admin, it inherits admin's power. Running day-to-day as a Standard user is one of the cheapest, oldest mitigations there is. (I'd put that as a strong default, not an absolute guarantee — privilege-escalation attacks exist precisely to climb from Standard to admin — but the wall is real and worth having up.)

### Settings worth knowing where to find

Two spots in the Settings app earn their place in a security inventory:

- **About your PC** — a single overview of the device's security, hardware and OS information. The natural first stop when you want to know *what you're actually standing on* before assessing it.
- **Update & Security** — because keeping the OS updated *is* a security control. Updates carry the patches that close known holes; an unpatched system is vulnerable to problems that already have fixes sitting unapplied. "Patch promptly" is unglamorous and it's one of the highest-value things anyone can do.

### Task Manager — actually seeing the processes the OS schedules

Last room I described "process management" as an abstraction — the OS creating, scheduling and terminating processes. Task Manager is where that abstraction becomes something you can *watch*: running apps and background processes, live CPU and memory use, logged-in users, and a details view with process IDs. As a defender's tool it's the first place you'd look to spot something consuming resources it shouldn't, or a process that has no business running. The abstract duty from the previous room now has a window I can open and see it working.

### Windows Security — the built-in layer, before any third-party tool

This is the part that most directly continues the "OS is already a security layer" thread. Windows ships with a security dashboard covering, in its own words, four areas — virus & threat protection, firewall & network protection, app & browser control, and device security — and they're on by default. The point I want to hold onto isn't the feature list; it's that **a fresh Windows box is not defenceless while you shop for an antivirus.** There's already a baseline layer running. Third-party tools add to it; they aren't filling a void.

### Windows Defender Firewall — and the three network profiles

The built-in firewall does the classic job: watch network connections and allow or deny them by rule. The detail worth remembering is that it applies **different rule sets depending on where you are**:

- **Domain** — when the machine is on an organisation's managed network.
- **Private** — a trusted network, like home or a lab.
- **Public** — untrusted networks such as café or airport Wi-Fi, where it applies the strictest posture.

That's a genuinely sensible design: "trust" isn't one setting, it's context-dependent, and the firewall changes its strictness to match the network's trustworthiness. Its advanced view exposes the actual inbound and outbound rules, which is where you'd go to see — or shape — exactly what the machine is allowed to talk to.

## Connects to my bigger goal

Two threads tie together here.

**First, the stack keeps building.** Across the last few rooms I've been assembling a picture: firmware sits below the OS, the kernel is the privileged core, and security tools run up in user space. Windows Basics adds the concrete, branded versions of the OS-level controls that live in that picture — login/authentication, account tiers, the built-in security dashboard, the firewall. It's the same architecture I sketched in the abstract, now with real tools I can point at.

**Second, this is the vocabulary layer under the access-control principles I keep meeting in compliance work.** Least privilege, authentication, patch management, network segmentation by trust level — the frameworks talk about these as requirements, and here they are as actual switches and account types on an actual OS. Seeing the mechanism first makes the requirement read less like paperwork and more like "use the thing that's already in front of you, properly." (As always: the *principle* is universal — I'm not implying every reader is under a legal duty to configure any of this. But the underlying security value of doing so doesn't depend on being regulated.)

## What surprised me / where I got stuck

Honestly, nothing on either count — this is an introductory, hands-on room and I came in already familiar with the environment. I'd rather record that plainly than invent a revelation. The value for me wasn't in *learning* the tools; it was in the deliberate act of re-reading a familiar desktop as a **list of security controls** rather than a list of conveniences. That reframing is the takeaway, even when the individual features aren't new.

## Revisit

- **Windows Defender Firewall's advanced rules** — I want to come back and actually read a rule set properly: what a well-scoped inbound/outbound rule looks like, and how you'd reason about tightening one.
- **Running as Standard vs Administrator in practice** — the mechanics of day-to-day least privilege on Windows (and where User Account Control fits), since that's the single habit with the best effort-to-protection ratio.
- **The CLI** — the room notes the next rooms move to the command line for both Linux and Windows. The GUI is the friendly face; I suspect the command line is where the real control (and the real defensive visibility) lives.

## Lessons Learned

- **The reframe that made a familiar room useful:** read the Windows "basics" as an inventory of built-in security controls, not a beginner's tour.
- **Account types = least privilege made concrete** — and running as Administrator by default is the anti-pattern to notice, not the model to copy.
- **Windows is not defenceless out of the box:** Windows Security and Defender Firewall are a baseline layer that exists before any third-party tool.
- **Trust is contextual:** the firewall's Domain/Private/Public profiles bake "how much should I trust this network?" straight into the rules.

## References

- The room's guided tasks and hands-on lab on TryHackMe
