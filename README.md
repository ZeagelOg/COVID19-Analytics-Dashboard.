<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>COVID-19 Analytics Dashboard</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #03050a;
    --surface: #080d16;
    --card: #0d1520;
    --border: #1a2535;
    --accent: #00e5ff;
    --accent2: #ff4b6e;
    --accent3: #39ff6e;
    --accent4: #ffb800;
    --text: #e8edf5;
    --muted: #5a6a80;
    --confirmed: #00e5ff;
    --deaths: #ff4b6e;
    --recovered: #39ff6e;
    --active: #ffb800;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Animated grid background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,229,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,229,255,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container {
    position: relative;
    z-index: 1;
    max-width: 1400px;
    margin: 0 auto;
    padding: 40px 24px;
  }

  /* HEADER */
  header {
    margin-bottom: 48px;
    animation: fadeDown 0.8s ease both;
  }

  .header-badge {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--accent);
    border: 1px solid rgba(0,229,255,0.3);
    padding: 4px 12px;
    border-radius: 2px;
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 16px;
  }

  .header-badge::before {
    content: '';
    width: 6px; height: 6px;
    background: var(--accent);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }

  header h1 {
    font-size: clamp(2rem, 5vw, 3.8rem);
    font-weight: 800;
    line-height: 1;
    letter-spacing: -2px;
  }

  header h1 span { color: var(--accent); }

  header p {
    margin-top: 12px;
    color: var(--muted);
    font-size: 15px;
    font-family: 'Space Mono', monospace;
    letter-spacing: 0.5px;
  }

  /* KPI CARDS */
  .kpi-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 40px;
    animation: fadeUp 0.8s 0.2s ease both;
  }

  .kpi-card {
    background: var(--card);
    border: 1px solid var(--border);
    padding: 24px;
    position: relative;
    overflow: hidden;
    transition: transform 0.2s, border-color 0.2s;
  }

  .kpi-card:hover {
    transform: translateY(-3px);
    border-color: var(--accent-color);
  }

  .kpi-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 3px;
    background: var(--accent-color);
  }

  .kpi-card::after {
    content: '';
    position: absolute;
    bottom: -40px; right: -40px;
    width: 100px; height: 100px;
    border-radius: 50%;
    background: var(--accent-color);
    opacity: 0.05;
  }

  .kpi-label {
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 10px;
  }

  .kpi-value {
    font-size: 2.2rem;
    font-weight: 800;
    letter-spacing: -1px;
    color: var(--accent-color);
    counter-reset: num;
    line-height: 1;
  }

  .kpi-sub {
    margin-top: 8px;
    font-size: 12px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }

  /* SECTION HEADERS */
  .section-header {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 20px;
    margin-top: 40px;
  }

  .section-header h2 {
    font-size: 1.1rem;
    font-weight: 700;
    letter-spacing: 1px;
    text-transform: uppercase;
  }

  .section-line {
    flex: 1;
    height: 1px;
    background: linear-gradient(90deg, var(--border), transparent);
  }

  /* CHARTS GRID */
  .chart-grid-2 {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    margin-bottom: 20px;
    animation: fadeUp 0.8s 0.4s ease both;
  }

  .chart-grid-3 {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 20px;
    margin-bottom: 20px;
    animation: fadeUp 0.8s 0.5s ease both;
  }

  .chart-full {
    margin-bottom: 20px;
    animation: fadeUp 0.8s 0.6s ease both;
  }

  .chart-card {
    background: var(--card);
    border: 1px solid var(--border);
    padding: 24px;
    position: relative;
  }

  .chart-title {
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .chart-title-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--accent);
    flex-shrink: 0;
  }

  /* BAR CHART */
  .bar-chart { display: flex; flex-direction: column; gap: 10px; }

  .bar-row {
    display: flex;
    align-items: center;
    gap: 12px;
    font-size: 12px;
  }

  .bar-label {
    width: 100px;
    text-align: right;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--text);
    flex-shrink: 0;
  }

  .bar-track {
    flex: 1;
    height: 24px;
    background: rgba(255,255,255,0.04);
    border-radius: 2px;
    overflow: hidden;
    position: relative;
  }

  .bar-fill {
    height: 100%;
    border-radius: 2px;
    transition: width 1.5s cubic-bezier(0.16,1,0.3,1);
    width: 0;
    position: relative;
  }

  .bar-fill::after {
    content: attr(data-val);
    position: absolute;
    right: 8px;
    top: 50%;
    transform: translateY(-50%);
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: rgba(0,0,0,0.8);
    font-weight: 700;
    white-space: nowrap;
  }

  .bar-rank {
    width: 20px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    text-align: center;
    flex-shrink: 0;
  }

  /* TREND CHART */
  .trend-svg { width: 100%; overflow: visible; }

  /* PIE / DONUT */
  .donut-wrapper {
    display: flex;
    align-items: center;
    gap: 24px;
  }

  .donut-svg { flex-shrink: 0; }

  .donut-legend { flex: 1; display: flex; flex-direction: column; gap: 10px; }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 10px;
    font-size: 12px;
  }

  .legend-dot {
    width: 10px; height: 10px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .legend-name { color: var(--text); font-size: 12px; }

  .legend-pct {
    margin-left: auto;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--accent);
  }

  /* HEATMAP */
  .heatmap-table {
    width: 100%;
    border-collapse: collapse;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
  }

  .heatmap-table th {
    padding: 8px 12px;
    text-align: right;
    color: var(--muted);
    font-size: 10px;
    letter-spacing: 1px;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
  }

  .heatmap-table th:first-child { text-align: left; }

  .heatmap-table td {
    padding: 8px 12px;
    text-align: right;
    border-bottom: 1px solid rgba(255,255,255,0.03);
    transition: background 0.2s;
  }

  .heatmap-table td:first-child { text-align: left; font-size: 12px; }

  .heatmap-table tr:hover td { background: rgba(255,255,255,0.03); }

  .heat-cell {
    display: inline-block;
    padding: 3px 8px;
    border-radius: 2px;
    font-weight: 700;
    min-width: 70px;
    text-align: right;
  }

  /* STACKED BAR */
  .stacked-row {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 8px;
    font-size: 12px;
  }

  .stacked-label {
    width: 80px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--text);
    flex-shrink: 0;
    text-align: right;
  }

  .stacked-track {
    flex: 1;
    height: 20px;
    display: flex;
    border-radius: 2px;
    overflow: hidden;
  }

  .stacked-seg {
    height: 100%;
    transition: width 1.5s cubic-bezier(0.16,1,0.3,1);
    width: 0;
  }

  /* GROWTH CHART */
  .growth-bar-row {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 10px;
  }

  .growth-label {
    width: 130px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--text);
    flex-shrink: 0;
  }

  .growth-track {
    flex: 1;
    height: 22px;
    background: rgba(255,255,255,0.04);
    border-radius: 2px;
    overflow: hidden;
    position: relative;
  }

  .growth-fill {
    height: 100%;
    background: linear-gradient(90deg, #ff4b6e, #ff8c42);
    border-radius: 2px;
    width: 0;
    transition: width 1.5s cubic-bezier(0.16,1,0.3,1);
    position: relative;
  }

  .growth-fill::after {
    content: attr(data-val);
    position: absolute;
    right: 8px;
    top: 50%;
    transform: translateY(-50%);
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: rgba(255,255,255,0.9);
    font-weight: 700;
  }

  /* REGION BARS */
  .region-row {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 14px;
  }

  .region-label {
    width: 140px;
    font-family: 'Space Mono', monospace;
    font-size: 10px;
    color: var(--text);
    flex-shrink: 0;
  }

  .region-bars { flex: 1; display: flex; flex-direction: column; gap: 3px; }

  .region-mini-bar {
    height: 8px;
    border-radius: 1px;
    width: 0;
    transition: width 1.5s cubic-bezier(0.16,1,0.3,1);
  }

  .region-vals {
    font-family: 'Space Mono', monospace;
    font-size: 9px;
    color: var(--muted);
    width: 80px;
    text-align: right;
    flex-shrink: 0;
  }

  /* TOOLTIP */
  .tooltip {
    position: fixed;
    background: rgba(8,13,22,0.97);
    border: 1px solid var(--border);
    padding: 10px 14px;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    pointer-events: none;
    z-index: 1000;
    display: none;
    max-width: 200px;
  }

  /* ANIMATIONS */
  @keyframes fadeDown {
    from { opacity: 0; transform: translateY(-20px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.3; }
  }

  @keyframes drawLine {
    from { stroke-dashoffset: 2000; }
    to { stroke-dashoffset: 0; }
  }

  .trend-line {
    stroke-dasharray: 2000;
    stroke-dashoffset: 2000;
    animation: drawLine 2s ease forwards;
  }

  /* FOOTER */
  footer {
    margin-top: 60px;
    padding-top: 24px;
    border-top: 1px solid var(--border);
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-family: 'Space Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    animation: fadeUp 0.8s 0.8s ease both;
  }

  @media (max-width: 900px) {
    .kpi-grid { grid-template-columns: repeat(2, 1fr); }
    .chart-grid-2 { grid-template-columns: 1fr; }
    .chart-grid-3 { grid-template-columns: 1fr; }
  }
</style>
</head>
<body>

<div class="container">

  <!-- HEADER -->
  <header>
    <div class="header-badge">🦠 Live Analytics · Apr–Jul 2020</div>
    <h1>COVID-19 <span>Analytics</span><br>Dashboard</h1>
    <p>WHO Global Dataset · 16.48M Cases · 195 Countries</p>
  </header>

  <!-- KPI CARDS -->
  <div class="kpi-grid">
    <div class="kpi-card" style="--accent-color: var(--confirmed);">
      <div class="kpi-label">Confirmed Cases</div>
      <div class="kpi-value" style="color:var(--confirmed);" data-target="16.48">0</div>
      <div class="kpi-sub">Million worldwide</div>
    </div>
    <div class="kpi-card" style="--accent-color: var(--deaths);">
      <div class="kpi-label">Total Deaths</div>
      <div class="kpi-value" style="color:var(--deaths);" data-target="654">0</div>
      <div class="kpi-sub">Thousand fatalities</div>
    </div>
    <div class="kpi-card" style="--accent-color: var(--recovered);">
      <div class="kpi-label">Recovered</div>
      <div class="kpi-value" style="color:var(--recovered);" data-target="9.47">0</div>
      <div class="kpi-sub">Million recovered</div>
    </div>
    <div class="kpi-card" style="--accent-color: var(--active);">
      <div class="kpi-label">Active Cases</div>
      <div class="kpi-value" style="color:var(--active);" data-target="6.36">0</div>
      <div class="kpi-sub">Million active (38.6%)</div>
    </div>
  </div>

  <!-- TOP 10 COUNTRIES + RECOVERY RATE -->
  <div class="section-header">
    <h2>Country Analysis</h2>
    <div class="section-line"></div>
  </div>

  <div class="chart-grid-2">
    <!-- Top 10 Countries -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--confirmed)"></div>
        Top 10 Countries — Confirmed Cases
      </div>
      <div class="bar-chart" id="top10-chart"></div>
    </div>

    <!-- Recovery Rate -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--recovered)"></div>
        Recovery Rate by Country (%)
      </div>
      <div class="bar-chart" id="recovery-chart"></div>
    </div>
  </div>

  <!-- GLOBAL TREND -->
  <div class="section-header">
    <h2>Global Trend — Apr to Jul 2020</h2>
    <div class="section-line"></div>
  </div>

  <div class="chart-full chart-card" style="padding-bottom:20px;">
    <div class="chart-title">
      <div class="chart-title-dot" style="background:var(--accent)"></div>
      90-Day Growth Trajectory
      <span style="margin-left:auto;display:flex;gap:20px;font-size:10px;">
        <span style="color:var(--confirmed)">■ Confirmed</span>
        <span style="color:var(--active)">■ Active</span>
        <span style="color:var(--recovered)">■ Recovered</span>
        <span style="color:var(--deaths)">■ Deaths</span>
      </span>
    </div>
    <svg id="trend-svg" class="trend-svg" height="220" viewBox="0 0 900 220"></svg>
  </div>

  <!-- REGIONAL DISTRIBUTION + PIE -->
  <div class="section-header">
    <h2>Regional Distribution</h2>
    <div class="section-line"></div>
  </div>

  <div class="chart-grid-2">
    <!-- Regional Bar -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--accent4)"></div>
        WHO Region — Cases / Deaths / Recovered
      </div>
      <div id="regional-chart"></div>
    </div>

    <!-- Pie Chart -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--accent)"></div>
        Confirmed Cases by WHO Region (%)
      </div>
      <div class="donut-wrapper">
        <svg id="donut-svg" class="donut-svg" width="180" height="180" viewBox="0 0 180 180"></svg>
        <div class="donut-legend" id="donut-legend"></div>
      </div>
    </div>
  </div>

  <!-- GROWTH + ACTIVE vs RECOVERED -->
  <div class="section-header">
    <h2>Outbreak Dynamics</h2>
    <div class="section-line"></div>
  </div>

  <div class="chart-grid-2">
    <!-- WoW Growth -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--deaths)"></div>
        Top 10 — Fastest Week-over-Week Growth
      </div>
      <div id="growth-chart"></div>
    </div>

    <!-- Active vs Recovered -->
    <div class="chart-card">
      <div class="chart-title">
        <div class="chart-title-dot" style="background:var(--active)"></div>
        Active vs Recovered Cases per Country
      </div>
      <div id="stacked-chart"></div>
    </div>
  </div>

  <!-- HEATMAP -->
  <div class="section-header">
    <h2>Country Metrics Heatmap — CFR%</h2>
    <div class="section-line"></div>
  </div>

  <div class="chart-full chart-card">
    <div class="chart-title">
      <div class="chart-title-dot" style="background:var(--deaths)"></div>
      Case Fatality Rate & Key Metrics — Top 10 Countries
    </div>
    <div style="overflow-x:auto;">
      <table class="heatmap-table" id="heatmap-table"></table>
    </div>
  </div>

  <!-- FOOTER -->
  <footer>
    <span>Data Source: WHO COVID-19 Global Dataset</span>
    <span>Apr–Jul 2020 · Built with Python · Pandas · Matplotlib</span>
  </footer>
