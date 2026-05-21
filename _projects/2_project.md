---
layout: page
title: Multi-Agent Restaurant Review Analyzer
description: An AutoGen pipeline of cooperating LLM agents that turns unstructured restaurant reviews into structured, aggregated ratings.
img: assets/img/restaurant_reviewer.jpg
importance: 2
category: work
---

Online restaurant reviews are free-form text — useful to read, but hard to compare at a glance.
This project builds an **automated pipeline of cooperating LLM agents** that reads raw reviews,
extracts structured quality signals, and produces a single aggregated rating per restaurant.

It is built on Microsoft's [AutoGen](https://microsoft.github.io/autogen/) multi-agent framework,
with **GPT-4o-mini** as the underlying model.

## How it works

Rather than one monolithic prompt, the system splits the job across four specialized
`ConversableAgent` instances, each responsible for one stage of the pipeline:

- **Entrypoint Agent** — orchestrates the run, executes functions, and routes data between agents.
- **Data Fetch Agent** — parses the restaurant name out of the user query and calls
  `fetch_restaurant_data()` to retrieve that restaurant's reviews.
- **Review Analysis Agent** — reads each review and assigns a `food_score` and a
  `customer_service_score` (1–5 each) using a fixed adjective-to-score mapping.
- **Scoring Agent** — collects the paired scores and invokes `calculate_overall_score()` to
  produce the final rating.

The 1–5 scale is anchored to descriptor keywords so scoring stays consistent across reviews — for
example *awful/horrible/disgusting* → 1, *average/uninspiring/forgettable* → 3, and
*awesome/incredible/amazing* → 5.

## Scoring model

Individual review scores are aggregated with a formula that rewards restaurants strong in **both**
food and service, rather than letting one dimension mask a weak one:

$$
\text{score} = \left( \frac{1}{N\sqrt{125}} \sum_{i=1}^{N} \sqrt{\text{food}_i^{\,2} \times \text{service}_i} \right) \times 10
$$

The geometric-style combination means a restaurant with great food but poor service cannot coast
on the food score alone, and the result is normalized to a clean 0–10 scale.

## Tech stack

Python · AutoGen (multi-agent orchestration) · OpenAI GPT-4o-mini

## What I took away from it

The project was a hands-on study in **agentic LLM design** — decomposing a task into narrowly
scoped agents with clear hand-offs proved more reliable and debuggable than a single large prompt,
since each agent can be tested and reasoned about in isolation.

## Links

- **Code:** [github.com/ksjacob27/restaurant_reviewer](https://github.com/ksjacob27/restaurant_reviewer)
