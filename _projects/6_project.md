---
layout: page
title: Autonomous Cybersecurity Defender
description: An AI-powered network intrusion detection API that pairs rule-based classification with a pipeline of GPT-4o agents to detect threats and generate human-readable response plans.
img:
importance: 6
category: work
---

The Autonomous Cybersecurity Defender is a **network intrusion detection service** that combines
fast rule-based classification with **LLM reasoning**. Instead of just flagging traffic as
malicious, it explains _why_ — producing a human-readable threat assessment and a concrete
response plan a defender could act on.

## How it works

The system is a FastAPI service exposing a single `POST /predict` endpoint. It takes lightweight
packet metadata — for example:

```json
{ "packet_size": 500, "protocol": 2 }
```

and runs it through a pipeline of cooperating GPT-4o **agents**, each with one job:

- **Traffic classifier agent** — makes the first call: is this traffic malicious or benign?
- **Threat analyzer agent** — for malicious traffic, characterizes the likely attack vector
  (e.g. a TCP/SYN flood) and assesses its severity.
- **Response planner agent** — produces a simulated firewall action plus a multi-step mitigation
  guide covering monitoring, rate limiting, access controls, and incident response.

The response bundles all of this together — the classification `result`, the `threat_info`
explanation, the `response_action`, and an ordered list of `response_steps` — so the output is
not just a label but actionable guidance.

## Design idea

The project's core idea is **layered detection**: cheap, deterministic rules handle the obvious
cases quickly, while LLM agents add the reasoning and clear explanations that rigid rule sets
can't. Splitting the LLM work across narrowly scoped agents (classify → analyze → plan) keeps each
step focused and the overall behavior easy to follow.

## Tech stack

Python · FastAPI · OpenAI GPT-4o · Docker · Thunder Client (VS Code API testing)

The work draws on the [UNB CIC-IDS-2017](https://www.unb.ca/cic/datasets/ids-2017.html) intrusion
detection dataset, and includes visualizations of packet-size and protocol correlations.

## Links

- **Code:** [github.com/ksjacob27/cybersecurity_defender](https://github.com/ksjacob27/cybersecurity_defender)