</div>

<div class="tooltip" id="tooltip"></div>

<script>
// ============================================================
// DATA
// ============================================================
const top10 = [
  { country: 'USA', cases: 4200000, color: '#00e5ff' },
  { country: 'Brazil', cases: 2500000, color: '#00c4d8' },
  { country: 'India', cases: 1500000, color: '#00a3b0' },
  { country: 'Russia', cases: 820000, color: '#008a92' },
  { country: 'S. Africa', cases: 450000, color: '#007077' },
  { country: 'Mexico', cases: 390000, color: '#005a5f' },
  { country: 'Peru', cases: 370000, color: '#004448' },
  { country: 'Chile', cases: 340000, color: '#003b40' },
  { country: 'UK', cases: 300000, color: '#00e5ff' },
  { country: 'Iran', cases: 290000, color: '#00c4d8' },
];

const recoveryRates = [
  { country: 'Chile', rate: 95.2, color: '#39ff6e' },
  { country: 'Iran', rate: 85.1, color: '#2fd660' },
  { country: 'Russia', rate: 78.3, color: '#24ad4e' },
  { country: 'S. Africa', rate: 62.5, color: '#1a8a3c' },
  { country: 'Brazil', rate: 58.7, color: '#116a2b' },
  { country: 'India', rate: 55.2, color: '#0e5a24' },
  { country: 'Peru', rate: 49.8, color: '#0a4a1c' },
  { country: 'USA', rate: 45.1, color: '#073c16' },
  { country: 'Mexico', rate: 28.6, color: '#1a8a3c' },
  { country: 'UK', rate: 4.8, color: '#ff4b6e' },
];

