# 🦠 COVID-19 Global Analytics Dashboard

> Analyzing **195 countries** of WHO global COVID-19 data (Apr–Jul 2020) using Python, Power BI, Tableau, and Looker Studio to uncover case growth trends, regional distributions, recovery rates, and CFR outliers.

---

## 📊 Live Dashboards

- 🔵 **Power BI:** [covid_dashboard.pbix](https://app.powerbi.com)
- 🟠 **Tableau Public:** *(link coming soon)*
- 🟢 **Looker Studio:** *(link coming soon)*

---

## 🗂️ Project Structure

| File | Description |
|------|-------------|
| `COVID19_Analytics_Dashboard.ipynb` | Full Python analysis notebook |
| `01_kpi_cards.png` | Global KPI summary |
| `02_top10_countries.png` | Top 10 countries bar chart |
| `03_regional_distribution.png` | Regional grouped bar chart |
| `04_recovery_rate.png` | Recovery rate by country |
| `05_global_trend.png` | 90-day global growth trends |
| `05_timeseries.png` | Time series Apr–Jun 2020 |
| `06_regional_pie.png` | WHO region pie chart |
| `07_wow_growth.png` | Week-over-week growth leaders |
| `08_active_vs_recovered.png` | Active vs Recovered stacked bar |
| `09_heatmap.png` | Country metrics heatmap (CFR%) |
| `README.md` | Documentation and insights |
| `LICENSE` | MIT License |

---

## 📌 Global Summary

| Metric | Value |
|--------|-------|
| ✅ Confirmed Cases | **16.48M** |
| ☠️ Deaths | **654K** |
| 💚 Recovered | **9.47M** |
| 🔥 Active Cases | **6.36M** |

> Data period: **April 1 – July 1, 2020** across **195 countries**

---

## 📈 Key Insights

- 🌍 **Americas dominate** — 52.8% of all global cases, nearly triple Europe's 20.0%
- 🇺🇸 **USA leads** with ~4.2M confirmed cases — more than Brazil + India combined
- 💊 **Chile tops recovery** at 95.2% recovery rate; UK sits at just 4.8%
- ☠️ **CFR outliers** — UK (15.33%) and Mexico (11.14%) far exceed the global average
- 📈 **Exponential growth** — cases surged from ~1M to ~16M in just 90 days (16× increase)
- 🚀 **Fastest surges** — Papua New Guinea (212%) and Gambia (194%) led week-over-week growth
- 🔥 **US healthcare strain** — 2.15M active cases with recoveries barely keeping pace
- 🌐 **Rising regions** — SE Asia & Eastern Mediterranean together account for ~20% of cases

---

## 🛠️ Tech Stack

- **Python** — Pandas, NumPy, Matplotlib, Seaborn
- **Power BI** — Interactive 3-page dashboard with DAX & R visuals
- **Tableau Public** — Published live dashboard with LOD calculations
- **Looker Studio** — Real-time Google dashboard connected to BigQuery
- **Jupyter Notebook** — Full analysis environment
- **Dataset** — WHO COVID-19 Global Dataset

---

## 📁 Charts Preview

> All charts generated from WHO dataset using Python (Matplotlib & Seaborn)

| Chart | Description |
|-------|-------------|
| 🧮 `01_kpi_cards.png` | Global KPIs — 16.48M confirmed, 654K deaths, 9.47M recovered, 6.36M active |
| 📊 `02_top10_countries.png` | USA leads with 4.2M, Brazil 2.5M, India 1.5M |
| 💚 `04_recovery_rate.png` | Chile 95.2% vs UK 4.8% |
| 📈 `05_global_trend.png` | 16× growth from 1M → 16.5M in 90 days |
| 📉 `05_timeseries.png` | Daily case progression — Apr to Jun 2020 time series |
| 🗺️ `03_regional_distribution.png` | Americas: 52.8% of global cases |
| 🥧 `06_regional_pie.png` | WHO regional breakdown |
| 🚀 `07_wow_growth.png` | Papua New Guinea & Gambia 200%+ WoW |
| ⚡ `08_active_vs_recovered.png` | Active vs Recovered per country |
| 🔥 `09_heatmap.png` | CFR% heatmap — UK & Mexico stark outliers |

---

## 📌 Data Source

COVID-19 data sourced from the [WHO COVID-19 Global Dataset](https://covid19.who.int/data) covering the **Apr–Jul 2020** pandemic period.

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).
