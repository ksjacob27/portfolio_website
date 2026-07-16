---
layout: page
title: Movie Recommendation Agent
description: An Agno-powered multi-agent system that researches current movies with live web search and recommends personalized picks using GPT-4o, deployed as a full-stack app on AWS.
img: assets/img/movie.png
importance: 7
category: work
---

Most recommendation engines lean on static ratings databases that go stale and can't explain
themselves. This project takes a different approach: a **team of cooperating LLM agents** that
searches the web for what's actually popular right now, then reasons about what a specific user
would enjoy — and says why.

Live at **[movierecommender.me](https://movierecommender.me)**.

## How it works

The backend is built on the [Agno](https://github.com/agno-agi/agno) agent framework and organizes
the work across two specialized agents under a coordinator team:

- **Researcher agent** — uses [Exa](https://exa.ai/) web search to find the most popular current
  movies in the genre the user asks about, rather than relying on a fixed, aging dataset.
- **Recommender agent** — reasons over the researcher's findings and the user's stated
  preferences to select the best matches.
- **Coordinator team** — orchestrates the hand-off between the two agents and returns **3 movie
  recommendations with a brief explanation for each**. It persists conversation state in SQLite
  with agentic memory, so it can draw on the last few runs of context within a session.

Both agents run on **GPT-4o**.

## Architecture & deployment

The app ships as a single-origin, containerized service on AWS EC2:

- **Frontend** — a React SPA, built and served as static files.
- **Backend** — the Agno agent server, kept private on the internal Docker network and never
  exposed directly to the host.
- **Caddy** — builds the frontend, serves it, reverse-proxies `/api/*` to the backend, and
  handles automatic HTTPS — so the whole app lives behind one domain with no CORS surface and one
  TLS certificate.
- **Hardening** — per-IP rate limiting on the agent API routes, a request-body size cap, security
  headers on every response, and secrets kept out of the image entirely via a gitignored `.env`.

## Tech stack

Python · Agno · OpenAI GPT-4o · Exa · FastAPI · React · Docker / Docker Compose · Caddy · AWS EC2

## Links

- **Code:** [github.com/ksjacob27/movie-recommendation](https://github.com/ksjacob27/movie-recommendation)
- **Live site:** [movierecommender.me](https://movierecommender.me)