const regions = [
  { name: 'Americas', cases: 8500, deaths: 320, recovered: 5200, color: '#00e5ff' },
  { name: 'Europe', cases: 3300, deaths: 210, recovered: 2400, color: '#ff4b6e' },
  { name: 'SE Asia', cases: 2000, deaths: 48, recovered: 1200, color: '#39ff6e' },
  { name: 'E. Medit.', cases: 1400, deaths: 37, recovered: 950, color: '#ffb800' },
  { name: 'Africa', cases: 870, deaths: 18, recovered: 480, color: '#a855f7' },
  { name: 'W. Pacific', cases: 310, deaths: 8, recovered: 250, color: '#fb923c' },
];

const regionPie = [
  { name: 'Americas', pct: 52.8, color: '#00e5ff' },
  { name: 'Europe', pct: 20.0, color: '#ff4b6e' },
  { name: 'SE Asia', pct: 11.5, color: '#39ff6e' },
  { name: 'E. Medit.', pct: 8.5, color: '#ffb800' },
  { name: 'Africa', pct: 5.3, color: '#a855f7' },
  { name: 'W. Pacific', pct: 1.9, color: '#fb923c' },
];

const growthData = [
  { country: 'Papua New Guinea', rate: 212, color: '#ff4b6e' },
  { country: 'Gambia', rate: 194, color: '#ff6b4b' },
  { country: 'Bahamas', rate: 178, color: '#ff8b4b' },
  { country: 'Zimbabwe', rate: 165, color: '#ffab4b' },
  { country: 'Libya', rate: 153, color: '#ffb800' },
  { country: 'Ethiopia', rate: 142, color: '#ffc800' },
  { country: 'Botswana', rate: 131, color: '#ffd800' },
  { country: 'Lesotho', rate: 121, color: '#ffe800' },
  { country: 'Suriname', rate: 112, color: '#fff200' },
  { country: 'Costa Rica', rate: 103, color: '#f0ff00' },
];

