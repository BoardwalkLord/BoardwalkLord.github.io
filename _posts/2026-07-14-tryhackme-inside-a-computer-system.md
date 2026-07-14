---
title: "TryHackMe: Inside a Computer System — Learning Notes"
description: "My notes on the Inside a Computer System room — meeting the components I used to upgrade for gaming, this time as an attack surface."
date: 2026-07-14 09:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, hardware, boot-process, uefi, blue-team]
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

- **Room:** Inside a Computer System — [link](https://tryhackme.com/room/insideacomputer)
- **Difficulty:** Info / Easy
- **What it teaches:** The core components of a computer system, and the sequence of events between pressing the power button and arriving at a working operating system.

I'll be honest about where I was starting from: I've been inside a PC case plenty of times. But I learned these components as a **gamer**, not as someone thinking about security — and going back over them with a different question in mind turned out to be more interesting than I expected.

## The components, as I already knew them

Everything I knew about computer hardware, I learned by asking one question: *will this run the game?*

- **RAM** — whether the game would run *smoothly*. The perennial upgrade, always in pursuit of better performance.
- **GPU** — more memory, for higher resolution and smoother graphical output. Purely about what came out of the screen.
- **HDD → SSD** — the HDD upgrade was about *size*, to fit bigger games. Moving to an SSD was about two things: escaping the corruption risk that comes with spinning, moving parts, and raw speed — which is why it's the natural home for the boot drive.
- **CPU** — faster processing. The upgrade I did *least* often, because in practice it usually meant replacing the whole machine. The most expensive move on the board.
- **PSU** — the one that taught me a lesson the hard way. I tend to leave the machine on overnight (asleep rather than off), and every so often a power surge would take the PSU out. I was replacing it every couple of years.
- **Network adapter** — I only really thought about this when consumer internet speeds jumped and the adapter became the bottleneck.

Reading that list back, every single item is framed as a **performance** decision. Not one of them is framed as a component that could be attacked, or that needs to be inventoried, owned, or protected. That's the shift this room quietly started.

## Genuinely new: the boot process

This was the part I'd never actually looked at. In my own words, the sequence runs:

1. **Press the power button.** The PSU opens the gate and lets electricity reach the components.
2. **Firmware starts.** The UEFI (or the older BIOS it largely replaced) wakes up. Worth being precise here, because it turns out to matter: this firmware doesn't live on your disk — it sits on a chip on the motherboard. It runs *before* any operating system exists.
3. **POST — Power-On Self Test.** The firmware checks that the required components are actually present, correctly configured, and functioning.
4. **Select the boot device.** The firmware holds an *ordered* list of devices to search for a bootloader, and works down it by priority.
5. **Run the bootloader.** The bootloader pulls the operating system off the chosen device and into RAM. Control of the hardware is then handed over from the firmware to the OS.

Written out like that, one thing jumps out. There is a whole sequence of things happening — decisions being made, code being executed, control being handed over — **before the operating system exists at all.**

## What this reframed for me

Two things, and the second one is the reason I'm glad I did an "easy" room properly rather than skipping it.

**First: components aren't just performance dials. They're surface area.** The more components a system has, the more places there are for something to go wrong or to be attacked. That's obvious once said, but it's genuinely not how I used to look at a spec sheet.

**Second — and this is the one that actually landed:** while working through the Security+ syllabus I remember coming across malware that specifically abuses the boot process to hide itself. At the time it was just a term. Now I can see *why* it works. If firmware runs before the OS, and firmware hands control to the OS, then anything that establishes itself down at that level is sitting **underneath** the operating system — and therefore underneath most of the security tooling, which runs *inside* the OS. Your defences are looking around a room they've been handed, without being able to see who handed it to them.

The room mentions in passing that attackers target the boot process. It doesn't explain how. But it gave me enough of the sequence that the *shape* of the attack now makes sense, which is more than I expected from an introductory room.

## Connects to my bigger goal

The room opens with an argument I found unexpectedly familiar: **you cannot defend what you don't understand.**

I've been spending a lot of time in compliance frameworks lately, and they say the same thing in bureaucratic language — that before any of the interesting security work can happen, you need an **inventory**: what devices do you have, what's on them, who owns each one, how sensitive is it. It always reads like paperwork. Reading this room, it stops reading like paperwork. You can't reason about risk to a system you haven't enumerated, for the same reason you can't defend a building you've never walked through.

Same argument. One version is a beginner room, the other is a national regulation. I didn't expect those to rhyme.

## Where I got stuck

Nothing, honestly. This is an introductory room and it's clearly written. I'd rather say that than invent a difficulty.

The one place I had to slow down and re-say it in my own words was **step 5** — specifically the handover, where the firmware gives up control of the hardware to the OS. Not because it's difficult, but because it's the step that makes the rest of the picture click, and skimming it would have cost me the point above.

## Revisit

- **Bootkits and firmware-level malware** — what running below the OS actually buys an attacker, and what it costs them.
- **Secure Boot** — the obvious defensive counterpart. If the boot chain is the weakness, what is it that verifies each link?
- **The firmware/OS trust boundary in general.** This feels like the first genuinely important boundary I've met, and I suspect it won't be the last.

## Lessons Learned

- **The reframe:** I knew every one of these components already — but only as performance dials. Meeting them as *attack surface* is a different subject wearing the same words.
- **The boot process is a sequence of trust handovers**, and the operating system is nowhere to be seen for most of it.
- **"You can't defend what you don't understand"** is not a slogan. It's the reason asset inventories exist, and it's why they're the first thing every serious framework asks for.

## References

- The room's guided tasks and static site on TryHackMe
