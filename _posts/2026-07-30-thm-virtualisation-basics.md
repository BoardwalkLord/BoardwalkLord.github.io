---
title: "TryHackMe: Virtualisation Basics — Learning Notes"
description: "My notes on the Virtualisation Basics room — the building analogy, VMs vs containers, Type 1 vs Type 2 hypervisors, and why a VM is the safe place to run malware you don't trust."
date: 2026-07-30 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [virtualisation, virtual-machines, containers, hypervisor, malware-analysis]
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

- **Room:** Virtualisation Basics — [link](https://tryhackme.com/room/virtualisationbasics)
- **Difficulty:** Info / Easy
- **What it teaches:** What virtualisation is, the hypervisor / VM / container stack, the difference between Type 1 and Type 2 hypervisors, and how a virtual environment is managed — all through a building analogy, ending in a hands-on management panel.

The problem it solves is waste. The old rule was "one server = one application," which left most machines idling at 5–20% of their capacity while a company paid for the hardware, power, cooling and floor space of each one. Virtualisation lets many virtual machines share a single physical box *safely* — and for me the interesting word there is "safely", because that isolation is exactly why a VM is where you open something you don't trust.

## The building analogy

The room's whole model is a block of flats, and it maps cleanly:

| Building | Virtualisation |
|---|---|
| The building | The physical server |
| The building manager | The hypervisor (divides the building up safely) |
| An apartment | A virtual machine — its own door, walls, kitchen |
| A room inside an apartment | A container |
| A tenant | An application or operating system |

The load-bearing idea: the apartments all share the building's structure — electricity, water, lifts — the way VMs share the physical hardware underneath, yet each VM is walled off from the other VMs. One tenant's mess doesn't reach the flat next door.

## Type 1 vs Type 2 hypervisors

The hypervisor is the software that carves the physical machine into virtual ones (VMs), hands each a slice of CPU, memory and storage, keeps them isolated, and manages their lifecycle. It comes in two flavours:

- **Type 1 (bare-metal)** runs directly on the hardware, with no host OS beneath it. That makes it fast and efficient — it's what runs production servers, databases and data centres. VMware ESXi and Microsoft Hyper-V are common examples.
- **Type 2 (hosted)** runs as an application *inside* an existing operating system. Easier to install, and the right fit for laptops, learning, software testing, running something like Kali, and safely running malicious files to see what they do. VirtualBox and VMware Workstation are Type 2 — this is the kind you'd install on your own machine.

## VMs vs containers

Both isolate, but they draw the boundary in very different places, and the difference is the most important thing in the room:

- A **VM** is a whole virtual computer with *its own operating system*, virtual CPU, RAM, storage and network. It can run any OS, and if one VM crashes the other VMs carry on. Strong separation, but heavier.
- A **container** packages a single application with its dependencies and *borrows the host's OS kernel rather than having its own independent standalone OS*. That makes it tiny and near-instant to start — but because it shares the host's kernel, it must match the host's OS type: you can't run a Windows container on a Linux host. Docker is the usual way to run them.

So a VM has its own OS behind a hypervisor boundary; a container shares the host's kernel. That container's thinner boundary is the reason why testing malware is better with a VM and not a container.

## Managing a virtual environment

The hands-on task drops you into a management panel with three views: a Summary, a Lab Machines list, and a Hosts view. A couple of things worth keeping:

- **Lifecycle actions.** A VM can be started, stopped, paused, cloned or deleted — and one sitting in an error state can simply be restarted back to health.
- **Sizing.** You create a VM by filling in a preset — a name, a number of CPU cores, an amount of memory, a disk size — and the hypervisor carves that slice out of the host.
- **The Hosts view is the one I'd actually live in.** Per physical host it shows how many VMs sit inside, how much of its CPU, memory and storage are used (in percentage terms) — so at a glance you can see which hosts have spare capacity to create another VM, which are nearly used up, and which are disconnected and carrying nothing.

## Connects to my bigger goal

The reason this room matters to me is malware: a VM is the standard place to run a suspicious file and watch what it does without risking your real machine. The room names two safeguards — run a guest OS *different* from the host, or *isolate* the guest so it can't talk to the host — and my question was why the malware can't just cross into the host anyway. Working it through:

- **The real boundary is the hypervisor.** The guest only ever touches *virtual* hardware — virtual CPU, RAM, disk, network — and the hypervisor is like a wall in between host and guest, and mediates every access. The malware can't see the host's memory, files or processes; by default it's boxed inside the virtual machine.
- **The "different OS" trick is a second layer, not the main one.** Even if a malicious file did reach the host, a binary built for one OS won't natively run on another — wrong format, wrong system calls — so a guest/host OS mismatch shrinks the blast radius. Useful, but it isn't what holds the line.
- **"No communication" is the practical safeguard.** The ways malware actually reaches a host aren't magic; they're conveniences you switched on — shared folders, shared clipboard, drag-and-drop, and above all network access. Turn those off and there's simply no channel to cross.
- **Honest caveat: strong by default, not absolute.** "VM escape" bugs — flaws in the hypervisor or its emulated devices — can in principle enable the malware to cross the boundary and reach the host. They're rare and valuable, but they exist, so "can't cross" really means "is designed not to, and crossing needs either an open channel or a hypervisor exploit."

## Where I got stuck

In my own notes I described my malware sandbox as a *container* running a different OS from the host — a Windows guest on a Linux host, that kind of thing. That's a VM's trick, not a container's, and catching the slip was the useful part. A container shares the host's **kernel** — it borrows the running OS's core instead of shipping its own — which is exactly why it *can't* run a different OS type: no Windows container on a Linux host. I'd half-carried the VM's "own OS" property across and pinned it on the wrong thing.

What made it click was seeing that the shared kernel explains two things at once. It's why a container is so light and quick — there's no second OS to boot — *and* why its isolation is thinner than a VM's: the boundary is that shared kernel rather than a hypervisor, so there's less standing between the workload and the host. Same fact, two consequences — and it's the reason a malware sandbox has to be a VM, not a container. For something you're deliberately trying to infect yourself with, you want the thicker wall.

And that shared kernel is exactly how a container escape would work: because the container and the host run on the very same kernel, that kernel *is* the bridge — exploit a flaw in it from inside the container and the malware can cross straight to the host and infect it. A VM shares no kernel with its host, so there's no such bridge to cross; the hypervisor wall stands in the way.

## Revisit

- **The operational reflexes I want to be second nature:** VMs get built per function (one for mail, one for development); you size each with a preset (CPU / RAM / disk); each can be started, stopped, paused, cloned or deleted; and the per-host view (VM count plus CPU / memory / storage load) is how you decide where a new VM will fit.
- **To dig deeper:** how the hypervisor actually *enforces* the boundary (hardware virtualisation — Intel VT-x / AMD-V), the classes of VM-escape exploit, and how container isolation works under the hood (shared kernel, namespaces and cgroups) compared with a VM's.

## Lessons Learned

- **Virtualisation is about using one physical machine efficiently** — instead of a box idling at 5–20% for a single app, many VMs share it.
- **VM vs container is a trade-off:** a VM brings its own OS and strong isolation but is heavier; a container is light and near-instant but shares the host's kernel.
- **Isolation is the security property**, and it's about the boundary, not the OS you picked. A VM keeps its own OS behind a hypervisor, so untrusted code stays boxed in. A container shares the host's kernel — and that shared kernel is the bridge a kernel-level exploit would use to reach the host — so anything you don't trust belongs in a VM.

## References

- [Virtualisation Basics — TryHackMe](https://tryhackme.com/room/virtualisationbasics)
- Next in the path: Cloud Computing Fundamentals (which builds on virtualisation and containerisation).