const activeVsRecovered = [
  { country: 'USA', active: 2150000, recovered: 1890000 },
  { country: 'Brazil', active: 900000, recovered: 1450000 },
  { country: 'India', active: 600000, recovered: 830000 },
  { country: 'Russia', active: 175000, recovered: 642000 },
  { country: 'UK', active: 285000, recovered: 14000 },
  { country: 'Mexico', active: 270000, recovered: 110000 },
  { country: 'S. Africa', active: 165000, recovered: 281000 },
  { country: 'Chile', active: 15000, recovered: 323000 },
];

const heatmapData = [
  { country: 'USA', confirmed: 4200000, deaths: 145000, recovered: 1890000, active: 2150000, cfr: 3.45 },
  { country: 'Brazil', confirmed: 2500000, deaths: 89000, recovered: 1450000, active: 900000, cfr: 3.56 },
  { country: 'India', confirmed: 1500000, deaths: 33000, recovered: 830000, active: 600000, cfr: 2.20 },
  { country: 'Russia', confirmed: 820000, deaths: 13400, recovered: 642000, active: 175000, cfr: 1.63 },
  { country: 'S. Africa', confirmed: 450000, deaths: 7000, recovered: 281000, active: 165000, cfr: 1.56 },
  { country: 'Mexico', confirmed: 390000, deaths: 43000, recovered: 110000, active: 270000, cfr: 11.14 },
  { country: 'Peru', confirmed: 370000, deaths: 17000, recovered: 184000, active: 170000, cfr: 4.59 },
  { country: 'Chile', confirmed: 340000, deaths: 9000, recovered: 323000, active: 15000, cfr: 2.65 },
  { country: 'UK', confirmed: 300000, deaths: 46000, recovered: 14000, active: 285000, cfr: 15.33 },
  { country: 'Iran', confirmed: 290000, deaths: 15600, recovered: 246000, active: 28000, cfr: 5.38 },
];

