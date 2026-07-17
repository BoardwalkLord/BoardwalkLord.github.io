---
title: "TryHackMe: Operating Systems — Introduction — Learning Notes"
description: "My notes on the Operating Systems Introduction room — the OS as an air-traffic controller, the kernel/user-space privilege boundary, and why the OS is a security layer before any security tool is installed."
date: 2026-07-17 09:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [fundamentals, operating-systems, kernel, privileges, blue-team]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no reproduced THM content, no lab steps/values, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }

> **On sources & TryHackMe's material:** These are independent learning notes in my own words. They describe *my* experience of the room and deliberately reproduce none of TryHackMe's room text, task content, screenshots, flags, or answers — go do the room to get those. Room names and the linked URL are used for reference only. TryHackMe and its content are the property of TryHackMe Ltd; this post is not affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** Operating Systems: Introduction — [link](https://tryhackme.com/room/operatingsystemsintroduction)
- **Difficulty:** Info / Easy
- **What it teaches:** What an operating system actually does beneath the surface — resource coordination, the privilege boundary between the kernel and ordinary applications, the OS's core duties, and the main OS families you'll meet in the real world.

This is where the "computer fundamentals" thread stops being about parts you can hold and starts being about the invisible layer that governs them. It's also the first room in this series to introduce a real *privilege boundary* inside software — which, from a security point of view, is the most important idea I've met so far.

## The analogy that carried the whole room: an airport

The room frames the computer as a busy airport, and it works well enough that I'll keep using it:

- The **hardware** (CPU, RAM, storage) is the physical infrastructure — runways, planes, fuel, radar.
- The **users and their applications** are the airlines and passengers, all wanting something at once.
- The **operating system** is air-traffic control.

The point the analogy drove home for me is about **conflict**. Every airline wants a runway *now*, but runways are finite, so somebody has to schedule turns. Picture every plane trying to take off at the same instant — that's not a busy airport, that's a wreck. The control tower exists so that limited physical resources get shared without chaos. That's exactly the OS's job: many demands, finite hardware, one coordinator deciding who gets what and when.

Without an OS, every application would have to reach for the CPU, memory and devices directly, and they'd collide constantly. The OS is what makes "everything just works" possible, and the analogy made me appreciate that "just works" is actually a lot of arbitration happening out of sight.

## New to me: privilege layers

This is the concept I'd never had a name for, and it's the one I think matters most.

Inside the OS there's a deliberate **separation of privilege**:

- **Kernel space** — the privileged core. The kernel can talk to the hardware directly: unrestricted access to CPU, memory, storage, devices.
- **User space** — where every normal application runs, deliberately *walled off* from the hardware. When an app wants to open a file, play a sound, or reach the network, it can't just do it. It has to make a **system call** — a formal request to the kernel — and the kernel decides whether and how to carry it out.

Back to the airport: the kernel is the control tower, a locked room only trusted controllers enter. The airlines on the ground can't walk into the tower or touch the radar; they *radio* their requests up, and the tower acts on them. One airline having a bad day can't bring down the airport, because it was never allowed near the controls in the first place.

That last part is the security insight. The boundary isn't just tidy architecture — it's containment. A single faulty (or malicious) app in user space can't directly wreck the system, because it never had direct access to begin with. **Isolation by design, before any security software is even installed.**

## The OS's core duties — remembered as installing and playing a game

The room lists the OS's main responsibilities, and there are enough of them that I needed a way to make them stick. What worked was imagining a single familiar sequence — installing and then playing a game — and watching each duty show up in order:

1. **User management** — I log in and authenticate, and I need admin rights to install anything. The OS decides who I am and what I'm allowed to do.
2. **File system management** — the installer lays the game's files into directories, with names, paths and permissions.
3. **Process management** — I run the installer; the OS creates a process for it (and later terminates it). When I launch the game, that's another process the OS schedules CPU time for.
4. **Memory management** — the game is graphics-heavy, so the OS allocates it RAM, keeps that RAM walled off from other apps, leans on virtual memory (disk) if RAM runs short, and reclaims it all when I quit.
5. **Device management** — the OS loads the graphics and sound drivers so the game looks and sounds the way it's meant to, without the game itself needing to know the hardware details.

And the thing I want to remember above all of it: **every single one of those steps is the same shape.** The game, on my behalf, *requests* something of the OS; the OS checks it; and if it checks out, the OS does it. Install, save, run, allocate, print — all of it is request-and-approve. Once I saw that pattern, the list stopped being five separate facts and became one idea with five faces.

## The OS is a security layer in its own right

The room makes a point I want to hold onto: **before any antivirus, firewall or security tool exists, the OS is already enforcing security.** Several of the duties above *are* security controls when you look again:

- **Authentication** — proving who you are (the login step).
- **Permissions** — the OS fencing off what each user and app may read, write or run (the approve/deny on every request).
- **Isolation** — keeping each process in its own box. *(One correction to my own first guess here: there are actually two different walls, and it's worth not confusing them. The kernel/user-space split is the **privilege** boundary — who may touch hardware. Process isolation is **memory management** keeping one app's RAM out of another app's reach. Same instinct — walls — but two separate walls doing two separate jobs.)*
- **System protection** — guarding critical system files and settings from unauthorised change.

So authentication, permissions and isolation aren't things you bolt *onto* an OS. They're things the OS already is.

## Connects to my bigger goal

Two connections, one that spans my earlier posts and one that reaches toward the security work I'm building.

**First — this room is the middle piece of a stack I've been assembling.** Two rooms ago ("Inside a Computer System") I ended on a question: firmware runs *below* the OS, so a bootkit can sit underneath the OS entirely. This room hands me the other half. Now I can state the whole stack in order:

- **Firmware** sits *below* the OS (runs before it, hands control to it).
- The **kernel** sits at the privileged core of the OS.
- **User space** sits above the kernel — and that's where the applications live, *including the antivirus and security tools.*

Line those up and the danger from the first post finally has a shape. Your security software runs in user space. It relies on the kernel beneath it, which relies on the firmware beneath *that*. Anything that establishes itself below the layer your defences run in is looking down on them. That's why "below the OS" was worth flagging, and it took three rooms to be able to say it properly.

**Second — the OS duties are a map of where attacks happen.** Categorising what the OS does also categorises the ways it can be abused:

- Going through the Security+ material a while back, I remember malware that exploits **memory** — which now clearly maps to memory management. The OS deciding how RAM is allocated and isolated is exactly the machinery such attacks target.
- From my own experience, running as a **non-admin user** limits the damage a lot of simpler malware can do — which maps to user management and permissions. I'd put this carefully, though, having thought about it more: a separate, non-admin account raises the wall, it doesn't guarantee containment. Plenty of malware runs perfectly happily at user-level privilege, and privilege-escalation exploits exist precisely to climb that wall. So it's a real and worthwhile mitigation, not a force field. (Overstating what a control guarantees is its own kind of mistake.)

And the same three duties — **user management, permissions, isolation** — are the raw material underneath the access-control ideas I keep meeting in the compliance world: least privilege, authentication, segregation of duties. Those frameworks aren't inventing new mechanisms; they're insisting you *use properly* the ones the OS already provides. Seeing the mechanism first makes the rule read less like bureaucracy and more like a reminder.

## Where I got stuck / have to revisit

Not stuck, exactly — but this is the first room I know I'll need to reread, purely because of the *volume* of responsibilities the OS carries. The game-installation walkthrough is my scaffold for now, but the formal terms sitting inside that story — authentication, permissions, isolation, system protection — are ones I want to come back to and pin down individually rather than by anecdote.

The room also has a hands-on lab (investigating a "mystery" second-hand computer). I'm deliberately not writing up what it contains — go poke at it yourself — but the exercise of using the OS to interrogate its *own* hardware and file system was a nice practical echo of everything above.

## Revisit

- **System calls** — the request-and-approve pattern is clearly the heart of it; I want to see what a system call actually looks like up close.
- **Kernel-level vs user-level attacks** — now that I can see the privilege line, the natural next question is what changes for an attacker who's on the wrong side of it (and how they try to cross).
- **The formal security terms**, pinned down one by one rather than as a story about installing a game.

## Lessons Learned

- **The privilege boundary is the big idea:** kernel space can touch hardware, user space must ask. That separation is containment by design.
- **Every OS duty is the same shape** — an application requesting something and the OS approving it — which is also a map of where things can be attacked.
- **The OS is a security layer before any security tool exists:** authentication, permissions, isolation and system protection are built in, not bolted on.
- **The stack finally lines up:** firmware below the OS, kernel at the core, security tools up in user space — which is exactly why something running below the kernel is so dangerous.

## References

- The room's guided tasks and static site on TryHackMe
