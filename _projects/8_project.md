---
layout: page
title: MFF Calculator
description: A small full-stack web calculator that computes in-game gold costs for Marvel Future Fight's BAM and CCF upgrade systems.
img: assets/img/mff_calc.png
importance: 8
category: work
---

Marvel Future Fight's BAM and CCF upgrade systems price each rank on a compounding gold curve
that's tedious to work out by hand. This is a small web tool that does the math for you: pick a
resource type, enter a rank and amount, and get back the total units needed and the gold cost.

## How it works

- **Frontend** — A single-page form (`index.html`) lets you choose **BAM** or **CCF**, enter a
  target rank and amount, and submit.
- **Backend** — A Node.js/Express server exposes a `POST /calculate` endpoint. It validates the
  inputs, scales the requested amount by `2^(rank-2)` to account for how upgrade materials
  compound per rank, converts that into total resource units (×50 for BAM, ×35 for CCF), and runs
  the result through a gold-cost function before returning JSON.
- **Gold cost curves** — Each resource type has its own base cost and per-rank increment (BAM
  starts at 187,500 gold and climbs by 62,500 per rank; CCF starts at 375,000 and climbs by
  125,000), reflecting the escalating cost structure of the actual game.
- **Origins in C++** — The core calculation logic was originally prototyped as a
  command-line C++ program (`mffCalc.cpp`) before being ported to the Express API.

## Tech stack

Node.js · Express · JavaScript · HTML/CSS · C++ (original prototype)

## Links

- **Code:** [github.com/ksjacob27/mff_calc](https://github.com/ksjacob27/mff_calc)