// Trend data (Apr 1 – Jul 1, monthly points)
const trendData = [
  { label: 'Apr 1', confirmed: 1000000, recovered: 210000, active: 760000, deaths: 30000 },
  { label: 'Apr 15', confirmed: 2100000, recovered: 480000, active: 1520000, deaths: 100000 },
  { label: 'May 1', confirmed: 3300000, recovered: 1000000, active: 2100000, deaths: 200000 },
  { label: 'May 15', confirmed: 4800000, recovered: 1800000, active: 2700000, deaths: 300000 },
  { label: 'Jun 1', confirmed: 6800000, recovered: 3200000, active: 3300000, deaths: 300000 },
  { label: 'Jun 15', confirmed: 9500000, recovered: 5100000, active: 4050000, deaths: 350000 },
  { label: 'Jul 1', confirmed: 16480000, recovered: 9470000, active: 6360000, deaths: 654000 },
];

// ============================================================
// RENDER FUNCTIONS
// ============================================================

function fmt(n) {
  if (n >= 1000000) return (n/1000000).toFixed(1)+'M';
  if (n >= 1000) return (n/1000).toFixed(0)+'K';
  return n;
}

// KPI counter animation
function animateCounters() {
  document.querySelectorAll('.kpi-value[data-target]').forEach(el => {
    const target = parseFloat(el.dataset.target);
    const suffix = el.dataset.target.includes('.') && target < 100 ? '' : '';
    let start = 0;
    const duration = 1500;
    const step = (timestamp) => {
      if (!start) start = timestamp;
      const p = Math.min((timestamp - start) / duration, 1);
      const ease = 1 - Math.pow(1 - p, 3);
      el.textContent = (ease * target).toFixed(target < 100 ? 2 : 0);
      if (p < 1) requestAnimationFrame(step);
      else el.textContent = target < 100 ? target.toFixed(2) : target.toFixed(0);
    };
    requestAnimationFrame(step);
  });
}

