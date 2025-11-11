# 🚚 Dynamic Anomaly Detection & Recommender for Smart Supply Chain

A hands‑on project notebook that detects **operational anomalies** (demand spikes, delayed shipments, sensor faults, inventory drift) and generates **actionable recommendations** (expedite, reroute, reorder, safety‑stock tweak) to keep the supply chain resilient and efficient.

> File: `Project_Dynamic_Anomaly_Detection_and_Recommender_System_for_Smart_Supply_Chain_Management.ipynb`

---

## 🎯 Objectives

- **Detect anomalies** across orders, inventory, shipments, and IoT sensors in (near) real‑time.
- **Recommend actions** to mitigate risk: reorder, expedite, reroute, supplier switch, staffing alert, etc.
- **Prioritize** incidents by business impact (stockouts avoided, service level protected, cost tradeoffs).

---

## 🧱 Approach (high level)

1. **Data ingestion & unification**
   - Merge `orders`, `inventory`, `shipments`, and optional `sensors` time series.
   - Normalize keys: `sku_id`, `location_id`, `timestamp`.
2. **Feature engineering**
   - Rolling stats (mean, std, EWMA), day‑of‑week/seasonality, lead/lag, service‑level gaps (On‑Hand – Demand).
3. **Anomaly detection (multi‑method)**
   - **Statistical baselines:** Z‑score / IQR on residuals (actual − forecast).
   - **Tree‑based:** Isolation Forest / Local Outlier Factor on window features.
   - **Sequence models (optional):** LSTM/Autoencoder reconstruction error for sensor or demand series.
4. **Forecast‑aware alerts**
   - Fit simple **ARIMA/ETS** (or Prophet) to forecast expected behavior; flag deviations by dynamic thresholds.
5. **Recommendation layer**
   - **Heuristics/Rules:** If `projected_stockout_days < threshold` ⇒ *reorder qty*.
   - **Association/Affinity (optional):** mine co‑movement and substitution to suggest SKU swaps.
   - **Routing/Priority:** suggest expedite/alternate lane when SLA risk > X%.
6. **Scoring & ranking**
   - Business impact score = stockout risk × margin × lead time penalty.
7. **Reporting**
   - Incident table + recommended action with rationale.

---

## 📦 Expected data schema (example)

You can adapt names/paths in the first cell of the notebook.

**orders.csv**
- `timestamp` (ISO), `sku_id`, `location_id`, `qty_ordered`

**inventory.csv**
- `timestamp`, `sku_id`, `location_id`, `on_hand`, `on_order`, `safety_stock`

**shipments.csv**
- `timestamp`, `sku_id`, `origin`, `destination` (= `location_id`), `eta_days`, `status`

**sensors.csv` (optional)**
- `timestamp`, `location_id`, `sensor_id`, `value` (e.g., temp, vibration)

> Timestamps should be at a consistent grain (hour/day). The notebook includes resampling helpers.

---

## 🗂️ Repo structure

```
.
├─ Project_Dynamic_Anomaly_Detection_and_Recommender_System_for_Smart_Supply_Chain_Management.ipynb
├─ data/
│  ├─ orders.csv
│  ├─ inventory.csv
│  ├─ shipments.csv
│  └─ sensors.csv          # optional
└─ artifacts/              # created by the notebook
   ├─ anomalies.parquet
   ├─ recommendations.parquet
   └─ models/              # serialized models (if saved)
```

---

## 🚀 Quickstart

1. **Create environment**
   ```bash
   python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```
2. **Place data** in `./data/` using the schema above.
3. **Run** the notebook end‑to‑end:  
   ```bash
   jupyter lab  # or jupyter notebook
   ```
4. **Review outputs**
   - `artifacts/anomalies.parquet` — each row = detected anomaly with scores & context.
   - `artifacts/recommendations.parquet` — action suggestions with confidence & impact.

---

## 🧪 Evaluation & monitoring

- **Anomaly precision/recall** (vs. labeled incidents if available).
- **Alert burden**: alerts/day, % actionable.
- **Business impact**: projected stockouts avoided, SLA adherence, carrying cost deltas.
- **Drift checks**: population stability index (PSI) for key features.

---

## ⚙️ Configuration (edit in first cell)

- Paths for `data/` and `artifacts/`
- Time grain (`D`, `H`), lookback windows, and thresholds
- Model toggles: IsolationForest / LOF / ARIMA / LSTM‑AE

---

## 🧰 Tech stack

- **Core:** Python 3.8+, pandas, numpy, scikit‑learn
- **Time series:** statsmodels (ARIMA/ETS) or prophet (optional)
- **Anomaly (optional):** pyod
- **Viz:** matplotlib (and optionally seaborn)
- **I/O:** pyarrow / parquet

Minimal `requirements.txt` (adjust to your notebook’s imports):

```
pandas
numpy
scikit-learn
matplotlib
pyarrow
statsmodels
# optional extras used in some variants:
# seaborn
# prophet
# pyod
# tensorflow  # if using LSTM/autoencoder
```

---

## 📊 What the outputs look like

**anomalies.parquet** (columns)
- `timestamp`, `sku_id`, `location_id`, `metric` (e.g., demand, on_hand, temp)
- `score`, `severity`, `method` (zscore|iforest|arima_resid|lstm_ae)
- `expected`, `actual`, `delta`, `context` (JSON)

**recommendations.parquet** (columns)
- `issue_id`/`anomaly_id`
- `action` (reorder|expedite|reroute|investigate_sensor|adjust_safety_stock)
- `quantity`/`target`/`lane` (as applicable)
- `confidence`, `impact_score`, `notes`

---

## 🛣️ Next steps

- Turn the notebook into a **Streamlit** or **FastAPI** app for interactive triage.
- Add a simple **cost‑optimizer** to pick best action under constraints.
- Introduce **multi‑location network effects** (substitution and transshipments).
- Add **Unit tests** and CI for data contracts and thresholds.

---

## 📜 License

MIT — use and modify freely. Replace with your preferred license if needed.

---

## 🙌 Acknowledgements

- Public retail/supply‑chain datasets and the open‑source community.
