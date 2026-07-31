---
title: "TryHackMe: Cloud Computing Fundamentals — Learning Notes"
description: "My notes on the Cloud Computing Fundamentals room — why the cloud exists, public vs private vs hybrid, the IaaS/PaaS/SaaS responsibility ladder, and why security work leans towards IaaS."
date: 2026-07-31 09:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [cloud, iaas, paas, saas, fundamentals]
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

- **Room:** Cloud Computing Fundamentals — [link](https://tryhackme.com/room/cloudcomputingfundamentals)
- **Difficulty:** Info / Easy
- **What it teaches:** What cloud computing is, the three deployment types (public / private / hybrid), the three service models (IaaS / PaaS / SaaS), the benefits, and a hands-on deploy of cloud machines.

The room sits right on top of the virtualisation one — the cloud is virtual machines and containers running on someone else's shared hardware, rented over the internet. The framing is a familiar problem: an app running on one laptop in one country can't really grow. Faraway users get lag, too many users at once overwhelm it, and if the laptop is off, the app is down. The cloud fixes all three.

## Why the cloud, instead of one laptop

The benefits all come back to *not owning the hardware*:

- **You focus on your software, not the servers.** The provider runs the machines; you run your app.
- **It grows with you.** You rent more machines on demand, up to global scale, and give them back when the spike passes.
- **You pay only for what you use** — no big upfront purchase.
- **It stays up and stays close.** High availability keeps it running if part of the system fails, and global regions keep it near users wherever they are.

## Deployment types: public, private, hybrid

The useful lens here is *how much control you need* — which, in practice, is driven by how regulated the industry is:

- **Public cloud** — shared infrastructure run by a provider, used by startups, websites and global apps because it's cheap, easy to scale, and needs no infrastructure management. It suits nearly every use case. (One thing I had to correct: public cloud isn't "the unregulated option." Plenty of regulated organisations run on it, and the big providers hold serious compliance certifications — the real trade-off is control, not whether the law applies.)
- **Private cloud** — infrastructure dedicated to a single organisation, used by banks, healthcare and government for greater control, customisation and compliance with rules on sensitive data. "Dedicated" is the key word: it can live in the organisation's own data centre, or be hosted by a third party but reserved for them alone. Location isn't the point.
- **Hybrid cloud** — a mix of the two, used by the likes of e-commerce platforms that keep sensitive data on the private side while scaling out to the public side during demand spikes (Black Friday being the classic case).

What "control" actually means here is how far down the stack you get to dictate. On public cloud you fully control your own machines, their operating systems and your app — but not the layer beneath: you don't pick the physical hardware, you share it (isolated) with other customers, you can pin data only to a *region* rather than an exact machine, and you trust the provider's audits instead of inspecting the infrastructure yourself. For most apps that's a great trade. For a bank or hospital that has to *prove* exactly where data sits, who can physically reach it, and that no other tenant shares the box, that missing control is the whole reason to pay for a dedicated private setup. In short, "control" here comes down to four things: physical custody of the hardware, whether the box is shared or dedicated (tenancy), where the data physically lives, and whether you can audit it yourself.

The big public providers are AWS (the leader), Microsoft Azure and Google Cloud Platform, with Alibaba, IBM and Oracle also in the mix. Netflix running its whole platform globally on AWS is the headline example of how far this scales.

## Service models: IaaS, PaaS, SaaS

These are a *responsibility ladder* — the higher you climb, the less you manage (and the less you control):

- **IaaS (Infrastructure as a Service)** — you rent virtual servers, storage and networking. The provider handles the hardware **and the virtualisation** — the hypervisors that slice their physical machines into the virtual servers you rent — while **you** manage the operating system inside each one, and your app.
- **PaaS (Platform as a Service)** — the provider handles the hardware, virtualisation **and** the operating system. You just build and run your app.
- **SaaS (Software as a Service)** — the provider handles everything; you just use the finished app in a browser. Gmail and Zoom are the standard examples.

The way that clicked for me was renting somewhere to live, moving up one rung at a time:

- **IaaS is an empty apartment.** You rent the bare space — walls, wiring, plumbing — and fit it out yourself. You bring and maintain the furniture (the operating system) and do all your own living (running your app). If something inside wears out, that's on you.
- **PaaS is a furnished rental.** The same space, but the furniture is already in and looked after — you don't buy it or fix it. You just move in and get on with living (building and running your app).
- **SaaS is a hotel.** Space, furniture and daily housekeeping are all provided and run for you. You fit out nothing and fix nothing — you just check in and use the room (the finished app).

## Deploying cloud machines (the hands-on)

The exercise drops you into an AWS-style console. A few terms carry it: an **EC2 instance** is a virtual computer in the cloud (CPU + RAM, runs apps); the **instance type** (t3.micro, m5.large, and so on) sets how powerful — and expensive — it is; and a **region** is the geographic location the machines live in. This whole exercise is the IaaS model in action: EC2 instances are rented virtual servers, and on a real one you'd manage the operating system and everything above it yourself.

Worth placing yourself against the last room here: launching an EC2 instance doesn't make you a hypervisor operator — you're a *guest* sitting on AWS's **Type-1** hypervisor, which only AWS runs on its bare-metal servers. You'd become a **Type-2** user only if you went inside your instance and installed something like VirtualBox on its OS to run VMs within it. You create a few instances, then check the **billing** view, stop the ones you aren't using, and watch the cost drop straight away. That last part is the whole point of the model in one move: capacity you can turn on, off, and pay for by the hour. (No answers here — go do the room.)

## Connects to my bigger goal

The room ties the hands-on straight to security work: you deploy those instances under the **IaaS** model because cyber security practice often needs full access to the operating system. That lands for where I'm heading:

- **OS control is what security work runs on.** With the whole OS in your hands you can install your own tooling, configure the system deeply, and — the part I care about — build isolated environments to run attacks and defences safely. In the cloud you do that by creating VMs (EC2 instances), and inside one you can spin up further throwaway VMs or containers as labs and malware sandboxes.
- **It explains why security work sits low on the ladder.** The higher you climb — PaaS, then SaaS — the less of the OS you're allowed to touch. A SaaS mail app gives you nothing to configure; an IaaS VM gives you the whole machine. So security tends to want IaaS.
- **The isolation lesson carries over** from the virtualisation room. A container's boundary is the host's kernel — you can't change it, and an escape reaches the host. A full VM gives you a boundary you control: its own OS, a hypervisor wall to the host, and snapshots to roll back. For something you're deliberately trying to infect, that control is the whole point — and it's why security work wants the OS in its own hands.

## Where I got stuck

I first read "public cloud" as "the unregulated option" — wrong. Plenty of regulated organisations run on it, and the big providers hold serious compliance certifications. The real axis is *control*, not whether regulation applies. What pushes a bank or hospital towards private is **data residency** — laws that require data to physically stay within a particular country. Public cloud lets you pick a region, but proving data never leaves an exact location can call for a dedicated setup.

## Revisit

- **The shared responsibility model** — the core cloud-security idea the room only gestures at ("providers protect the infrastructure"). The provider secures the cloud; you secure what you put in it. Worth learning properly, since most cloud breaches live on the customer's side of that line.
- **What compliance on public cloud actually looks like** — the certifications, and the data-residency / sovereignty rules that decide whether a regulated workload can run there.
- **Running isolated labs inside a cloud VM** — how nested virtualisation works in practice, and where its limits are.

## Lessons Learned

- **The cloud is renting computing over the internet instead of owning it** — so you focus on your software, scale on demand, and pay for what you use.
- **Deployment type is a control-vs-cost trade:** public for cheap scale, private for control and compliance, hybrid for both — and regulation is what pushes an industry towards private.
- **The service models are a responsibility ladder:** IaaS (you manage the OS and app), PaaS (you manage the app), SaaS (you just use it). The higher you climb, the less you manage — and the less you control.
- **Security work tends to sit low on that ladder, at IaaS,** precisely because it needs OS-level control.

## References

- [Cloud Computing Fundamentals — TryHackMe](https://tryhackme.com/room/cloudcomputingfundamentals)
- Builds on: [Virtualisation Basics](https://tryhackme.com/room/virtualisationbasics). Next: [Operating Systems Introduction](https://tryhackme.com/room/operatingsystemsintroduction).
