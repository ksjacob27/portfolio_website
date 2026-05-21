---
layout: page
title: F1 Pit Stop Strategy Optimizer
description: A deep learning system that recommends Formula 1 pit stop windows from publicly available lap data — competitive with tools that normally require proprietary team telemetry.
img: assets/img/f1_poster.jpg
importance: 1
category: work
---

Formula 1 pit stop strategy is one of the highest-leverage real-time decisions in motorsport: a
well-timed stop can gain 5–10 seconds through an undercut, while a poorly timed one can cost a
podium. Professional teams answer this with proprietary simulation software fed by live telemetry —
a capability entirely unavailable to independent analysts, broadcasters, and fans.

This project — my M.S. in Computer Science capstone at the University of Colorado Denver — asks a
direct question: **can a deep learning model trained only on _publicly available_ lap-time data
recommend pit windows with competitive accuracy?** The answer turned out to be yes.

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/f1_strategy_ver.png" title="Predicted pit window vs. actual pit stop" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/f1_strategy_nor.png" title="A representative miss" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left — Verstappen, 2024 Bahrain GP: the predicted pit window (green) correctly brackets the
    actual stop on lap 38. Right — Norris, 2024 Australian GP: a tactical undercut on lap 13 that no
    degradation-based model can anticipate, the system's primary failure mode.
</div>

## How it works

The system is a three-stage pipeline:

- **Data pipeline** — Lap times, tire compounds, fuel load, and weather are pulled from the
  [FastF1](https://docs.fastf1.dev/) API for the 2022–2024 seasons (the only fully open source of F1
  lap timing). Models train on 2022–2023 and are evaluated entirely on the held-out 2024 season,
  using a temporal split between seasons to prevent leakage of season-specific car performance.
- **Compound-specific GRU models** — Three independent Gated Recurrent Unit networks, one each for
  the SOFT, MEDIUM, and HARD tire compounds. Each predicts the *lap-to-lap change* in lap time
  (Δt) rather than absolute lap time — a reformulation that isolates the tire degradation signal
  from baseline circuit pace and roughly doubled strategy accuracy on its own.
- **Strategy simulator** — At inference, the trained model auto-regressively projects 20 future laps
  under two scenarios — staying on worn tires versus pitting for fresh ones — and identifies the
  first lap where pitting becomes net positive, accounting for circuit-specific pit-lane time loss.

## Results

Evaluated against **122 race-driver combinations** from the 2024 season, the final system reaches
**52.5% strategy accuracy** — predicting the first pit lap within ±5 laps of the actual stop, a
+21.5 percentage-point gain over a brute-force baseline. The initial success criterion was a 10%
MAE improvement over a moving-average baseline; every GRU configuration exceeded that target by
roughly 4×.

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/f1_mae_comparison.png" title="Model comparison" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/f1_predictions.png" title="GRU predictions vs. actual" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left — the GRU cuts prediction error 39.9% below baseline and clearly beats every LSTM variant.
    Right — predicted vs. actual lap times on held-out 2024 races, tracking within-stint degradation
    slopes including the step changes at pit stops.
</div>

A few findings stood out:

- **Architecture matters.** The GRU beat every LSTM variant tested — including an LSTM+attention
  model — because with short 10-lap windows the GRU's lower parameter count reduces overfitting on a
  ~28k-sequence dataset.
- **Compound-specific modeling was the key breakthrough.** Training a separate model per tire
  compound delivered the single largest jump in accuracy (+26.2 points), since degradation rates
  differ 2–3× across compounds.
- **Lower error ≠ better strategy.** A systematic ablation showed that test MAE on lap-time
  prediction is a poor proxy for strategy accuracy — the configuration with the best MAE produced
  0% useful strategy calls.
- **The remaining bottleneck is driver identity.** Per-driver accuracy ranged from 40% to 70%; the
  model has no driver or team features, so it cannot anticipate tactical, non-degradation-driven
  stops.

## Tech stack

Python · PyTorch · FastF1 API · pandas · NumPy · Matplotlib

The full experimental work spans nine model configurations, two data-pipeline variants, and five
strategy-level phases — including two reverted experiments documented with root-cause analysis.

## Links

- **Code:** [github.com/ksjacob27/formula1-optimization](https://github.com/ksjacob27/formula1-optimization)
