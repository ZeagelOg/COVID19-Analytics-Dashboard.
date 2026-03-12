<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>COVID-19 Analytics Dashboard — README</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: #ffffff;
    color: #1f2328;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Inter', sans-serif;
    font-size: 16px;
    line-height: 1.6;
  }

  /* GitHub-style page shell */
  .page {
    max-width: 860px;
    margin: 0 auto;
    padding: 32px 24px 80px;
  }

  /* ── HEADING STYLES ── */
  h1 { font-size: 2em; font-weight: 600; border-bottom: 1px solid #d1d9e0; padding-bottom: 10px; margin: 24px 0 16px; line-height: 1.25; }
  h2 { font-size: 1.5em; font-weight: 600; border-bottom: 1px solid #d1d9e0; padding-bottom: 8px; margin: 32px 0 16px; line-height: 1.25; }
  h3 { font-size: 1.25em; font-weight: 600; margin: 24px 0 12px; }

  p { margin-bottom: 16px; color: #1f2328; }

  /* blockquote */
  blockquote {
    border-left: 4px solid #d1d9e0;
    padding: 0 16px;
    color: #656d76;
    margin: 0 0 20px;
  }

  blockquote p { margin: 0; color: #656d76; }

  /* ── LISTS ── */
  ul, ol { padding-left: 24px; margin-bottom: 16px; }
  li { margin-bottom: 4px; color: #1f2328; }
  li strong { color: #1f2328; }

  /* ── INLINE CODE ── */
  code {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 6px;
    padding: 2px 6px;
    font-family: 'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, monospace;
    font-size: 85%;
    color: #1f2328;
  }

  /* ── CODE BLOCKS ── */
  pre {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 6px;
    padding: 16px;
    overflow-x: auto;
    margin-bottom: 16px;
  }

  pre code {
    background: none;
    border: none;
    padding: 0;
    font-size: 87.5%;
    color: #1f2328;
    line-height: 1.7;
  }

  /* ── TABLES ── */
  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 20px;
    font-size: 14px;
  }

  th {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    padding: 8px 16px;
    text-align: left;
    font-weight: 600;
    color: #1f2328;
  }

  td {
    border: 1px solid #d1d9e0;
    padding: 8px 16px;
    color: #1f2328;
    vertical-align: top;
  }

  tr:nth-child(even) td { background: #f6f8fa; }

  /* ── BADGES ── */
  .badges { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 20px; }

  .badge {
    display: inline-flex;
    align-items: center;
    height: 20px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 600;
    padding: 0 8px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    text-decoration: none;
    white-space: nowrap;
  }

  /* ── INSIGHT CALLOUT BOXES ── */
  .callout {
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-left: 4px solid #0969da;
    border-radius: 0 6px 6px 0;
    padding: 12px 16px;
    margin-bottom: 10px;
    font-size: 14px;
    display: flex;
    gap: 10px;
    align-items: flex-start;
  }

  .callout.red   { border-left-color: #cf222e; }
  .callout.green { border-left-color: #1a7f37; }
  .callout.amber { border-left-color: #9a6700; }
  .callout.purple{ border-left-color: #8250df; }

  .callout-icon { font-size: 16px; line-height: 1.5; flex-shrink: 0; }
  .callout-text { color: #1f2328; line-height: 1.55; }
  .callout-text strong { font-weight: 600; }

  /* ── CHART CONTAINERS (github image-style) ── */
  .chart-block {
    border: 1px solid #d1d9e0;
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 20px;
  }

  .chart-caption {
    background: #f6f8fa;
    border-top: 1px solid #d1d9e0;
    padding: 8px 14px;
    font-size: 13px;
    color: #656d76;
    font-style: italic;
  }

  .chart-inner {
    padding: 20px 20px 12px;
    background: #ffffff;
  }

  .chart-title-label {
    font-size: 13px;
    font-weight: 600;
    color: #1f2328;
    margin-bottom: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  /* bar chart */
  .bar-list { display: flex; flex-direction: column; gap: 7px; }

  .bar-row { display: flex; align-items: center; gap: 10px; font-size: 13px; }

  .bar-rank {
    width: 18px; text-align: center;
    font-size: 11px; color: #656d76;
    flex-shrink: 0;
  }

  .bar-lbl {
    width: 88px; text-align: right;
    font-size: 12px; color: #1f2328;
    flex-shrink: 0;
    font-family: 'SFMono-Regular', Consolas, monospace;
  }

  .bar-track {
    flex: 1; height: 20px;
    background: #f6f8fa;
    border: 1px solid #d1d9e0;
    border-radius: 3px;
    overflow: hidden;
    position: relative;
  }

  .bar-fill {
    height: 100%; width: 0;
    border-radius: 2px;
    transition: width 1.4s cubic-bezier(0.16,1,0.3,1);
    position: relative;
  }

  .bar-fill::after {
    content: attr(data-v);
    position: absolute; right: 6px; top: 50%;
    transform: translateY(-50%);
    font-size: 10px; font-weight: 600;
    color: #ffffff;
    font-family: 'SFMono-Regular', Consolas, monospace;
    white-space: nowrap;
  }

  /* trend svg */
  .trend-svg { width: 100%; display: block; }

  /* donut */
  .donut-wrap { display: flex; align-items: center; gap: 24px; }
  .donut-legend { flex: 1; display: flex; flex-direction: column; gap: 7px; }
  .leg-row { display: flex; align-items: center; gap: 8px; font-size: 13px; }
  .leg-dot { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }
  .leg-name { flex: 1; color: #1f2328; }
  .leg-pct { font-family: 'SFMono-Regular', Consolas, monospace; font-size: 12px; color: #656d76; }

  /* stacked */
  .stk-list { display: flex; flex-direction: column; gap: 7px; }
  .stk-row { display: flex; align-items: center; gap: 10px; }
  .stk-lbl { width: 75px; text-align: right; font-size: 12px; color: #1f2328; flex-shrink: 0; font-family: 'SFMono-Regular', Consolas, monospace; }
  .stk-track { flex: 1; height: 18px; display: flex; border-radius: 3px; overflow: hidden; border: 1px solid #d1d9e0; }
  .stk-seg { height: 100%; width: 0; transition: width 1.4s cubic-bezier(0.16,1,0.3,1); }

  /* growth */
  .grw-list { display: flex; flex-direction: column; gap: 7px; }
  .grw-row { display: flex; align-items: center; gap: 10px; }
  .grw-lbl { width: 140px; font-size: 12px; color: #1f2328; flex-shrink: 0; font-family: 'SFMono-Regular', Consolas, monospace; }
  .grw-track { flex: 1; height: 20px; background: #f6f8fa; border: 1px solid #d1d9e0; border-radius: 3px; overflow: hidden; position: relative; }
  .grw-fill { height: 100%; width: 0; background: #cf222e; transition: width 1.4s cubic-bezier(0.16,1,0.3,1); position: relative; }
  .grw-fill::after { content: attr(data-v); position: absolute; right: 6px; top: 50%; transform: translateY(-50%); font-size: 10px; font-weight: 600; color: #fff; font-family: 'SFMono-Regular', Consolas, monospace; }

  /* region bars */
  .rgn-list { display: flex; flex-direction: column; gap: 10px; }
  .rgn-row { display: flex; align-items: center; gap: 10px; }
  .rgn-lbl { width: 100px; font-size: 12px; color: #1f2328; flex-shrink: 0; font-family: 'SFMono-Regular', Consolas, monospace; }
  .rgn-bars { flex: 1; display: flex; flex-direction: column; gap: 3px; }
  .rgn-bar { height: 7px; border-radius: 2px; width: 0; transition: width 1.4s cubic-bezier(0.16,1,0.3,1); }
  .rgn-val { width: 50px; text-align: right; font-size: 11px; color: #656d76; flex-shrink: 0; font-family: 'SFMono-Regular', Consolas, monospace; }

  /* heatmap table inside chart */
  .heat-tbl { width: 100%; border-collapse: collapse; font-size: 13px; }
  .heat-tbl th { background: #f6f8fa; border: 1px solid #d1d9e0; padding: 7px 12px; text-align: right; font-weight: 600; font-size: 12px; color: #1f2328; }
  .heat-tbl th:first-child { text-align: left; }
  .heat-tbl td { border: 1px solid #d1d9e0; padding: 6px 12px; text-align: right; }
  .heat-tbl td:first-child { text-align: left; font-family: 'SFMono-Regular', Consolas, monospace; font-size: 12px; font-weight: 600; }
  .heat-tbl tr:nth-child(even) td { background: #f6f8fa; }
  .heat-cell { display: inline-block; padding: 1px 6px; border-radius: 3px; font-size: 11px; font-weight: 600; font-family: 'SFMono-Regular', Consolas, monospace; }

  /* 2-col chart grid */
  .chart-grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 4px; }

  /* legend row */
  .mini-legend { display: flex; gap: 16px; flex-wrap: wrap; margin-top: 12px; padding-top: 10px; border-top: 1px solid #d1d9e0; }
  .mini-leg-item { display: flex; align-items: center; gap: 5px; font-size: 12px; color: #656d76; }
  .mini-leg-sq { width: 10px; height: 7px; border-radius: 2px; flex-shrink: 0; }

  /* dashboard platform rows */
  .platform-row {
    display: flex; align-items: center; gap: 12px;
    border: 1px solid #d1d9e0; border-radius: 6px;
    padding: 14px 16px; margin-bottom: 10px;
    background: #ffffff;
    transition: background 0.15s;
  }
  .platform-row:hover { background: #f6f8fa; }
  .platform-icon { font-size: 22px; flex-shrink: 0; }
  .platform-info { flex: 1; }
  .platform-name { font-weight: 600; font-size: 14px; color: #1f2328; }
  .platform-desc { font-size: 12px; color: #656d76; margin-top: 2px; }
  .platform-tags { display: flex; gap: 5px; margin-top: 5px; flex-wrap: wrap; }
  .platform-tag { font-size: 11px; background: #f6f8fa; border: 1px solid #d1d9e0; border-radius: 12px; padding: 1px 8px; color: #1f2328; font-family: 'SFMono-Regular', Consolas, monospace; }
  .platform-status { font-size: 12px; font-weight: 600; display: flex; align-items: center; gap: 5px; flex-shrink: 0; }
  .status-dot { width: 8px; height: 8px; border-radius: 50%; animation: blink 1.8s infinite; }

  /* tech stack items */
  .tech-row { display: flex; align-items: flex-start; gap: 12px; padding: 10px 0; border-bottom: 1px solid #f3f4f6; }
  .tech-row:last-child { border-bottom: none; }
  .tech-icon-box { font-size: 20px; flex-shrink: 0; width: 32px; text-align: center; }
  .tech-body { flex: 1; }
  .tech-name { font-weight: 600; font-size: 14px; color: #1f2328; }
  .tech-desc { font-size: 13px; color: #656d76; }
  .tech-badges { display: flex; gap: 5px; margin-top: 4px; flex-wrap: wrap; }

  /* file tree */
  .filetree {
    background: #f6f8fa; border: 1px solid #d1d9e0; border-radius: 6px;
    padding: 16px 20px; font-family: 'SFMono-Regular', Consolas, monospace;
    font-size: 13px; line-height: 2;
  }

  .ft-root { font-weight: 700; color: #1f2328; margin-bottom: 2px; }
  .ft-item { display: flex; align-items: center; gap: 6px; color: #1f2328; }
  .ft-item:hover { color: #0969da; cursor: default; }
  .ft-conn { color: #656d76; flex-shrink: 0; }
  .ft-ico { flex-shrink: 0; }
  .ft-name { flex: 1; }
  .ft-name.nb { color: #0969da; }
  .ft-name.img { color: #656d76; }
  .ft-name.md { color: #1a7f37; font-weight: 600; }
  .ft-desc { font-size: 11px; color: #656d76; white-space: nowrap; }

  /* KPI summary row */
  .kpi-summary {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 12px;
    margin-bottom: 24px;
  }

  .kpi-box {
    border: 1px solid #d1d9e0; border-radius: 6px;
    padding: 14px 16px; background: #ffffff;
    text-align: center;
  }

  .kpi-num {
    font-size: 1.6rem; font-weight: 700; color: #1f2328;
    line-height: 1; margin-bottom: 4px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  }

  .kpi-lbl { font-size: 12px; color: #656d76; }

  /* separator */
  hr { border: none; border-top: 1px solid #d1d9e0; margin: 28px 0; }

  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.2} }

  @media(max-width:640px) {
    .chart-grid-2 { grid-template-columns: 1fr; }
    .kpi-summary { grid-template-columns: repeat(2,1fr); }
  }
</style>
</head>
<body>
<div class="page">

<!-- TITLE -->
<h1>🦠 COVID-19 Global Analytics Dashboard</h1>

<blockquote>
  <p>Analyzing WHO global COVID-19 data covering Apr–Jul 2020 using Python, SQL, Power BI, Tableau, and Looker Studio to uncover case growth trends, regional distributions, recovery rates, and CFR outliers across 195 countries.</p>
</blockquote>

<div class="badges">
  <img class="badge" src="https://img.shields.io/badge/Python-3.10-3572A5?style=flat-square&logo=python&logoColor=white" alt="">
  <img class="badge" src="https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white" alt="">
  <img class="badge" src="https://img.shields.io/badge/Pandas-Data_Analysis-150458?style=flat-square&logo=pandas&logoColor=white" alt="">
  <img class="badge" src="https://img.shields.io/badge/Power_BI-Dashboard-F2C811?style=flat-square&logo=powerbi&logoColor=black" alt="">
  <img class="badge" src="https://img.shields.io/badge/Tableau-Public-E97627?style=flat-square&logo=tableau&logoColor=white" alt="">
  <img class="badge" src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="">
</div>

<hr>

<!-- KPI SUMMARY -->
<h2>📌 Global Summary</h2>

<div class="kpi-summary">
  <div class="kpi-box">
    <div class="kpi-num" data-target="16.48" data-dec="2"><span class="kc">0</span>M</div>
    <div class="kpi-lbl">Confirmed Cases</div>
  </div>
  <div class="kpi-box">
    <div class="kpi-num" data-target="654" data-dec="0"><span class="kc">0</span>K</div>
    <div class="kpi-lbl">Deaths</div>
  </div>
  <div class="kpi-box">
    <div class="kpi-num" data-target="9.47" data-dec="2"><span class="kc">0</span>M</div>
    <div class="kpi-lbl">Recovered</div>
  </div>
  <div class="kpi-box">
    <div class="kpi-num" data-target="6.36" data-dec="2"><span class="kc">0</span>M</div>
    <div class="kpi-lbl">Active Cases</div>
  </div>
</div>

<hr>

<!-- LIVE DASHBOARDS -->
<h2>📊 Live Dashboards</h2>

<div id="platforms">
  <div class="platform-row">
    <div class="platform-icon">📊</div>
    <div class="platform-info">
      <div class="platform-name">Power BI</div>
      <div class="platform-desc">Multi-page interactive dashboard with slicers, drill-through, and R visuals</div>
      <div class="platform-tags">
        <span class="platform-tag">DAX</span>
        <span class="platform-tag">DirectQuery</span>
        <span class="platform-tag">R Visuals</span>
        <span class="platform-tag">3 Pages</span>
      </div>
    </div>
    <div class="platform-status" style="color:#9a6700">
      <div class="status-dot" style="background:#9a6700"></div>
      <a href="https://app.powerbi.com" target="_blank" style="color:#0969da;text-decoration:none;font-size:13px">covid_dashboard.pbix ↗</a>
    </div>
  </div>

  <div class="platform-row">
    <div class="platform-icon">🔭</div>
    <div class="platform-info">
      <div class="platform-name">Looker Studio</div>
      <div class="platform-desc">Real-time Google dashboard connected to BigQuery and Google Sheets</div>
      <div class="platform-tags">
        <span class="platform-tag">BigQuery</span>
        <span class="platform-tag">Sheets</span>
        <span class="platform-tag">Geo Maps</span>
        <span class="platform-tag">Scorecards</span>
      </div>
    </div>
    <div class="platform-status" style="color:#1a7f37">
      <div class="status-dot" style="background:#1a7f37"></div>
      <a href="https://lookerstudio.google.com" target="_blank" style="color:#0969da;text-decoration:none;font-size:13px">link coming soon ↗</a>
    </div>
  </div>

  <div class="platform-row">
    <div class="platform-icon">📈</div>
    <div class="platform-info">
      <div class="platform-name">Tableau Public</div>
      <div class="platform-desc">Published live workbook with LOD calculations, parameters, and filter actions</div>
      <div class="platform-tags">
        <span class="platform-tag">LOD Calc</span>
        <span class="platform-tag">Parameters</span>
        <span class="platform-tag">Actions</span>
        <span class="platform-tag">Sets</span>
      </div>
    </div>
    <div class="platform-status" style="color:#8250df">
      <div class="status-dot" style="background:#8250df"></div>
      <a href="https://public.tableau.com" target="_blank" style="color:#0969da;text-decoration:none;font-size:13px">link coming soon ↗</a>
    </div>
  </div>
</div>

<hr>

<!-- PROJECT STRUCTURE -->
<h2>🗂️ Project Structure</h2>

<div class="filetree">
  <div class="ft-root">📁 COVID19-Analytics-Dashboard/</div>
  <div style="padding-left:16px">
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">📓</span><span class="ft-name nb">COVID19_Analytics_Dashboard.ipynb</span><span class="ft-desc"> — Main analysis notebook</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">01_kpi_cards.png</span><span class="ft-desc"> — Global KPI summary</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">02_top10_countries.png</span><span class="ft-desc"> — Top 10 countries bar chart</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">03_regional_distribution.png</span><span class="ft-desc"> — Regional grouped bar chart</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">04_recovery_rate.png</span><span class="ft-desc"> — Recovery rate by country</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">05_global_trend.png</span><span class="ft-desc"> — 90-day global growth trends</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">05_timeseries.png</span><span class="ft-desc"> — Time series Apr–Jun 2020</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">06_regional_pie.png</span><span class="ft-desc"> — WHO region pie chart</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">07_wow_growth.png</span><span class="ft-desc"> — Week-over-week growth leaders</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">08_active_vs_recovered.png</span><span class="ft-desc"> — Active vs Recovered stacked bar</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">🖼️</span><span class="ft-name img">09_heatmap.png</span><span class="ft-desc"> — Country metrics heatmap (CFR%)</span></div>
    <div class="ft-item"><span class="ft-conn">├──</span><span class="ft-ico">📋</span><span class="ft-name md">README.md</span><span class="ft-desc"> — Documentation and insights</span></div>
    <div class="ft-item"><span class="ft-conn">└──</span><span class="ft-ico">📄</span><span class="ft-name">LICENSE</span><span class="ft-desc"> — MIT License</span></div>
  </div>
</div>

<hr>

<!-- KEY INSIGHTS -->
<h2>📈 Key Insights</h2>

<div class="callout">
  <span class="callout-icon">🌍</span>
  <span class="callout-text"><strong>Americas dominate globally</strong> — The Americas account for <strong>52.8%</strong> of all confirmed cases worldwide, nearly triple Europe's share of 20.0%. Over 70% of global cases were concentrated in just two regions.</span>
</div>

<div class="callout red">
  <span class="callout-icon">🇺🇸</span>
  <span class="callout-text"><strong>US leads by a wide margin</strong> — The United States recorded ~4.2M confirmed cases, more than Brazil (~2.5M) and India (~1.5M) combined — 25.5% of the global total at the dataset cutoff.</span>
</div>

<div class="callout green">
  <span class="callout-icon">💊</span>
  <span class="callout-text"><strong>Chile tops recovery rates</strong> — Chile leads all countries with a recovery rate exceeding <strong>95%</strong>, while the United Kingdom sits at the bottom with under 5% — likely due to national reporting differences.</span>
</div>

<div class="callout red">
  <span class="callout-icon">☠️</span>
  <span class="callout-text"><strong>UK &amp; Mexico: high CFR outliers</strong> — Despite relatively fewer cases, the UK has a CFR of <strong>15.33%</strong> and Mexico <strong>11.14%</strong>, far above the global average, signalling severe healthcare strain.</span>
</div>

<div class="callout amber">
  <span class="callout-icon">📈</span>
  <span class="callout-text"><strong>Steep growth Apr–Jul 2020</strong> — Global confirmed cases grew from ~1M to ~16M in just 90 days (a 16× increase), with recoveries lagging significantly behind.</span>
</div>

<div class="callout red">
  <span class="callout-icon">🚀</span>
  <span class="callout-text"><strong>Fastest surge: Papua New Guinea &amp; Gambia</strong> — Week-over-week growth exceeded <strong>200%+</strong> in smaller nations, signalling emerging outbreak zones with limited healthcare capacity.</span>
</div>

<div class="callout amber">
  <span class="callout-icon">🔥</span>
  <span class="callout-text"><strong>US active cases critically high</strong> — The US had ~2.15M active cases with recovered only slightly ahead, indicating enormous concurrent pressure on healthcare systems.</span>
</div>

<div class="callout purple">
  <span class="callout-icon">🌐</span>
  <span class="callout-text"><strong>South-East Asia &amp; Eastern Mediterranean rising</strong> — Together ~20% of global cases, showing spread well beyond initial Western epicentres by mid-2020.</span>
</div>

<hr>

<!-- TECH STACK -->
<h2>🛠️ Tech Stack</h2>

<table>
  <thead><tr><th>Category</th><th>Tools</th></tr></thead>
  <tbody>
    <tr><td><strong>Language</strong></td><td>Python 3.x</td></tr>
    <tr><td><strong>Data Analysis</strong></td><td>Pandas, NumPy</td></tr>
    <tr><td><strong>Visualization</strong></td><td>Matplotlib, Seaborn</td></tr>
    <tr><td><strong>Notebook Environment</strong></td><td>Jupyter Notebook</td></tr>
    <tr><td><strong>BI Platforms</strong></td><td>Power BI, Tableau Public, Looker Studio</td></tr>
    <tr><td><strong>Data Source</strong></td><td>WHO COVID-19 Global Dataset</td></tr>
    <tr><td><strong>Version Control</strong></td><td>Git, GitHub</td></tr>
  </tbody>
</table>

<hr>

<!-- GETTING STARTED -->
<h2>🚀 Getting Started</h2>

<p>1. <strong>Clone the repository</strong></p>
<pre><code>git clone https://github.com/ZeagelOg/COVID19-Analytics-Dashboard.git
cd COVID19-Analytics-Dashboard</code></pre>

<p>2. <strong>Install dependencies</strong></p>
<pre><code>pip install pandas numpy matplotlib seaborn jupyter</code></pre>

<p>3. <strong>Launch the notebook</strong></p>
<pre><code>jupyter notebook COVID19_Analytics_Dashboard.ipynb</code></pre>

<p>4. <strong>Run all cells</strong> — Charts are auto-saved as <code>.png</code> files in the project root.</p>

<hr>

<!-- CHARTS PREVIEW -->
<h2>📁 Charts Preview</h2>

<!-- Chart 1: Top 10 + Recovery -->
<div class="chart-grid-2" style="margin-bottom:16px">
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">🏆 Top 10 Countries — Confirmed Cases</div>
      <div class="bar-list" id="top10"></div>
    </div>
    <div class="chart-caption">02_top10_countries.png — Confirmed cases by country</div>
  </div>
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">💚 Recovery Rate by Country (%)</div>
      <div class="bar-list" id="recovery"></div>
    </div>
    <div class="chart-caption">04_recovery_rate.png — Chile leads at 95%+</div>
  </div>
</div>

<!-- Chart 2: Global Trend -->
<div class="chart-block">
  <div class="chart-inner">
    <div class="chart-title-label">📈 Global COVID-19 Growth Trends — Apr to Jul 2020</div>
    <svg id="trend-svg" class="trend-svg" height="220"></svg>
    <div class="mini-legend">
      <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#0969da"></div>Confirmed</div>
      <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#9a6700"></div>Active</div>
      <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#1a7f37"></div>Recovered</div>
      <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#cf222e"></div>Deaths</div>
    </div>
  </div>
  <div class="chart-caption">05_global_trend.png — 16× growth in 90 days</div>
</div>

<!-- Chart 3: Regional + Pie -->
<div class="chart-grid-2" style="margin-bottom:16px">
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">🗺️ Regional Distribution of Cases</div>
      <div class="rgn-list" id="regional"></div>
      <div class="mini-legend">
        <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#0969da"></div>Cases</div>
        <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#cf222e"></div>Deaths</div>
        <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#1a7f37"></div>Recovered</div>
      </div>
    </div>
    <div class="chart-caption">03_regional_distribution.png</div>
  </div>
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">🥧 Confirmed Cases by WHO Region</div>
      <div class="donut-wrap">
        <svg id="donut-svg" width="165" height="165" viewBox="0 0 165 165" style="flex-shrink:0"></svg>
        <div class="donut-legend" id="donut-legend"></div>
      </div>
    </div>
    <div class="chart-caption">06_regional_pie.png — Americas: 52.8%</div>
  </div>
</div>

<!-- Chart 4: Growth + Stacked -->
<div class="chart-grid-2" style="margin-bottom:16px">
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">🚀 Top 10 — Fastest WoW Case Growth</div>
      <div class="grw-list" id="growth"></div>
    </div>
    <div class="chart-caption">07_wow_growth.png — PNG & Gambia 200%+</div>
  </div>
  <div class="chart-block" style="margin-bottom:0">
    <div class="chart-inner">
      <div class="chart-title-label">⚡ Active vs Recovered Cases</div>
      <div class="stk-list" id="stacked"></div>
      <div class="mini-legend">
        <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#1a7f37"></div>Recovered</div>
        <div class="mini-leg-item"><div class="mini-leg-sq" style="background:#9a6700"></div>Active</div>
      </div>
    </div>
    <div class="chart-caption">08_active_vs_recovered.png</div>
  </div>
</div>

<!-- Chart 5: Heatmap -->
<div class="chart-block">
  <div class="chart-inner">
    <div class="chart-title-label">🔥 Country Metrics Heatmap — CFR%</div>
    <div style="overflow-x:auto">
      <table class="heat-tbl" id="heatmap"></table>
    </div>
  </div>
  <div class="chart-caption">09_heatmap.png — UK (15.33%) and Mexico (11.14%) are stark CFR outliers</div>
</div>

<hr>

<!-- DATA SOURCE -->
<h2>📌 Data Source</h2>
<p>COVID-19 data sourced from the <a href="https://covid19.who.int/data" style="color:#0969da">WHO COVID-19 Global Dataset</a> covering the Apr–Jul 2020 pandemic period.</p>

<hr>

<!-- LICENSE -->
<h2>📄 License</h2>
<p>This project is open-source and available under the <a href="#" style="color:#0969da">MIT License</a>.</p>

</div><!-- /.page -->

<script>
// ─── DATA ───
const top10 = [
  {c:'USA',       v:4200000},{c:'Brazil',    v:2500000},{c:'India',     v:1500000},
  {c:'Russia',    v:820000}, {c:'S. Africa', v:450000}, {c:'Mexico',    v:390000},
  {c:'Peru',      v:370000}, {c:'Chile',     v:340000}, {c:'UK',        v:300000},
  {c:'Iran',      v:290000},
];
const recovery = [
  {c:'Chile',     r:95.2},{c:'Iran',      r:85.1},{c:'Russia',    r:78.3},
  {c:'S. Africa', r:62.5},{c:'Brazil',    r:58.7},{c:'India',     r:55.2},
  {c:'Peru',      r:49.8},{c:'USA',       r:45.1},{c:'Mexico',    r:28.6},
  {c:'UK',        r:4.8},
];
const trend = [
  {l:'Apr 1',  cf:1000000,  rc:210000,  ac:760000,  dt:30000},
  {l:'Apr 15', cf:2100000,  rc:480000,  ac:1520000, dt:100000},
  {l:'May 1',  cf:3300000,  rc:1000000, ac:2100000, dt:200000},
  {l:'May 15', cf:4800000,  rc:1800000, ac:2700000, dt:300000},
  {l:'Jun 1',  cf:6800000,  rc:3200000, ac:3300000, dt:300000},
  {l:'Jun 15', cf:9500000,  rc:5100000, ac:4050000, dt:350000},
  {l:'Jul 1',  cf:16480000, rc:9470000, ac:6360000, dt:654000},
];
const regions = [
  {n:'Americas',   cases:8500,deaths:320,rec:5200},
  {n:'Europe',     cases:3300,deaths:210,rec:2400},
  {n:'SE Asia',    cases:2000,deaths:48, rec:1200},
  {n:'E. Medit.',  cases:1400,deaths:37, rec:950},
  {n:'Africa',     cases:870, deaths:18, rec:480},
  {n:'W. Pacific', cases:310, deaths:8,  rec:250},
];
const pieData = [
  {n:'Americas',   p:52.8,col:'#0969da'},
  {n:'Europe',     p:20.0,col:'#cf222e'},
  {n:'SE Asia',    p:11.5,col:'#1a7f37'},
  {n:'E. Medit.',  p:8.5, col:'#9a6700'},
  {n:'Africa',     p:5.3, col:'#8250df'},
  {n:'W. Pacific', p:1.9, col:'#0550ae'},
];
const growth = [
  {c:'Papua New Guinea',r:212},{c:'Gambia',r:194},{c:'Bahamas',r:178},
  {c:'Zimbabwe',r:165},{c:'Libya',r:153},{c:'Ethiopia',r:142},
  {c:'Botswana',r:131},{c:'Lesotho',r:121},{c:'Suriname',r:112},{c:'Costa Rica',r:103},
];
const stacked = [
  {c:'USA',     ac:2150000,rc:1890000},{c:'Brazil',  ac:900000, rc:1450000},
  {c:'India',   ac:600000, rc:830000}, {c:'Russia',  ac:175000, rc:642000},
  {c:'UK',      ac:285000, rc:14000},  {c:'Mexico',  ac:270000, rc:110000},
  {c:'S.Africa',ac:165000, rc:281000}, {c:'Chile',   ac:15000,  rc:323000},
];
const heatData = [
  {c:'USA',     cf:4200000,dt:145000,rc:1890000,ac:2150000,cfr:3.45},
  {c:'Brazil',  cf:2500000,dt:89000, rc:1450000,ac:900000, cfr:3.56},
  {c:'India',   cf:1500000,dt:33000, rc:830000, ac:600000, cfr:2.20},
  {c:'Russia',  cf:820000, dt:13400, rc:642000, ac:175000, cfr:1.63},
  {c:'S.Africa',cf:450000, dt:7000,  rc:281000, ac:165000, cfr:1.56},
  {c:'Mexico',  cf:390000, dt:43000, rc:110000, ac:270000, cfr:11.14},
  {c:'Peru',    cf:370000, dt:17000, rc:184000, ac:170000, cfr:4.59},
  {c:'Chile',   cf:340000, dt:9000,  rc:323000, ac:15000,  cfr:2.65},
  {c:'UK',      cf:300000, dt:46000, rc:14000,  ac:285000, cfr:15.33},
  {c:'Iran',    cf:290000, dt:15600, rc:246000, ac:28000,  cfr:5.38},
];

function fmt(n) {
  if(n>=1000000) return (n/1e6).toFixed(1)+'M';
  if(n>=1000)    return (n/1000).toFixed(0)+'K';
  return n;
}

// KPI counters
function animateKPIs() {
  document.querySelectorAll('.kpi-num[data-target]').forEach(el => {
    const span = el.querySelector('.kc');
    const target = parseFloat(el.dataset.target);
    const dec = parseInt(el.dataset.dec);
    let start = null;
    const step = ts => {
      if(!start) start = ts;
      const p = Math.min((ts-start)/1600, 1);
      const e = 1-Math.pow(1-p, 3);
      span.textContent = (e*target).toFixed(dec);
      if(p<1) requestAnimationFrame(step);
      else span.textContent = target.toFixed(dec);
    };
    requestAnimationFrame(step);
  });
}

// Top 10
function renderTop10() {
  const el = document.getElementById('top10');
  const max = top10[0].v;
  top10.forEach((d,i) => {
    const p = (d.v/max*100).toFixed(1);
    el.innerHTML += `<div class="bar-row">
      <div class="bar-rank">${i+1}</div>
      <div class="bar-lbl">${d.c}</div>
      <div class="bar-track"><div class="bar-fill" style="background:#0969da" data-w="${p}" data-v="${fmt(d.v)}"></div></div>
    </div>`;
  });
}

// Recovery
function renderRecovery() {
  const el = document.getElementById('recovery');
  recovery.forEach((d,i) => {
    const col = d.r < 10 ? '#cf222e' : d.r > 70 ? '#1a7f37' : '#0969da';
    el.innerHTML += `<div class="bar-row">
      <div class="bar-rank">${i+1}</div>
      <div class="bar-lbl">${d.c}</div>
      <div class="bar-track"><div class="bar-fill" style="background:${col}" data-w="${d.r}" data-v="${d.r}%"></div></div>
    </div>`;
  });
}

// Trend SVG
function renderTrend() {
  const svg = document.getElementById('trend-svg');
  const W = svg.parentElement.offsetWidth - 40 || 780;
  const H = 200;
  svg.setAttribute('viewBox', `0 0 ${W} ${H}`);
  svg.setAttribute('width', W);

  const maxV = 18000000;
  const pl=60,pr=16,pt=12,pb=28;
  const iW=W-pl-pr, iH=H-pt-pb;
  const n=trend.length;
  const X = i => pl+(i/(n-1))*iW;
  const Y = v => pt+iH-(v/maxV)*iH;

  let grid='';
  [0,.25,.5,.75,1].forEach(p=>{
    const yy=pt+iH*(1-p);
    grid+=`<line x1="${pl}" x2="${W-pr}" y1="${yy}" y2="${yy}" stroke="#d1d9e0" stroke-width="1"/>`;
    grid+=`<text x="${pl-5}" y="${yy+4}" text-anchor="end" fill="#656d76" font-size="9" font-family="monospace">${fmt(p*maxV)}</text>`;
  });
  let xlbls='';
  trend.forEach((d,i)=>{
    xlbls+=`<text x="${X(i)}" y="${H-4}" text-anchor="middle" fill="#656d76" font-size="9" font-family="monospace">${d.l}</text>`;
  });

  const series=[
    {key:'cf',col:'#0969da',w:2.5,delay:0},
    {key:'ac',col:'#9a6700',w:2,  delay:300},
    {key:'rc',col:'#1a7f37',w:2,  delay:600},
    {key:'dt',col:'#cf222e',w:1.5,delay:900},
  ];

  // area fill under confirmed
  const areaPts = trend.map((d,i)=>`${i===0?'M':'L'}${X(i)},${Y(d.cf)}`).join(' ');
  let paths=`<defs><linearGradient id="ag" x1="0" x2="0" y1="0" y2="1">
    <stop offset="0%" stop-color="#0969da" stop-opacity="0.12"/>
    <stop offset="100%" stop-color="#0969da" stop-opacity="0.01"/>
  </linearGradient></defs>
  <path d="${areaPts} L${X(n-1)},${pt+iH} L${X(0)},${pt+iH} Z" fill="url(#ag)"/>`;

  series.forEach(s=>{
    const pts=trend.map((d,i)=>`${i===0?'M':'L'}${X(i)},${Y(d[s.key])}`).join(' ');
    paths+=`<path d="${pts}" fill="none" stroke="${s.col}" stroke-width="${s.w}" stroke-linecap="round" stroke-linejoin="round" stroke-dasharray="2000" stroke-dashoffset="2000" style="animation:draw 1.8s ${s.delay}ms ease forwards"/>`;
    trend.forEach((d,i)=>{
      paths+=`<circle cx="${X(i)}" cy="${Y(d[s.key])}" r="3" fill="${s.col}"/>`;
    });
  });

  svg.innerHTML = `<style>@keyframes draw{to{stroke-dashoffset:0}}</style>${grid}${xlbls}${paths}`;
}

// Regional
function renderRegional() {
  const el=document.getElementById('regional');
  const maxC=Math.max(...regions.map(r=>r.cases));
  regions.forEach(r=>{
    el.innerHTML+=`<div class="rgn-row">
      <div class="rgn-lbl">${r.n}</div>
      <div class="rgn-bars">
        <div class="rgn-bar" style="background:#0969da" data-w="${r.cases/maxC*100}"></div>
        <div class="rgn-bar" style="background:#cf222e" data-w="${r.deaths/maxC*100}"></div>
        <div class="rgn-bar" style="background:#1a7f37" data-w="${r.rec/maxC*100}"></div>
      </div>
      <div class="rgn-val">${(r.cases/1000).toFixed(1)}K</div>
    </div>`;
  });
}

// Donut
function renderDonut() {
  const svg=document.getElementById('donut-svg');
  const leg=document.getElementById('donut-legend');
  const cx=82.5,cy=82.5,R=72,r=46;
  let a=-Math.PI/2,paths='';
  pieData.forEach(d=>{
    const sw=(d.p/100)*Math.PI*2;
    const x1=cx+R*Math.cos(a),y1=cy+R*Math.sin(a);
    const x2=cx+R*Math.cos(a+sw),y2=cy+R*Math.sin(a+sw);
    const xi1=cx+r*Math.cos(a),yi1=cy+r*Math.sin(a);
    const xi2=cx+r*Math.cos(a+sw),yi2=cy+r*Math.sin(a+sw);
    const lg=sw>Math.PI?1:0;
    paths+=`<path d="M${xi1},${yi1} L${x1},${y1} A${R},${R} 0 ${lg} 1 ${x2},${y2} L${xi2},${yi2} A${r},${r} 0 ${lg} 0 ${xi1},${yi1} Z" fill="${d.col}" opacity="0.85" style="cursor:pointer;transition:opacity .2s" onmouseover="this.style.opacity=1" onmouseout="this.style.opacity=0.85"/>`;
    leg.innerHTML+=`<div class="leg-row"><div class="leg-dot" style="background:${d.col}"></div><span class="leg-name">${d.n}</span><span class="leg-pct">${d.p}%</span></div>`;
    a+=sw;
  });
  svg.innerHTML=paths
    +`<text x="82.5" y="78" text-anchor="middle" fill="#1f2328" font-size="14" font-weight="700" font-family="-apple-system,sans-serif">16.5M</text>`
    +`<text x="82.5" y="93" text-anchor="middle" fill="#656d76" font-size="9" font-family="monospace">CASES</text>`;
}

// Growth
function renderGrowth() {
  const el=document.getElementById('growth');
  const max=growth[0].r;
  growth.forEach(d=>{
    el.innerHTML+=`<div class="grw-row">
      <div class="grw-lbl">${d.c}</div>
      <div class="grw-track"><div class="grw-fill" data-w="${d.r/max*100}" data-v="${d.r}%"></div></div>
    </div>`;
  });
}

// Stacked
function renderStacked() {
  const el=document.getElementById('stacked');
  const maxT=Math.max(...stacked.map(d=>d.ac+d.rc));
  stacked.forEach(d=>{
    const t=d.ac+d.rc;
    el.innerHTML+=`<div class="stk-row">
      <div class="stk-lbl">${d.c}</div>
      <div class="stk-track" style="width:${(t/maxT*100).toFixed(1)}%">
        <div class="stk-seg" style="background:#1a7f37" data-w="${(d.rc/t*100).toFixed(1)}"></div>
        <div class="stk-seg" style="background:#9a6700" data-w="${(d.ac/t*100).toFixed(1)}"></div>
      </div>
    </div>`;
  });
}

// Heatmap
function renderHeatmap() {
  const tbl=document.getElementById('heatmap');
  const maxCFR=Math.max(...heatData.map(d=>d.cfr));
  tbl.innerHTML=`<thead><tr><th>Country</th><th>Confirmed</th><th>Deaths</th><th>Recovered</th><th>Active</th><th>CFR %</th></tr></thead><tbody></tbody>`;
  const tb=tbl.querySelector('tbody');
  heatData.forEach(d=>{
    const ci=(d.cf/4200000*0.4);
    const di=(d.dt/145000*0.4);
    const cfrI=d.cfr/maxCFR;
    const cfrCol=cfrI>0.6?'#cf222e':cfrI>0.3?'#9a6700':'#1a7f37';
    tb.innerHTML+=`<tr>
      <td>${d.c}</td>
      <td><span class="heat-cell" style="background:rgba(9,105,218,${ci});color:#1f2328">${fmt(d.cf)}</span></td>
      <td><span class="heat-cell" style="background:rgba(207,34,46,${di});color:${di>0.2?'#fff':'#1f2328'}">${fmt(d.dt)}</span></td>
      <td><span class="heat-cell" style="background:rgba(26,127,55,0.1);color:#1a7f37">${fmt(d.rc)}</span></td>
      <td><span class="heat-cell" style="background:rgba(154,103,0,0.1);color:#9a6700">${fmt(d.ac)}</span></td>
      <td><span class="heat-cell" style="background:${cfrCol}18;color:${cfrCol};border:1px solid ${cfrCol}40;min-width:50px">${d.cfr}%</span></td>
    </tr>`;
  });
}

function animateBars() {
  document.querySelectorAll('.bar-fill[data-w]').forEach(el=>el.style.width=el.dataset.w+'%');
  document.querySelectorAll('.stk-seg[data-w]').forEach(el=>el.style.width=el.dataset.w+'%');
  document.querySelectorAll('.grw-fill[data-w]').forEach(el=>el.style.width=el.dataset.w+'%');
  document.querySelectorAll('.rgn-bar[data-w]').forEach(el=>el.style.width=el.dataset.w+'%');
}

window.addEventListener('load', () => {
  animateKPIs();
  renderTop10();
  renderRecovery();
  renderTrend();
  renderRegional();
  renderDonut();
  renderGrowth();
  renderStacked();
  renderHeatmap();
  setTimeout(animateBars, 300);
});
</script>
</body>
</html>
