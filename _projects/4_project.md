---
layout: page
title: AudioDB Music Explorer
description: A containerized full-stack web app for searching artists and leaving reviews, backed by a PostgreSQL database and the TheAudioDB API.
img: assets/img/audiodb.jpg
importance: 4
category: work
---

AudioDB Music Explorer is a **full-stack, containerized web application** for discovering music
artists. Search for any artist, browse their profile pulled live from a public music API, and
leave reviews that persist in a database — the whole thing packaged to spin up with a single
Docker command.

## What it does

- **Artist search** — Enter an artist name and the app queries [TheAudioDB](https://www.theaudiodb.com/)
  API, then renders a profile with the artist's biography, genre, year of formation, official
  website, and banner image.
- **Artist reviews** — Visitors can write reviews tied to an artist; reviews are stored in a
  PostgreSQL `artistReviews` table with the artist name, review text, and date.

## Architecture

The app is a classic server-rendered web stack, fully containerized:

- **Backend** — A Node.js / Express server (`server.js`) exposes the routes: a home page, an
  artist-search endpoint that calls the external API via `axios`, and review create/read endpoints.
- **Frontend** — EJS templates render pages server-side, populated with artist data and reviews.
- **Database** — PostgreSQL, accessed through `pg-promise`, with an initialization script that
  sets up the schema on first run.
- **Deployment** — A `Dockerfile` and `docker-compose.yml` orchestrate the app and database
  together, so the full environment comes up reproducibly with `docker compose up`. A Heroku
  deployment configuration is also included.

## Tech stack

Node.js · Express · EJS · PostgreSQL · pg-promise · axios · Docker · Docker Compose · TheAudioDB API

## What I took away from it

This project tied together the pieces of a real deployable web service — an external API
integration, a relational database with its own schema and init scripts, server-side rendering,
and reproducible multi-container packaging with Docker Compose.

## Links

- **Code:** [github.com/ksjacob27/audiodb](https://github.com/ksjacob27/audiodb)