// Top 10 bar chart
function renderTop10() {
  const max = top10[0].cases;
  const el = document.getElementById('top10-chart');
  top10.forEach((d, i) => {
    const pct = (d.cases / max * 100).toFixed(1);
    el.innerHTML += `
      <div class="bar-row">
        <div class="bar-rank">${i+1}</div>
        <div class="bar-label">${d.country}</div>
        <div class="bar-track">
          <div class="bar-fill" style="background:linear-gradient(90deg,${d.color},${d.color}88);width:0%"
            data-width="${pct}" data-val="${fmt(d.cases)}"></div>
        </div>
      </div>`;
  });
}

// Recovery rate bar chart
function renderRecovery() {
  const el = document.getElementById('recovery-chart');
  recoveryRates.forEach((d, i) => {
    el.innerHTML += `
      <div class="bar-row">
        <div class="bar-rank">${i+1}</div>
        <div class="bar-label">${d.country}</div>
        <div class="bar-track">
          <div class="bar-fill" style="background:linear-gradient(90deg,${d.color},${d.color}88);width:0%"
            data-width="${d.rate}" data-val="${d.rate}%"></div>
        </div>
      </div>`;
  });
}

// Trend SVG
function renderTrend() {
  const svg = document.getElementById('trend-svg');
  const W = svg.parentElement.offsetWidth - 48;
  const H = 200;
  svg.setAttribute('viewBox', `0 0 ${W} ${H}`);

  const maxVal = 18000000;
  const pad = { left: 60, right: 20, top: 10, bottom: 30 };
  const iW = W - pad.left - pad.right;
  const iH = H - pad.top - pad.bottom;
  const n = trendData.length;

  const x = i => pad.left + (i / (n-1)) * iW;
  const y = v => pad.top + iH - (v / maxVal * iH);

  // Grid lines
  let gridLines = '';
  [0, 0.25, 0.5, 0.75, 1].forEach(pct => {
    const yv = pad.top + iH * (1 - pct);
    const val = pct * maxVal;
    gridLines += `<line x1="${pad.left}" x2="${W-pad.right}" y1="${yv}" y2="${yv}" stroke="rgba(255,255,255,0.05)" stroke-width="1"/>`;
    gridLines += `<text x="${pad.left - 6}" y="${yv + 4}" text-anchor="end" fill="#5a6a80" font-size="9" font-family="Space Mono,monospace">${fmt(val)}</text>`;
  });

  // X labels
  let xLabels = '';
  trendData.forEach((d, i) => {
    xLabels += `<text x="${x(i)}" y="${H - 6}" text-anchor="middle" fill="#5a6a80" font-size="9" font-family="Space Mono,monospace">${d.label}</text>`;
  });

  // Lines
  const lines = [
    { key: 'confirmed', color: '#00e5ff', width: 2.5 },
    { key: 'active', color: '#ffb800', width: 2 },
    { key: 'recovered', color: '#39ff6e', width: 2 },
    { key: 'deaths', color: '#ff4b6e', width: 1.5 },
  ];

  let paths = '';
  lines.forEach(({ key, color, width }) => {
    const pts = trendData.map((d, i) => `${i === 0 ? 'M' : 'L'}${x(i)},${y(d[key])}`).join(' ');
    paths += `<path d="${pts}" fill="none" stroke="${color}" stroke-width="${width}" stroke-linecap="round" stroke-linejoin="round" class="trend-line" style="animation-delay:${lines.indexOf(arguments[0]) * 0.2}s"/>`;
    // Dots
    trendData.forEach((d, i) => {
      paths += `<circle cx="${x(i)}" cy="${y(d[key])}" r="3" fill="${color}" opacity="0.8"/>`;
    });
  });

  // Area fill for confirmed
  const areaConf = trendData.map((d, i) => `${i === 0 ? 'M' : 'L'}${x(i)},${y(d.confirmed)}`).join(' ')
    + ` L${x(n-1)},${pad.top+iH} L${x(0)},${pad.top+iH} Z`;

  svg.innerHTML = `
    <defs>
      <linearGradient id="areaGrad" x1="0" x2="0" y1="0" y2="1">
        <stop offset="0%" stop-color="#00e5ff" stop-opacity="0.12"/>
        <stop offset="100%" stop-color="#00e5ff" stop-opacity="0"/>
      </linearGradient>
    </defs>
    ${gridLines}
    ${xLabels}
    <path d="${areaConf}" fill="url(#areaGrad)"/>
    ${paths}
  `;
}

