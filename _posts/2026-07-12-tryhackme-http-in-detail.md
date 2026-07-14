---
title: "TryHackMe: HTTP in Detail — Learning Notes"
description: "My notes on the HTTP in Detail room: how requests and responses work, URLs, HTTP methods, status codes, headers, and cookies."
date: 2026-07-12 10:00:00 +0800
categories: [Writeups, TryHackMe]
tags: [http, web, headers, cookies, fundamentals]
toc: true
comments: true
---

{% comment %}
  Self-reminder block unchanged — see _template.md. Own words, no flags/answers,
  no reproduced THM content, learning-notes framing, no commercial pitch.
{% endcomment %}

> These are my personal learning notes as I work through TryHackMe — honest notes, not an authoritative guide. Corrections welcome.
{: .prompt-info }
> **On sources & TryHackMe's material:** These are independent learning notes in my
> own words. They describe *my* experience of the room and deliberately reproduce none
> of TryHackMe's room text, task content, screenshots, flags, or answers — go do the
> room to get those. Room names and the linked URL are used for reference only.
> TryHackMe and its content are the property of TryHackMe Ltd; this post is not
> affiliated with, authorised by, or endorsed by them.
{: .prompt-tip }

## Overview

- **Room:** HTTP in Detail — [link](https://tryhackme.com/room/httpindetail)
- **Difficulty:** Info / Easy
- **What it teaches:** How the web actually works under the hood — HTTP requests and responses, URLs, HTTP methods, status codes, headers, and cookies.

This one connected directly to things I'd already run into setting up my own site (HTTPS certificates, status codes, redirects), which made it click faster. It also drove home how much happens between a browser and a server *before* a human ever sees a webpage.

## HTTP and HTTPS

HTTP (HyperText Transfer Protocol) is the set of rules browsers and web servers use to exchange webpage data — HTML, images, video. HTTPS is the secure version: the data is **encrypted**, which does two things — it stops others reading what's sent between you and the server, *and* it gives assurance you're talking to the genuine server rather than an impersonator.

## The request/response flow — the part I had to work through

My main confusion was *how* the browser actually talks to the server — is the request hidden in the webpage, sent separately, or part of the URL? Working through it, the sequence finally made sense:

1. You give the browser a **URL** — just the *address* of what you want.
2. The browser opens a network connection to that server and sends an **HTTP request** over it. This is a *separate communication* — there's no webpage yet.
3. The server sends back an **HTTP response**, which *contains* the HTML.
4. Only now does the browser have a page to render.

So the request isn't inside the page — the request is what *produces* the page. That reframing cleared up my confusion: URL, request, and page are three things that happen in sequence, not the same thing.

### URLs hold more than I realised

A URL is often just scheme + host + path, but it can carry more: a **username** (for authentication), a **port** (usually 80 for HTTP, 443 for HTTPS), a **query string** (extra info sent to the path, e.g. `/blog?id=1` to request article 1), and a **fragment** (a jump-to location on the page).

### A request, broken down

A minimal request is one line (`GET / HTTP/1.1`), but a real one sends **headers** too:

- **`GET / HTTP/1.1`** — the method (GET), the resource (`/`), and the HTTP version.
- **`Host:`** — which site you want (one server can host many domains).
- **`User-Agent:`** — your browser and version, so the server can format the page appropriately.
- **`Referer:`** — the page you came *from*. (Amusing detail: this is a famous misspelling of "Referrer" that got baked into the standard and never fixed.)
- **`Accept-Encoding:`** — which compression methods your browser can handle.
- A **blank line** signals the request has finished.

### A response, broken down

- **`HTTP/1.1 200 OK`** — the version and a **status code** (200 = success).
- **`Server:`** — the server software and version.
- **`Date:`** — the server's date/time.
- **`Content-Type:`** — what kind of data is coming (HTML, image, PDF…), so the browser knows how to handle it.
- **`Content-Length:`** — how long the response is, so the browser knows nothing's missing.
- **`Content-Encoding:`** — how the data was compressed (the server's answer to the browser's `Accept-Encoding`).
- A **blank line**, then the actual content (the HTML that becomes the page).

## HTTP methods

These reminded me of working with databases in PHP — they map neatly onto create/read/update/delete:

- **GET** — fetch information from the server (read).
- **POST** — submit data, potentially *creating* a new record (e.g. submitting a signup form).
- **PUT** — submit data to *update* existing information.
- **DELETE** — delete a record.

The GET-vs-POST distinction clicked with the room's form example: the server sends a form via GET; when the user submits it, the browser uses POST to send the entered data (e.g. a name) back to be stored.

## Status codes

There are many, but they fall into five ranges, which is the real key to remembering them:

- **1xx** — informational
- **2xx** — success (e.g. **200 OK**, **201 Created**)
- **3xx** — redirection (e.g. **301** permanent, **302** temporary)
- **4xx** — *client* errors (e.g. **400** bad request, **401** not authorised, **403** forbidden, **404** not found, **405** method not allowed)
- **5xx** — *server* errors (e.g. **500** internal error, **503** service unavailable)

The mnemonic that works for me: **the first digit tells you *who's at fault* — 4xx is "you (the client) messed up," 5xx is "the server messed up."** That single distinction covers most of what I need day to day. (I'd already met 404 and the deployment errors first-hand setting up my own site, which helped these stick.)

## Headers and cookies

**Headers** are the extra data attached to requests and responses — not strictly required, but you'd struggle to view a site properly without them. Request headers (Host, User-Agent, Accept-Encoding, Cookie) come from the browser; response headers (Set-Cookie, Cache-Control, Content-Type, Content-Encoding) come from the server.

**Cookies** finally made sense once I saw *why* they exist: HTTP is **stateless** — the server doesn't remember your previous requests. So when a server wants to remember you, it sends a `Set-Cookie` header; your browser stores that small piece of data and sends it back on every future request, reminding the server who you are. They're most commonly used for authentication — and the value usually isn't your password in plain text but a **token** (a hard-to-guess secret code).

## Connects to my bigger goal

This room sits right on the ground where a lot of web attacks and defenses happen, so a few things stood out for the direction I'm heading:

- **HTTPS and certificates.** HTTPS secures the browser-server connection; without it, traffic is exposed. I've noticed that when a site's TLS certificate expires and the owner forgets to renew, the site can start serving over plain HTTP — a real window of exposure. I wondered whether cert expiry dates are publicly visible, and they are: you can inspect any site's certificate in the browser, and public **Certificate Transparency logs** record them. So a lapsing certificate is a *visible* opportunity — the kind of thing defenders (and attackers) can monitor for.
- **Cookies as an attack surface.** Since cookies often hold authentication tokens, they can be stolen or manipulated — a whole area I want to dig into later.
- **The bigger realisation:** the more detail I learn about how computing actually works, the *wider* the attack surface becomes. Every header, every method, every stored cookie is another thing that can be misused. Learning how something works and learning how it can be attacked are turning out to be the same study from two directions.

## Lessons Learned

- **The reread that paid off:** the request is a *separate communication* that produces the page — URL, request, and page are a sequence, not one thing.
- **Clarified:** POST *creates*, PUT *updates* — a distinction I'd been fuzzy on from my database days.
- **Retention trick:** status codes by first digit — 4xx = client's fault, 5xx = server's fault.
- **Security instinct forming:** statelessness → cookies → tokens → a stealable/manipulable attack surface; and expiring certificates as a publicly visible exposure.

## References

- The room's guided tasks on TryHackMe
- `http.cat` — a memorable visual reference for status codes
