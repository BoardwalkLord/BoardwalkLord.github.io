---
title: "TryHackMe: DNS in Detail — Learning Notes"
description: "My notes on the DNS in Detail room: how domain names map to IP addresses, the anatomy of a domain, and the common DNS record types."
date: 2026-07-05 13:38:00 +0800
categories: [Writeups, TryHackMe]
tags: [dns, networking, web-fundamentals]
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

- **Room:** DNS in Detail — [link](https://tryhackme.com/room/dnsindetail)
- **Difficulty:** Info / Easy
- **What it teaches:** What DNS is, how a domain name is structured, and the main types of DNS records that make websites and email work.

## What DNS actually does

The simplest way I can put it: DNS is a translator. Computers find each other using numerical addresses (IP addresses), but humans are bad at remembering numbers. DNS lets us type a human-friendly name — an "English" address — and converts it into the numerical one the machines use. Every time you visit a site by name, a DNS lookup happened in the background to turn that name into an IP.

## Anatomy of a domain name

A domain name isn't one flat thing — it's built in layers, read right to left:

- **Subdomain** — the leftmost part (e.g. the `blog` in `blog.example.com`). A site can have many.
- **Second-level domain** — the main registered name (the `example` in `example.com`).
- **Top-level domain (TLD)** — the ending (`.com`, `.my`, etc).

TLDs themselves split into two families:

- **gTLD (generic)** — categorised by purpose. For example `.edu` for educational sites and `.gov` for government.
- **ccTLD (country-code)** — tied to a country. For example `.my` for Malaysia.

## DNS record types

A domain's DNS records are the instructions that say "this name points here." The ones the room covered:

| Record | What it maps to | Plain-language example |
|--------|-----------------|------------------------|
| **A** | An IPv4 address (`xxx.xxx.xxx.xxx`) | "This domain lives at this IPv4 address." |
| **AAAA** | An IPv6 address (`xx:xx:xx::xx:xx`) | Same idea as A, but for the newer, longer IPv6 addresses. |
| **CNAME** | Another domain name | Points one name at another. E.g. `shop.mywebsite.com` is a CNAME to `mycustomshop.shopify.com` — so the shop subdomain "jumps to" Shopify. |
| **MX** | A mail server | Says where email for the domain should go. E.g. `mywebsite.com`'s mail is handled by an external mail server. |
| **TXT** | Free-form text | Holds arbitrary text records. *I don't fully understand yet why these matter — flagged for a revisit below.* |

The A/AAAA/CNAME/MX group clicked once I saw them side by side: A and AAAA are "where the site is," CNAME is "actually, go look at this other name instead," and MX is "where the email goes." TXT is the odd one out that I haven't got a handle on.

## Lessons Learned

- **New to me:** The layered structure of a domain — subdomain, second-level, TLD — and that TLDs are categorised either by purpose (gTLD) or by country (ccTLD).
- **What surprised me:** `.com` was originally meant for commercial sites specifically. That meaning has completely eroded — now everyone wants a `.com`, even for a personal blog. The label outlived its original intent.
- **What I found tough:** Keeping the record types straight. Writing out what each one maps to (above) is what made them stick — A/AAAA to IP addresses, CNAME to another name, MX to a mail server.
- **Revisit:** TXT records. I don't yet see why a free-text DNS record is useful — that's the gap I want to close next.

## References

- The room's guided tasks on TryHackMe