// Regional chart
function renderRegional() {
  const el = document.getElementById('regional-chart');
  const maxC = Math.max(...regions.map(r => r.cases));
  regions.forEach(r => {
    el.innerHTML += `
      <div class="region-row">
        <div class="region-label">${r.name}</div>
        <div class="region-bars">
          <div class="region-mini-bar" style="background:${r.color};opacity:0.9" data-width="${r.cases/maxC*100}"></div>
          <div class="region-mini-bar" style="background:#ff4b6e" data-width="${r.deaths/maxC*100}"></div>
          <div class="region-mini-bar" style="background:#39ff6e" data-width="${r.recovered/maxC*100}"></div>
        </div>
        <div class="region-vals">${(r.cases/1000).toFixed(1)}K</div>
      </div>`;
  });
  // Legend
  el.innerHTML += `<div style="display:flex;gap:16px;margin-top:12px;font-family:Space Mono,monospace;font-size:10px;color:var(--muted)">
    <span style="color:#00e5ff">■ Cases</span>
    <span style="color:#ff4b6e">■ Deaths</span>
    <span style="color:#39ff6e">■ Recovered</span>
  </div>`;
}

// Donut chart
function renderDonut() {
  const svg = document.getElementById('donut-svg');
  const legend = document.getElementById('donut-legend');
  const cx = 90, cy = 90, r = 70, inner = 44;
  let angle = -Math.PI / 2;
  let paths = '';

  regionPie.forEach(d => {
    const sweep = (d.pct / 100) * Math.PI * 2;
    const x1 = cx + r * Math.cos(angle);
    const y1 = cy + r * Math.sin(angle);
    const x2 = cx + r * Math.cos(angle + sweep);
    const y2 = cy + r * Math.sin(angle + sweep);
    const xi1 = cx + inner * Math.cos(angle);
    const yi1 = cy + inner * Math.sin(angle);
    const xi2 = cx + inner * Math.cos(angle + sweep);
    const yi2 = cy + inner * Math.sin(angle + sweep);
    const large = sweep > Math.PI ? 1 : 0;

    paths += `<path d="M${xi1},${yi1} L${x1},${y1} A${r},${r} 0 ${large} 1 ${x2},${y2} L${xi2},${yi2} A${inner},${inner} 0 ${large} 0 ${xi1},${yi1} Z"
      fill="${d.color}" opacity="0.85" class="donut-seg" style="transition:opacity 0.2s;"
      onmouseover="this.style.opacity=1" onmouseout="this.style.opacity=0.85"/>`;

    legend.innerHTML += `
      <div class="legend-item">
        <div class="legend-dot" style="background:${d.color}"></div>
        <span class="legend-name">${d.name}</span>
        <span class="legend-pct">${d.pct}%</span>
      </div>`;

    angle += sweep;
  });

  svg.innerHTML = paths + `
    <text x="90" y="85" text-anchor="middle" fill="#e8edf5" font-size="14" font-weight="700" font-family="Syne,sans-serif">16.5M</text>
    <text x="90" y="100" text-anchor="middle" fill="#5a6a80" font-size="9" font-family="Space Mono,monospace">TOTAL</text>`;
}

// WoW Growth chart
function renderGrowth() {
  const el = document.getElementById('growth-chart');
  const max = growthData[0].rate;
  growthData.forEach((d, i) => {
    el.innerHTML += `
      <div class="growth-bar-row">
        <div class="growth-label">${d.country}</div>
        <div class="growth-track">
          <div class="growth-fill" data-width="${d.rate/max*100}" data-val="${d.rate}%"
            style="background:linear-gradient(90deg,${d.color},${d.color}88)"></div>
        </div>
      </div>`;
  });
}

