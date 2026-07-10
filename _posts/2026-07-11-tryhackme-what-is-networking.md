---
title: "TryHackMe: What is Networking? — Learning Notes"
description: "My notes on the What is Networking room: networks and the internet, how devices are identified by IP and MAC addresses, MAC spoofing, and ping."
date: 2026-07-11 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [networking, ip-address, mac-address, fundamentals]
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

- **Room:** What is Networking? — [link](https://tryhackme.com/room/whatisnetworking)
- **Difficulty:** Info / Easy
- **What it teaches:** What a network and the internet actually are, how devices identify themselves (IP and MAC addresses), how MAC addresses can be spoofed, and the basics of ping.

This is the first properly technical foundational room for me — the intro rooms so far were conceptual. Networking is the layer everything else in security sits on, so I tried to actually internalise this one rather than skim.

## Networks and the internet

A network is just *things connected* — the room uses everyday examples (a group of friends, a postal system, a power grid) before applying the idea to devices. The internet, then, is one giant network made up of many smaller ones.

The distinction that clarified things for me: the internet is a web of **private networks** (small, local networks) joined together by **public networks**. That reframed my old mental picture of the internet as one flat, equal mesh of computers — it's really lots of little private networks stitched together.

*My own inference:* if the internet is private networks joined to a public one, it makes sense that a private network would often want to control what crosses the boundary — what its internal devices can reach on the wider internet, and what from outside can reach in. I later realised that's essentially what a firewall does, and the room's MAC-spoofing example (below) is exactly a firewall making allow/deny decisions.

## How devices are identified: IP and MAC

The room uses a nice analogy: devices are identified two ways, like humans having both a **name** (changeable) and **fingerprints** (permanent).

- **IP address** — the changeable "name." A set of four octets identifying a device on a network *for a period of time*; it can later be reassigned to another device. Follows protocols so devices all "speak the same language."
- **MAC address** — the permanent "fingerprint." A twelve-character hexadecimal number assigned to the device's network interface at the factory, e.g. `a4:c3:f0:85:ac:2d`.

One MAC detail I found interesting: it's split in two. The **first six characters identify the manufacturer** of the network interface, and the **last six are a unique number** for that specific device. So the MAC itself tells you who made the hardware.

## Private vs public IP — the bit I had to reread

My snag: I couldn't see why a device would have *both* a private and a public IP address. Working through it, the room's own example made it click, and I landed on a hotel analogy:

- A **private IP** (like `192.168.1.77`) is your **room number** — used to reach other devices *inside* the same local network.
- The **public IP** (like `86.157.52.21`) is the **hotel's street address** — how the outside world (the internet) reaches you.

So when one guest phones another guest in the same hotel, they use room numbers (private IPs). When a guest contacts someone at a *different* hotel, what matters is the hotel address (the public IP). In the room's example, two devices on the same private network each have their own private IP but share a *single* public IP for anything they send to the internet.

**The "why" I was missing:** this setup exists largely because of **IPv4 address exhaustion**. IPv4 only has about 4.29 billion addresses (2³²), and we've been running out — so instead of giving every device its own public address, many devices share one public IP (through their router) while using private IPs internally. **IPv6** (2¹²⁸ addresses — a trillions-plus pool) is the long-term fix. So the hotel analogy is the *how*; the address shortage is the *why*.

## MAC spoofing — and why it matters

I already knew every device has a unique MAC, but I didn't know MACs can be **faked ("spoofed")** — a device pretending to be another by using its MAC address.

The important part is *why that's a problem*: it breaks security designs that assume devices on a network are trustworthy. The room's example: a firewall set to allow traffic to/from the administrator's MAC. If an attacker spoofs that MAC, the firewall is fooled into treating them as the admin. That's a clean illustration of why "identify by MAC" is weak security on its own.

The room's interactive lab makes this concrete with a **hotel Wi-Fi** scenario: guests who've paid get their traffic through, while an unpaid guest's packets get dropped — until he spoofs a paid guest's MAC and slips through. I'd vaguely known hotels/cafés throttle or gate Wi-Fi speed by payment; seeing it done *via MAC control* — and seeing how trivially spoofing defeats it — was the useful part.

## Ping (ICMP)

Ping is a basic tool to check whether a connection between devices exists and how it's performing. It uses **ICMP** (Internet Control Message Protocol) packets — an echo request out, an echo reply back — and measures the round-trip time. It's built into Linux and Windows; the syntax is just `ping <IP or URL>`.

## Lessons Learned

- **Reframed:** the internet isn't a flat mesh of equal computers — it's many private networks joined by public networks.
- **The reread that paid off:** private vs public IP finally clicked via the hotel analogy (room number vs street address), and I connected it to *why* it exists — IPv4 address exhaustion, with IPv6 as the fix.
- **New and useful:** MAC addresses can be spoofed, which breaks any security that trusts a device purely by its MAC — a concrete lesson in not assuming identity equals trust.
- **A thought of my own (speculative):** I noticed foundational tech often starts as government/defence research and later finds commercial uses — nuclear weapons research → nuclear energy being the clearest case. (Note: the *World Wide Web* is sometimes cited here, but it's actually a counter-example — Tim Berners-Lee released it royalty-free rather than commercialising it, so I've dropped it from this theory.)

## References

- The room's guided tasks on TryHackMe
