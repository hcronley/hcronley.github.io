---
layout: default
title: Multi-Paradigm Forecasting Bench
description: A Streamlit application that benchmarks statistical, machine learning, and deep learning forecasters on a shared rolling-origin backtest.
---

# Multi-Paradigm Forecasting Bench

<p class="meta">
  <strong>Built for:</strong> DATA 5630 — Deep Forecasting, Utah State University<br>
  <strong>Stack:</strong> <code>StatsForecast</code> · <code>MLForecast</code> ·
  <code>NeuralForecast</code> · <code>XGBoost / LightGBM</code> ·
  <code>PyTorch Lightning</code> · <code>Streamlit</code> · <code>Plotly</code>
</p>

[View source on GitHub](https://github.com/hcronley/nixtla-forecasting)

## The Problem

"Which forecasting model should I use?" usually gets answered badly, because the
comparison is unfair. ARIMA gets evaluated on one train/test split, the neural net gets
tuned on another, and the gradient-boosted model quietly gets features the others never
saw. The three paradigms — classical statistical, machine learning, deep learning — are
rarely put on the same footing, so the choice ends up driven by familiarity rather than
evidence.

## The Approach

One harness, one evaluation protocol, seven models.

**Rolling-origin cross-validation.** Every model is scored across five rolling windows
rather than a single holdout, so the metrics come with a spread instead of a single
number that might just be a lucky split. MAE, RMSE, and MAPE are reported with standard
deviations, which turns out to matter — models that tie on mean error often separate
clearly on variance.

**Three paradigms, same interface.**

| Framework | Models | Suited to |
| :--- | :--- | :--- |
| StatsForecast | AutoARIMA, AutoETS, SeasonalNaive | Clear, stable seasonality |
| MLForecast | XGBoost, LightGBM, RandomForest | Exogenous features, structured signal |
| NeuralForecast | LSTM, MLP, NBEATS | Long horizons, complex patterns |

**Three forecasting strategies**, because horizon handling is itself a modeling choice:
one-step with iterative refitting (most accurate, slowest), multi-step recursive (what
deployment usually looks like), and multi-output direct (fastest).

## Beyond the Models

The parts that took the most work were the ones that aren't modeling:

- **Input validation** — format checks, date parsing, missing-value detection, frequency
  auto-detection, and parameter consistency checks, each with an error message that says
  what to fix. Most of the failure modes in a forecasting tool are data problems wearing
  a modeling costume.
- **Configuration persistence** — a winning setup exports to JSON and reloads, so a
  result can be reproduced rather than re-derived from memory.
- **Modular pipelines** — separate wrappers per Nixtla library behind a common surface,
  with backtesting, reporting, and validation as independent modules.

## Results

Four sample datasets ship with the app — monthly airline passengers, daily energy
consumption, weekly retail sales, and daily temperature — chosen to span different
seasonal structures and series lengths.

<!-- TODO: fill in your actual findings from the DATA 5630 runs. A couple of concrete
     numbers here does more for this page than anything else on it. For example:
     which model family won on which dataset, the MAE spread across the five windows,
     and any case where the expensive model lost to SeasonalNaive. -->


<!-- TODO: drop a screenshot of the model-comparison view into assets/imgs/ and embed it here. -->

## Runtime

<ul class="stats">
  <li><span class="stat-value">30–60s</span><span class="stat-label">Single forecast</span></li>
  <li><span class="stat-value">2–3 min</span><span class="stat-label">Full backtest (7 models × 5 windows)</span></li>
  <li><span class="stat-value">~1 GB</span><span class="stat-label">Peak memory</span></li>
</ul>

[View source on GitHub](https://github.com/hcronley/nixtla-forecasting)