// Active vs Recovered stacked bar
function renderStacked() {
  const el = document.getElementById('stacked-chart');
  const maxTotal = Math.max(...activeVsRecovered.map(d => d.active + d.recovered));
  activeVsRecovered.forEach(d => {
    const total = d.active + d.recovered;
    const recPct = (d.recovered / total * 100).toFixed(1);
    const actPct = (d.active / total * 100).toFixed(1);
    el.innerHTML += `
      <div class="stacked-row">
        <div class="stacked-label">${d.country}</div>
        <div class="stacked-track" style="width:${(total/maxTotal*100).toFixed(1)}%">
          <div class="stacked-seg" style="background:#39ff6e" data-width="${recPct}"></div>
          <div class="stacked-seg" style="background:#ffb800" data-width="${actPct}"></div>
        </div>
      </div>`;
  });
  el.innerHTML += `<div style="display:flex;gap:16px;margin-top:12px;font-family:Space Mono,monospace;font-size:10px;color:var(--muted)">
    <span style="color:#39ff6e">■ Recovered</span>
    <span style="color:#ffb800">■ Active</span>
  </div>`;
}

// Heatmap
function renderHeatmap() {
  const table = document.getElementById('heatmap-table');
  const maxConf = Math.max(...heatmapData.map(d => d.confirmed));
  const maxDeath = Math.max(...heatmapData.map(d => d.deaths));
  const maxCFR = Math.max(...heatmapData.map(d => d.cfr));

  table.innerHTML = `<thead><tr>
    <th>Country</th>
    <th>Confirmed</th>
    <th>Deaths</th>
    <th>Recovered</th>
    <th>Active</th>
    <th>CFR %</th>
  </tr></thead><tbody></tbody>`;

  const tbody = table.querySelector('tbody');
  heatmapData.forEach(d => {
    const cfrIntensity = d.cfr / maxCFR;
    const r = Math.round(255 * cfrIntensity);
    const g = Math.round(75 * (1 - cfrIntensity));
    const cfrColor = `rgb(${r},${g},80)`;
    const confIntensity = d.confirmed / maxConf;
    const ci = Math.round(confIntensity * 100);

    tbody.innerHTML += `<tr>
      <td style="font-weight:700;color:var(--text)">${d.country}</td>
      <td><span class="heat-cell" style="background:rgba(0,229,255,${confIntensity * 0.35});color:#e8edf5">${fmt(d.confirmed)}</span></td>
      <td><span class="heat-cell" style="background:rgba(255,75,110,${d.deaths/maxDeath*0.5});color:#e8edf5">${fmt(d.deaths)}</span></td>
      <td><span class="heat-cell" style="background:rgba(57,255,110,0.12);color:#39ff6e">${fmt(d.recovered)}</span></td>
      <td><span class="heat-cell" style="background:rgba(255,184,0,0.12);color:#ffb800">${fmt(d.active)}</span></td>
      <td><span class="heat-cell" style="background:${cfrColor}22;color:${cfrColor};border:1px solid ${cfrColor}44;font-weight:800">${d.cfr}%</span></td>
    </tr>`;
  });
}

// Animate bars after render
function animateBars() {
  setTimeout(() => {
    document.querySelectorAll('.bar-fill[data-width]').forEach(el => {
      el.style.width = el.dataset.width + '%';
    });
    document.querySelectorAll('.stacked-seg[data-width]').forEach(el => {
      el.style.width = el.dataset.width + '%';
    });
    document.querySelectorAll('.growth-fill[data-width]').forEach(el => {
      el.style.width = el.dataset.width + '%';
    });
    document.querySelectorAll('.region-mini-bar[data-width]').forEach(el => {
      el.style.width = el.dataset.width + '%';
    });
  }, 300);
}

// ============================================================
// INIT
// ============================================================
window.addEventListener('load', () => {
  renderTop10();
  renderRecovery();
  renderTrend();
  renderRegional();
  renderDonut();
  renderGrowth();
  renderStacked();
  renderHeatmap();
  animateBars();
  animateCounters();
});
</script>
</body>
</html>
