---
title: "TryHackMe: Client-Server Basics — Learning Notes"
description: "My notes on the Client-Server Basics room — the client-server model, request and response, ports, DNS, and HTTP GET, mapped through the pizza-takeaway analogy."
date: 2026-07-29 21:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [networking, client-server, http, dns, ports]
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

- **Room:** Client-Server Basics — [link](https://tryhackme.com/room/clientserverbasics)
- **Difficulty:** Info / Easy
- **What it teaches:** The client-server model, and the vocabulary that hangs off it — client, server, request and response, protocol, port, DNS — with HTTP GET as the worked example.

A concepts room, no target machine. Much of it retreads ground the HTTP in Detail and DNS in Detail rooms already cover, so the value for me wasn't any single new fact but seeing the pieces tied together under one model, through a single running analogy: ordering pizza.

## The client-server model

The whole idea reduces to one rule: one machine offers a service, another uses it, and **the client always initiates while the server only ever responds**. Everything else is just naming the parts of that exchange — the *client* that makes the request, the *server* that answers, the *protocol* that fixes the language and format both sides agree on, the *port* that picks which service on the server should answer, and *DNS* that turns a human-readable name into the server's numeric address.

## A page load, step by step

The room builds this through Luigi's pizza takeaway. Reading it the smooth way — the computer event first, then the everyday equivalent:

| Under the hood | Pizza-takeaway equivalent |
|---|---|
| The browser (client) kicks off a request for a web page | Alice checks Luigi's menu and tells Bob which pizza she wants |
| It resolves the site's name to a server IP via DNS, then heads for that address | Bob types "Luigi's Pizza" into the GPS and drives to the coordinates it returns |
| It connects on the right port — `8080` here, the one serving the web page | Luigi's has three doors (takeaway, dine-in, delivery); Bob takes the takeaway door |
| It sends the HTTP request `GET /index.html` | Bob places his order: "a large pepperoni" |
| The server replies with a status line in the response header — `200 OK` | The staff acknowledge the order and get to work |
| The server sends the response body — the page's HTML | Bob carries the finished pizza home |

One place the analogy quietly diverges: Bob makes two trips, but in HTTP the header (`200 OK`) and the body come back as a *single* response — status line first, then the content, in one stream.

The room's second half lets you watch the last three rows happen for real: open Firefox DevTools, reload the page, and the Network tab lists the GET requests, each showing the scheme, host, filename (`/`, which resolves to `index.html`), the address, and the `200 OK` status, with the returned HTML under the Response tab. This part is the practical face of what HTTP in Detail already covered.

## Connects to my bigger goal

The client-server model is the scaffolding under nearly everything else I'll meet in this space. Every request I'll later intercept, replay, or pick apart is, at bottom, a client asking a server for something and a server answering. Getting this pattern automatic now means the hands-on work further along — proxies, hand-crafted requests, poking at web apps — should read as variations on a shape I already know rather than new machinery each time. So it's less a fact to memorise than the frame I'll hang the later rooms on.

## Where I got stuck

Mapping the analogy back to a real URL, I got tangled on why *resolving the name to an IP* and *choosing the port* are two separate steps, when a URL like `http://httpdemo.local:8080` seems to show them as one.

What untangled it: the URL is just notation — it writes the host and the port side by side, but they're handled by different mechanisms. DNS resolves a *name* to an IP, and it only runs when the host is a name (`httpdemo.local` resolves to `127.0.0.1`). Type the IP directly, as in `127.0.0.1:8080`, and there's no DNS step at all — nothing to look up. The port was never a lookup either; it's simply stated and used to pick which service answers, the way Bob picks a door. So it's really one lookup (for names only) plus one connection parameter — not two sequential steps. The URL just displays them together.

## Revisit

- **The other HTTP methods** — the room names nine and works only `GET`; `POST` is the obvious next one.
- **Status codes beyond `200`** — the families worth knowing as a set (3xx redirects, 4xx client errors, 5xx server errors).
- **How statefulness is bolted onto a stateless protocol** — sessions, cookies, and tokens, which is where login and authentication actually live.

## Lessons Learned

- **The client always initiates; the server only responds.** That one rule orders the entire exchange.
- **The jargon is just the parts of one interaction** — client, server, request/response, protocol, port, DNS aren't six topics, they're six roles in a single handshake.
- **A URL bundles separate things** — scheme, host, port, path — that are handled independently underneath, even though they're written as one string.

## References

- [Client-Server Basics — TryHackMe](https://tryhackme.com/room/clientserverbasics)
- Rooms this builds on: [HTTP in Detail](https://tryhackme.com/room/httpindetail), [DNS in Detail](https://tryhackme.com/room/dnsindetail)
