---
title: "TryHackMe: Computer Types — Learning Notes"
description: "My notes on the Computer Types room — the eight types of computer, why every design is a trade-off, and why the ones you can't see are the ones that go missing from an asset inventory."
date: 2026-07-15 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, hardware, iot, embedded, asset-inventory]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Computer Types — [link](https://tryhackme.com/room/computertypes)
- **Difficulty:** Info / Easy
- **What it teaches:** The main types of computer — the ones you sit in front of, and the ones hiding inside everyday objects — and why no single design can do everything.

This is a beginner room, and I'll be honest that I already use most of these machines daily. What it gave me wasn't new hardware knowledge so much as a cleaner way to *categorise* it — and one genuinely useful idea (every design is a trade-off) that I'd never actually put into words before. I've written it up through the lens I keep coming back to: if you're responsible for securing an organisation, which of these computers are you most likely to forget you own?

## Two ways to sort a pile of computers

The room sorts computers along two axes, and separating them was the part that sharpened my own thinking.

**By how you use them — the ones you sit in front of:**

- **Laptop** — portable, everyday computing. The catch is heat: cooling a machine that small and battery-powered is genuinely hard, which is why mine slows down under a long task.
- **Desktop** — fixed in one place, running on wall power with room to cool properly, so it holds performance far longer.
- **Workstation** — looks like a desktop, built differently. Tuned for accuracy and reliability on serious professional work (simulations, 3D), using components chosen to reduce errors during long computations.
- **Server** — no screen, runs around the clock, answering many users' requests at once. You never sit at one, but everything you use is downstream of them.

**By visibility — the ones you *don't* sit in front of:**

- **Smartphone** — a pocket computer whose components are optimised around battery life and staying connected.
- **Tablet** — touch-first, larger screen; the middle ground between phone and laptop.
- **IoT device** — a network-connected device with a single job: report data or take commands over a network (thermostat, smart doorbell, fitness tracker).
- **Embedded computer** — a chip built *into* another device to make it work, often quietly for years (the controller in a coffee maker, the sensor in an automatic door).

## The one distinction worth slowing down on: IoT vs embedded

These two sound like the same thing, and the room draws the line on a single axis that turns out to matter: **connectivity.**

An **IoT** device talks to a network — that's the whole point of it. An **embedded** computer just does its job inside the machine and may never connect to anything at all.

That's not trivia. It's a security distinction. The connected device is the one with an **attack surface** — something reachable, something that can be probed or spoofed or recruited. The embedded chip is a different kind of problem: not usually an entry point, but *invisible*, and invisible things don't make it onto lists.

## What surprised me: every design is a trade-off

This was the line that actually stuck, because I'd never phrased it for myself: **there is no best computer, only the right tool for the job — because every design is a trade-off.**

Once it's said, it's obvious, and I can see it running through the whole list:

- A laptop buys **portability** and pays for it in sustained performance and cooling.
- A server buys **constant availability and reliability** — often through redundancy, spare power supplies and disks — and pays for it in cost and the fact that no human sits at it.
- An IoT device buys **connectivity** and, though the room doesn't say this part, pays for it in exactly the way I noticed above: the moment you connect something to a network to make it convenient, you've also given it an attack surface it didn't have before.

Prioritise one property and you concede others. That's not a flaw in any single machine; it's the shape of the whole field.

## Connects to my bigger goal

Here's where a beginner room turned out to be more useful than I expected.

Part of keeping any organisation secure is knowing where all its computers actually *are* — and this room is a catalogue of the ways that's harder than it sounds. The visible machines are easy: laptops, desktops, workstations, servers. You can walk the office and count them. It's the **IoT and embedded** devices that quietly don't get counted — the smart doorbell, the networked sensor, the chip in a piece of equipment nobody thinks of as "a computer."

And you can't assign a risk level to a device you never wrote down. Enumerate first, then classify, then decide how to treat each one — but only if it made the list in the first place. This room is really a tour of the things most likely to be *missing* from that list.

That's the same thread the "Inside a Computer System" room opened for me — *you can't defend what you haven't enumerated* — except this one shows you exactly where the gaps hide. It's also why the serious security frameworks I've been reading make asset inventory an explicit, itemised requirement, IoT devices named as their own category rather than lumped in with "IT equipment." Having now seen how easily those devices vanish into the furniture, I understand *why* the frameworks are so insistent about it.

(One honest qualifier so I don't overstate it: most small businesses aren't under a *legal* duty to keep a formal asset inventory — that obligation attaches to specific regulated entities. But the underlying security *principle* is universal. You don't need to be legally required to enumerate your devices to benefit from knowing where they all are.)

## Where I got stuck

Nothing — it's an introductory room and clearly laid out. The only part I sat with wasn't a difficulty but a thought worth keeping: the IoT-vs-embedded line, because that's where "types of computer" quietly turns into "types of risk."

## Revisit

- **IoT as an attack surface.** The room told me what IoT *is*; I want to come back to how these devices actually get attacked and why they're so often the weak point on a network.
- **Embedded systems and the "invisible inventory" problem.** If embedded chips run for years unnoticed, how does anyone responsible for security ever account for them? That feels like a real, unresolved question rather than a beginner one.

## Lessons Learned

- **Cleaner categories:** computers sort two ways — by how you use them (laptop / desktop / workstation / server) and by visibility (smartphone / tablet / IoT / embedded).
- **The idea that stuck:** every design is a trade-off; prioritising one property (portability, availability, connectivity) always concedes others.
- **The security reframe:** the computers hardest to secure are the ones you forget you own — and connectivity is the line where a convenient device also becomes an attack surface.
- **The through-line:** you can't assign risk to a device you never enumerated, and IoT/embedded devices are the ones that go missing from the list.

## References

- The room's guided tasks and static site on TryHackMe
