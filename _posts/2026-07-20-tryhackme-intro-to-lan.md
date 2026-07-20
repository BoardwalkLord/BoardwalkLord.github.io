---
title: "TryHackMe: Intro to LAN — Learning Notes"
description: "My notes on the Intro to LAN room — network topologies, subnetting as a security boundary, ARP, DHCP, and the pattern I keep meeting: identity without verification is not security."
date: 2026-07-20 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [networking, lan, arp, dhcp, subnetting, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no lab break-steps/values, no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Intro to LAN — [link](https://tryhackme.com/room/introtolan)
- **Difficulty:** Info / Easy
- **What it teaches:** How a local network is physically arranged (topologies), how one network is split into smaller ones (subnetting), and two protocols that make a LAN work — ARP (matching IP addresses to hardware) and DHCP (handing out IP addresses automatically).

I'd met "network" and "topology" as abstract ideas in other subjects years ago — as maths and geography concepts, with no physical weight. This room revisits them as real, physical arrangements with real consequences, and looks at each through a security lens. That reframing is the thread of these notes: for each piece, what happens when it fails or is abused.

## Topologies — and an honest complaint

The room covers three ways to physically arrange a network. I'll summarise them, then say what frustrated me about the comparison.

**Star.** Every device connects to a central device (a switch or hub). All traffic passes through that centre. It's the most common design today. *Pros:* easy to scale — add a device by plugging it into the centre — and reliable. *Cons:* more cabling and dedicated hardware, so more expensive and more to maintain; and the centre is a single point of failure. If the central switch fails, **every** device loses the ability to communicate through the network (a single failed cable, by contrast, only takes out the one device on it).

**Bus.** Every device branches off one shared backbone cable, like bones off a fish spine. *Pros:* cheap and simple to set up. *Cons:* all traffic shares one cable, so it bottlenecks quickly under load; hard to troubleshoot which device is causing a problem; and the backbone is a single point of failure — one break and the whole network is down.

**Ring** (also called token topology). Devices connect directly to each other in a loop; data travels one direction around it, each device forwarding until it reaches the destination. *Pros:* little cabling, no dependence on a central device. *Cons:* inefficient (data may pass through many devices to arrive), and any single break in the loop stops the whole network.

**My honest complaint:** on a first read these all blur together. Every topology has a single point of failure. Every one bottlenecks under enough traffic. Adding devices always costs more cable and maintenance. So what actually separates them? Working through it, the real differentiator isn't the pros/cons list — it's **where the single point of failure sits and how isolated a failure stays**:

- In a **star**, a device failure is isolated to that device; only the *central* failure is catastrophic — and you can spend money to make that one central device robust. That containment is why star dominates in practice.
- In a **bus** and a **ring**, there's a shared element (the backbone / the loop) where a single failure takes down everyone, and you can't buy your way out of it as cleanly.

So the room's flat pros/cons table undersells the real point: star wins not because it has fewer weaknesses, but because it lets you *concentrate* the weakness into one place you can then defend and pay to harden. That's a more useful way to hold it than a list.

### A speculation about the ring — and its limit

The room notes something specific about the ring: a device forwards other devices' data *only if it has nothing of its own to send* — it prioritises its own traffic. That made me wonder: could a compromised device abuse that priority? If an attacker took over one device and had it continuously send its own data, could it starve the devices "behind" it in the loop, since they'd keep deferring — a denial-of-service by hogging the loop?

The reasoning is sound *given the room's description*, and the general shape — one misbehaving node degrading a shared medium — is a real class of attack. The wrinkle I later found: actual token-ring networks use a **token** (a permission-to-send marker passed around the loop, with time limits on how long any device may hold it), designed precisely to stop one device monopolising the ring. So the pure "spam forever and starve everyone" version is mitigated in the real protocol. I'm flagging it as a revisit rather than claiming I've found a hole — but the instinct to look at the priority rule as an abuse vector was the right kind of question to ask.

## Switch vs router — where I genuinely got stuck

The room defines a switch and a router, and I came away unable to tell them apart — both sounded like "a box that moves data between devices." This is the thing I had to go and sort out, because the definitions blur it. The clean distinction:

- A **switch** connects devices **within one network** (a LAN). It aggregates computers, printers and so on, and it's smart about delivery: it keeps track of which device is on which port, so when a packet arrives it sends it only to the intended port. That's the difference from a **hub**, its dumber ancestor, which blindly repeats every packet to every port — noisier and less private.
- A **router** connects **different networks to each other** and passes data between them. That's what "routing" means: finding a path across networks so data reaches a device on a *different* network.

So the one-line version I wish the room had led with: **a switch moves data *inside* a network; a router moves data *between* networks.** Once I had that, the rest fell into place. (The room also mentions connecting switches and routers together for **redundancy** — multiple paths, so if one goes down another carries the traffic. That's an availability gain, i.e. the "A" in the CIA triad.)

One open question the room left me: when a router has several possible paths, how does it choose? By speed? By least traffic? The room doesn't say. I gather routers use routing protocols with different "cost" metrics (some count hops, some weigh bandwidth or delay), but the detail is beyond this room — noted for later.

## Subnetting — and the reserved addresses the room glosses over

Subnetting is splitting one network into smaller ones, usually along functional lines — a subnet for HR, another for finance. The room lists the benefits as efficiency, security, and control.

A subnet uses IP addresses in three roles:

- **Network address** — identifies the network itself. In the room's example, `192.168.1.0`.
- **Host address** — identifies an actual device. The usable range in this example is `192.168.1.1` to `192.168.1.254`.
- **Default gateway** — the one device on the network that can send data *out* to other networks. Usually the first or last usable address (`.1` or `.254`).

Two things in my notes needed correcting, and answering them turned out to explain the whole structure:

**I'd assumed host addresses ran to `.255`. They don't — they stop at `.254`.** And I'd wondered why `192.168.1.255` is never mentioned. Here's the answer, and it's the missing piece: in a network of this size, **two addresses are reserved and can't be given to a device**:

- `.0` is the **network address** (names the network itself).
- `.255` is the **broadcast address** (send this to *every* device on the subnet at once).

That leaves `.1` through `.254` — exactly **254 usable host addresses**. So the "254 devices" figure isn't arbitrary; it's 256 total addresses minus the two reserved ends. That single fact ties together the network address, the host range, and the missing `.255` all at once.

**And I'd assumed `192.168.1.0` is always the network address.** It isn't — that's just this example. The network address is the first address of *whatever* range a given subnet occupies.

**Why `.1` or `.254` for the gateway?** Pure convention: pick a predictable end of the range so every device and admin knows where the exit is, without having to look it up. Nothing technical forces it.

**How this actually shows subnetting** (my second question): yes — my guess was right. In the simple case here, changing the **third octet** gives you a different subnet. `192.168.1.0` can be HR and `192.168.2.0` can be finance — two separate subnets, each with its own `.1–.254` host range. (The precise split is set by something called the subnet mask, covered just below.)

### Putting one subnet together

Laying out a single subnet end to end, using the room's range as "Subnet A":

- `192.168.1.0` — the **network address** (names Subnet A; not assignable to a device).
- `192.168.1.1`–`192.168.1.254` — the **254 host addresses**, one per device.
- `192.168.1.1` *or* `192.168.1.254` — one of these is reserved by convention as the **default gateway**, the device that sends data out to other networks. The gateway is just an ordinary host address given a special job — a *specialisation* of a host address, not a separate kind.
- `192.168.1.255` — the **broadcast address**, used to send one message to *every* device on the subnet at once.

A second subnet ("Subnet B") mirrors this exactly, one octet over: `192.168.2.0` is its network address, and `192.168.2.1`–`.254` / `.255` are its hosts, gateway and broadcast. Same structure, different network.

### Where the subnet mask fits in

The one term the room leaves implicit is the **subnet mask**, and it's the piece that explains *why* all the numbers above fall where they do.

Every IP address is secretly in two parts: a **network portion** (which network you're on) and a **host portion** (which device you are). The subnet mask is what decides where that split falls. For `192.168.1.100` with the common mask `255.255.255.0`:

- the `255`s mark the **network** part → `192.168.1` is the network;
- the `0` marks the **host** part → the last octet is the device.

That's why only the last octet varies in the examples above — the first three octets are "locked" as the network — which is exactly what produces the `.0`–`.255` range, and after reserving `.0` and `.255`, the 254 usable hosts. It's also why changing the **third** octet makes a whole different subnet: `192.168.1.x` and `192.168.2.x` differ in the *locked network part*, so they're separate networks.

The `/24` shorthand (as in `192.168.1.0/24`) is the same idea counted in bits: 24 of the address's 32 bits are the network part (three octets × 8 bits), leaving 8 bits — 2⁸ = 256 addresses — for hosts. Change the mask and you move the split; a `/25` would halve the range. That deeper mechanic is for a later room, but the one-line version is enough here: **the subnet mask is the ruler that marks where "network" ends and "host" begins in an IP address.**

## ARP — matching an IP to a hardware address

ARP (Address Resolution Protocol) is how a device finds the **MAC address** (permanent hardware ID) that belongs to a known **IP address** on the local network. The steps, as I understand them:

1. Device X wants to reach IP address Z but doesn't know its hardware address. X **broadcasts an ARP Request** to the whole network: "who owns IP Z?"
2. The device that owns Z — call it Y — **sends an ARP Reply**: "that's me, here's my MAC address." Every other device stays quiet.
3. X records the mapping (IP Z → Y's MAC) in its **ARP cache**, a local phonebook, for future use.

**What surprised me — and it's the security point:** ARP simply *trusts the reply*. There's no check that the device answering actually owns that IP. Whoever replies first, claiming to own Z, gets believed and cached. This is the same weakness I met with MAC spoofing a few rooms ago: an identifier that anyone can claim, with nothing verifying the claim. Here it enables **ARP spoofing** — an attacker answers "I own that IP" for, say, the default gateway's address, and now other devices send the gateway's traffic to the attacker instead, who quietly sits in the middle. I'm not writing the attack up here, but the missing-verification property is worth naming, because I've now seen it twice.

## DHCP — and the attack I initially missed

DHCP (Dynamic Host Configuration Protocol) hands out IP addresses automatically. The four-step handshake:

1. A new device Z with no IP address joins the network and **broadcasts a DHCP Discover**: "are there any DHCP servers here?"
2. A DHCP server X replies with a **DHCP Offer**: "you can use this IP address."
3. Z replies with a **DHCP Request**: "yes, I'll take it."
4. X sends a **DHCP ACK**: "confirmed, it's yours — start using it."

**Here's where my first analysis was wrong, and it's worth showing the mistake.** In my notes I reasoned that the only possible bad actor is the *new device Z*, and since Z can only accept or reject what it's offered, there's little room to misbehave. That's true — *about the client*. But it's looking at the wrong actor. The real DHCP attacks come from impersonating DHCP server X:

- **Rogue DHCP server.** Nothing in the handshake verifies that a DHCP server is *legitimate*. An attacker puts their own DHCP server on the network and races to answer Discover messages before the real server does. Because a DHCP Offer sets not just the device's IP but its whole network configuration, a malicious Offer can do two nasty things:
  - **Set the default gateway to the attacker's own machine.** The gateway is the exit every device uses to reach anything outside the subnet. If the attacker's machine *is* the gateway, every packet the victim sends outward passes *through* the attacker first — who can copy it (stealing company data), read anything unencrypted, or alter it before forwarding it on. The victim notices nothing, because the traffic still reaches its destination. This is the classic man-in-the-middle position.
  - **Set the DNS server to one the attacker controls.** Recall that DNS turns a name like `mybank.com` into an IP address. The DHCP Offer also tells the device *which DNS server to use* — so a rogue server can point the victim at the attacker's DNS. Now when the victim types `mybank.com`, the attacker's DNS answers with the *wrong* IP: a fake site the attacker runs. The victim trusts the answer because it trusted the DHCP server that supplied the DNS setting in the first place.
- **DHCP starvation.** Here the attacker works from the *client* side: they repeatedly broadcast **DHCP Discover** messages, each carrying a *different fake MAC address*, so the real server believes many new devices are joining and leases out a real IP to each — until the pool of addresses is exhausted and legitimate devices can get none (a denial of service). This is often the setup move: starve out the real server, then stand up a rogue one to answer in its place. (Note the fake MAC addresses here — the same "a MAC can just be made up" property from the ARP section, reused as ammunition for a different attack.)

The lesson for me: I'd assumed the client was the only thing that could attack, and missed that the *server* has no authentication either. Same flaw as ARP, same flaw as MAC filtering — nothing proves the responder is who it claims to be.

## The pattern: identity without verification is not security

Putting the last few rooms together, one idea has now shown up three times:

- **MAC addresses** (Networking room): a firewall trusts a device by its MAC, but a MAC can be spoofed.
- **ARP** (this room): a device trusts whoever replies claiming to own an IP, with no check.
- **DHCP** (this room): a client trusts whatever server answers, and the network trusts whatever client asks, with no check either way.

Three different protocols, one shared weakness: they establish *identity by assertion* — a device simply *claims* to be something — and nothing *verifies* the claim. Every one of the attacks above (MAC spoofing, ARP poisoning, rogue DHCP) is the same move: assert an identity nobody checks. Seeing it three times makes it feel less like three separate facts and more like one principle: **an identifier that can be claimed without being verified is not a security control.** That's a lens I expect to keep using.

## Connects to my bigger goal

Reading how this hardware actually works makes it much easier to picture advising a real client.

- **Topologies → knowing where the single point of failure is.** For any network a client runs, I can now point at the thing whose failure takes everything down — the central switch in a star, the backbone in a bus, any break in a ring — and talk about hardening or adding redundancy around it.
- **Subnetting → segmentation.** This is the one that connects hardest to the compliance side. Splitting a network into subnets is the mechanism behind **network segmentation** — keeping sensitive systems on a separate subnet from untrusted ones. The room's café example (staff/payment devices on one network, public guest Wi-Fi on another) is exactly this. My earlier guess — that the security benefit comes from putting controls *between* the subnets, like mini-firewalls — is roughly right: the subnet provides the separation, and firewall rules or access controls between subnets decide what may cross. Segmentation is a control that serious security frameworks expect for protecting sensitive systems. (As always, that's the universal principle — the value of separating a card-payment network from public Wi-Fi doesn't depend on being a regulated entity.)
- **ARP and DHCP → the verification gap.** The advisory takeaway isn't "block new devices from announcing themselves" (my first instinct) — it's recognising that these plumbing protocols trust by default, and that the defences (things like switch-level ARP inspection and DHCP snooping) exist specifically to add the verification the protocols lack. Named for later, not yet understood in depth.

## Where I got stuck

The genuine snag was **switch vs router** — the room's definitions didn't separate them for me, and I had to work out the "inside a network vs between networks" distinction myself (above). The other honest friction was the topologies comparison feeling muddled until I reframed it around *where the failure is contained* rather than the flat pros/cons list.

## Revisit

- **Routing metrics** — how a router actually chooses among multiple paths (hops vs bandwidth vs delay), which the room raised but didn't answer.
- **ARP spoofing and its defences** — Dynamic ARP Inspection, static entries — done properly.
- **DHCP snooping** — the switch feature that blocks rogue DHCP servers; the concrete defence for the attack above.
- **Subnet masks in depth** — the bit-level mechanics of moving the network/host split (e.g. what a `/25` or `/26` actually does), beyond the `/24` "third octet" case covered here.
- **The token-ring priority question** — whether the send-priority rule has any real abuse angle once token-passing is accounted for.

## Lessons Learned

- **Topologies differ by where failure is contained, not by having different weaknesses** — star dominates because it isolates per-device failures and concentrates the risk into one hardenable centre.
- **A switch works inside one network; a router works between networks.** That single distinction resolves most of the confusion.
- **Subnet reserved addresses:** `.0` is the network, `.255` is broadcast, so `.1–.254` are the 254 usable hosts — which explains the "254 devices" figure and the missing `.255`.
- **The subnet mask marks where "network" ends and "host" begins** in an IP address — which is why only the last octet varies in a `/24`, and why a different third octet is a different network.
- **Subnetting is the mechanism behind network segmentation** — the security benefit compliance frameworks expect for separating sensitive systems.
- **The recurring pattern:** MAC, ARP and DHCP all establish identity by assertion with no verification — which is why spoofing, ARP poisoning and rogue-DHCP attacks all work. An unverified identifier is not a security control.

## References

- The room's guided tasks and interactive labs on TryHackMe
