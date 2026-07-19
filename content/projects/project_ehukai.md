---
title: "Ehukai - personal surf intelligence assistant"
date: 2025-06-01T00:00:00+00:00
draft: false
---

<img src="../../img/ehukai_logo.png" style="max-width:220px; display:block; margin: 0 auto 2rem;">

<a href="https://ehukai.vercel.app/" target="_blank">🌐 ehukai.vercel.app</a>

I surf most mornings. Every morning before paddling out I'd find myself opening three or four different apps: Surfline for the forecast, a separate one for wind, the tide chart, and then trying to piece it all together into "is it worth going today, and if so, where?" Doing the same mental calculation every single morning is repetitive and wasteful.

Ehukai is my answer to that. It's a personal surf intelligence assistant that replaces the whole tab-switching ritual with a single opinionated recommendation.

The name comes from the years I spent living in Hawaii. In Hawaiian, *ehukai* means sea spray. Ehukai Beach Park sits on the North Shore of Oahu, right in front of Pipeline, and if you've stood on that beach on a big swell day you've felt exactly what the name describes. It's a place I have a lot of memories associated with, and it happens to contain "AI" in it, so it felt like the perfect name for my product.

## What it does

The core question Ehukai answers is: **"Should I surf today, and where?"** It fetches live forecasts for your local spots from the Surfline API, scores conditions against what you personally enjoy, ranks the spots, and gives you an answer in plain language. No raw swell period tables. Just the answer.

Beyond the daily check, you can log your sessions ("I surfed Supertubos this morning, head-high, really fun, 8/10") and Ehukai learns which conditions you actually rate. Over time the scoring shifts from generic Surfline ratings toward what you as an individual tend to enjoy: long-period groundswell, light offshore, mid-tide, rather than what the crowd thinks is good.

It also handles trip planning. Ask "where should I surf next week?" and it scans destinations worldwide. Or run a strike mission over a region to surface anything that's firing, not just the famous spots but all 9,000+ spots Surfline covers globally.

## Two interfaces

Ehukai started as an MCP server: a Python process that connects to Claude via the Model Context Protocol, giving Claude direct access to Surfline data. The server talks to the Surfline API, maintains a local SQLite database of your spot preferences and session history, and surfaces everything through natural conversation in Claude Code or Claude Desktop. No UI, no browser, just a chat window and the surf data.

The second interface is a minimal dark-mode web app, built for dawn patrol conditions and designed around one principle: decision before data. You see the ranked spots and the scores first, raw numbers are secondary. Dark-mode-first isn't a preference, it's a use-case requirement.

## Technical choices

The MCP server is pure Python. `httpx` for the Surfline API, SQLite for local storage, `mcp[cli]` for the protocol transport. FastAPI sits underneath the web app's backend, deployed as Python serverless functions on Vercel. The frontend is Vite + React, also on Vercel.

The two interfaces store data differently. The MCP server keeps everything in a local SQLite file on your machine. The web app uses Neon Postgres, so your spot preferences, session history, and scores are stored in the cloud and available across devices. Auth is handled via Neon Auth with Google sign-in.

The scoring model blends four factors: wind (35%), swell period (25%), wave size (25%), Surfline rating (15%). A personal layer kicks in once you've logged enough sessions. Conditions similar to your highest-rated surfs get a boost. It's a simple similarity score but in practice it cuts through the noise well.

**Stack:** Python · FastAPI · MCP (Anthropic) · Vite · React · Neon Postgres · SQLite · Vercel
