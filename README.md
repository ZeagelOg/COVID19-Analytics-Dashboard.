import { useState, useEffect, useRef } from "react";

// ── DATA ──────────────────────────────────────────────────────
const top10 = [
  { c: "USA", v: 4200000 }, { c: "Brazil", v: 2500000 }, { c: "India", v: 1500000 },
  { c: "Russia", v: 820000 }, { c: "S. Africa", v: 450000 }, { c: "Mexico", v: 390000 },
  { c: "Peru", v: 370000 }, { c: "Chile", v: 340000 }, { c: "UK", v: 300000 }, { c: "Iran", v: 290000 },
];
const recovery = [
  { c: "Chile", r: 95.2 }, { c: "Iran", r: 85.1 }, { c: "Russia", r: 78.3 },
  { c: "S. Africa", r: 62.5 }, { c: "Brazil", r: 58.7 }, { c: "India", r: 55.2 },
  { c: "Peru", r: 49.8 }, { c: "USA", r: 45.1 }, { c: "Mexico", r: 28.6 }, { c: "UK", r: 4.8 },
];
const trendData = [
  { l: "Apr 1",  cf: 1000000,  rc: 210000,  ac: 760000,  dt: 30000 },
  { l: "Apr 15", cf: 2100000,  rc: 480000,  ac: 1520000, dt: 100000 },
  { l: "May 1",  cf: 3300000,  rc: 1000000, ac: 2100000, dt: 200000 },
  { l: "May 15", cf: 4800000,  rc: 1800000, ac: 2700000, dt: 300000 },
  { l: "Jun 1",  cf: 6800000,  rc: 3200000, ac: 3300000, dt: 300000 },
  { l: "Jun 15", cf: 9500000,  rc: 5100000, ac: 4050000, dt: 350000 },
  { l: "Jul 1",  cf: 16480000, rc: 9470000, ac: 6360000, dt: 654000 },
];
const regions = [
  { n: "Americas",   cases: 8500, deaths: 320, rec: 5200 },
  { n: "Europe",     cases: 3300, deaths: 210, rec: 2400 },
  { n: "SE Asia",    cases: 2000, deaths: 48,  rec: 1200 },
  { n: "E. Medit.",  cases: 1400, deaths: 37,  rec: 950  },
  { n: "Africa",     cases: 870,  deaths: 18,  rec: 480  },
  { n: "W. Pacific", cases: 310,  deaths: 8,   rec: 250  },
];
const pieData = [
  { n: "Americas",   p: 52.8, col: "#0969da" },
  { n: "Europe",     p: 20.0, col: "#cf222e" },
  { n: "SE Asia",    p: 11.5, col: "#1a7f37" },
  { n: "E. Medit.",  p: 8.5,  col: "#9a6700" },
  { n: "Africa",     p: 5.3,  col: "#8250df" },
  { n: "W. Pacific", p: 1.9,  col: "#0550ae" },
];
const growth = [
  { c: "Papua New Guinea", r: 212 }, { c: "Gambia",     r: 194 },
  { c: "Bahamas",          r: 178 }, { c: "Zimbabwe",   r: 165 },
  { c: "Libya",            r: 153 }, { c: "Ethiopia",   r: 142 },
  { c: "Botswana",         r: 131 }, { c: "Lesotho",    r: 121 },
  { c: "Suriname",         r: 112 }, { c: "Costa Rica", r: 103 },
];
const stackedData = [
  { c: "USA",      ac: 2150000, rc: 1890000 }, { c: "Brazil",   ac: 900000,  rc: 1450000 },
  { c: "India",    ac: 600000,  rc: 830000  }, { c: "Russia",   ac: 175000,  rc: 642000  },
  { c: "UK",       ac: 285000,  rc: 14000   }, { c: "Mexico",   ac: 270000,  rc: 110000  },
  { c: "S.Africa", ac: 165000,  rc: 281000  }, { c: "Chile",    ac: 15000,   rc: 323000  },
];
const heatData = [
  { c: "USA",      cf: 4200000, dt: 145000, rc: 1890000, ac: 2150000, cfr: 3.45  },
  { c: "Brazil",   cf: 2500000, dt: 89000,  rc: 1450000, ac: 900000,  cfr: 3.56  },
  { c: "India",    cf: 1500000, dt: 33000,  rc: 830000,  ac: 600000,  cfr: 2.20  },
  { c: "Russia",   cf: 820000,  dt: 13400,  rc: 642000,  ac: 175000,  cfr: 1.63  },
  { c: "S.Africa", cf: 450000,  dt: 7000,   rc: 281000,  ac: 165000,  cfr: 1.56  },
  { c: "Mexico",   cf: 390000,  dt: 43000,  rc: 110000,  ac: 270000,  cfr: 11.14 },
  { c: "Peru",     cf: 370000,  dt: 17000,  rc: 184000,  ac: 170000,  cfr: 4.59  },
  { c: "Chile",    cf: 340000,  dt: 9000,   rc: 323000,  ac: 15000,   cfr: 2.65  },
  { c: "UK",       cf: 300000,  dt: 46000,  rc: 14000,   ac: 285000,  cfr: 15.33 },
  { c: "Iran",     cf: 290000,  dt: 15600,  rc: 246000,  ac: 28000,   cfr: 5.38  },
];

// ── HELPERS ───────────────────────────────────────────────────
function fmt(n) {
  if (n >= 1000000) return (n / 1e6).toFixed(1) + "M";
  if (n >= 1000)    return (n / 1000).toFixed(0) + "K";
  return String(n);
}

function useInView(ref) {
  const [visible, setVisible] = useState(false);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => {
      if (e.isIntersecting) { setVisible(true); obs.disconnect(); }
    }, { threshold: 0.15 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  return visible;
}

function useCounter(target, dec) {
  const [val, setVal] = useState(0);
  useEffect(() => {
    let start = null;
    const tick = (ts) => {
      if (!start) start = ts;
      const p = Math.min((ts - start) / 1600, 1);
      const e = 1 - Math.pow(1 - p, 3);
      setVal(parseFloat((e * target).toFixed(dec)));
      if (p < 1) requestAnimationFrame(tick);
    };
    requestAnimationFrame(tick);
  }, [target]);
  return val.toFixed(dec);
}

// ── SHARED CSS VALUES ─────────────────────────────────────────
const BORDER = "1px solid #d1d9e0";
const BG_SUBTLE = "#f6f8fa";
const TEXT = "#1f2328";
const MUTED = "#656d76";
const MONO = "'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, monospace";
const SANS = "-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif";

// ── SIMPLE BAR ROW ────────────────────────────────────────────
function BarRow({ rank, label, pct, color, valLabel, animated }) {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 7 }}>
      <span style={{ width: 18, textAlign: "center", fontSize: 11, color: MUTED, flexShrink: 0, fontFamily: MONO }}>{rank}</span>
      <span style={{ width: 84, textAlign: "right", fontSize: 11, color: TEXT, flexShrink: 0, fontFamily: MONO }}>{label}</span>
      <div style={{ flex: 1, height: 20, background: BG_SUBTLE, border: BORDER, borderRadius: 3, overflow: "hidden" }}>
        <div style={{
          width: animated ? pct + "%" : "0%",
          height: "100%",
          background: color,
          borderRadius: 2,
          transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)",
          position: "relative",
          display: "flex",
          alignItems: "center",
          justifyContent: "flex-end",
          paddingRight: 6,
        }}>
          <span style={{ fontSize: 10, fontWeight: 700, color: "#fff", fontFamily: MONO, whiteSpace: "nowrap" }}>{valLabel}</span>
        </div>
      </div>
    </div>
  );
}

// ── BAR CHART ─────────────────────────────────────────────────
function BarChart({ data, maxVal, colorFn, valFn }) {
  const ref = useRef();
  const animated = useInView(ref);
  return (
    <div ref={ref}>
      {data.map((d, i) => (
        <BarRow
          key={i}
          rank={i + 1}
          label={d.c}
          pct={((d.v ?? d.r) / maxVal) * 100}
          color={colorFn(d)}
          valLabel={valFn(d)}
          animated={animated}
        />
      ))}
    </div>
  );
}

// ── TREND CHART ───────────────────────────────────────────────
function TrendChart() {
  const ref = useRef();
  const animated = useInView(ref);
  const W = 720, H = 190, pl = 56, pr = 12, pt = 10, pb = 26;
  const iW = W - pl - pr, iH = H - pt - pb;
  const maxV = 18000000;
  const n = trendData.length;
  const X = (i) => pl + (i / (n - 1)) * iW;
  const Y = (v) => pt + iH - (v / maxV) * iH;
  const series = [
    { key: "cf", col: "#0969da", w: 2.5 },
    { key: "ac", col: "#9a6700", w: 2 },
    { key: "rc", col: "#1a7f37", w: 2 },
    { key: "dt", col: "#cf222e", w: 1.5 },
  ];
  const areaPath = trendData.map((d, i) => `${i === 0 ? "M" : "L"}${X(i)},${Y(d.cf)}`).join(" ")
    + ` L${X(n - 1)},${pt + iH} L${X(0)},${pt + iH} Z`;

  return (
    <div ref={ref}>
      <svg viewBox={`0 0 ${W} ${H}`} style={{ width: "100%", display: "block" }}>
        <defs>
          <linearGradient id="cfGrad" x1="0" x2="0" y1="0" y2="1">
            <stop offset="0%" stopColor="#0969da" stopOpacity="0.1" />
            <stop offset="100%" stopColor="#0969da" stopOpacity="0" />
          </linearGradient>
        </defs>
        {[0, 0.25, 0.5, 0.75, 1].map((p) => (
          <g key={p}>
            <line x1={pl} x2={W - pr} y1={pt + iH * (1 - p)} y2={pt + iH * (1 - p)} stroke="#d1d9e0" strokeWidth="1" />
            <text x={pl - 4} y={pt + iH * (1 - p) + 4} textAnchor="end" fill={MUTED} fontSize="9" fontFamily="monospace">{fmt(p * maxV)}</text>
          </g>
        ))}
        {trendData.map((d, i) => (
          <text key={i} x={X(i)} y={H - 4} textAnchor="middle" fill={MUTED} fontSize="9" fontFamily="monospace">{d.l}</text>
        ))}
        <path d={areaPath} fill="url(#cfGrad)" />
        {series.map((s) => {
          const d = trendData.map((p, i) => `${i === 0 ? "M" : "L"}${X(i)},${Y(p[s.key])}`).join(" ");
          return (
            <g key={s.key}>
              <path
                d={d} fill="none" stroke={s.col} strokeWidth={s.w}
                strokeLinecap="round" strokeLinejoin="round"
                strokeDasharray="2000"
                strokeDashoffset={animated ? "0" : "2000"}
                style={{ transition: "stroke-dashoffset 1.8s ease" }}
              />
              {trendData.map((p, i) => <circle key={i} cx={X(i)} cy={Y(p[s.key])} r="2.8" fill={s.col} />)}
            </g>
          );
        })}
      </svg>
      <div style={{ display: "flex", gap: 16, marginTop: 10, paddingTop: 10, borderTop: BORDER, flexWrap: "wrap" }}>
        {[["#0969da","Confirmed"],["#9a6700","Active"],["#1a7f37","Recovered"],["#cf222e","Deaths"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: MUTED }}>
            <div style={{ width: 10, height: 6, background: col, borderRadius: 1 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ── DONUT ─────────────────────────────────────────────────────
function DonutChart() {
  const cx = 80, cy = 80, R = 68, r = 44;
  let a = -Math.PI / 2;
  const slices = pieData.map((d) => {
    const sw = (d.p / 100) * Math.PI * 2;
    const cos1 = Math.cos(a), sin1 = Math.sin(a);
    const cos2 = Math.cos(a + sw), sin2 = Math.sin(a + sw);
    const path = [
      `M${cx + r * cos1},${cy + r * sin1}`,
      `L${cx + R * cos1},${cy + R * sin1}`,
      `A${R},${R} 0 ${sw > Math.PI ? 1 : 0} 1 ${cx + R * cos2},${cy + R * sin2}`,
      `L${cx + r * cos2},${cy + r * sin2}`,
      `A${r},${r} 0 ${sw > Math.PI ? 1 : 0} 0 ${cx + r * cos1},${cy + r * sin1}Z`,
    ].join(" ");
    a += sw;
    return { ...d, path };
  });
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 20 }}>
      <svg width="160" height="160" viewBox="0 0 160 160" style={{ flexShrink: 0 }}>
        {slices.map((s) => (
          <path key={s.n} d={s.path} fill={s.col} opacity="0.85" />
        ))}
        <text x="80" y="76" textAnchor="middle" fill={TEXT} fontSize="14" fontWeight="700" fontFamily={SANS}>16.5M</text>
        <text x="80" y="91" textAnchor="middle" fill={MUTED} fontSize="9" fontFamily="monospace">CASES</text>
      </svg>
      <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 7 }}>
        {pieData.map((d) => (
          <div key={d.n} style={{ display: "flex", alignItems: "center", gap: 8 }}>
            <div style={{ width: 9, height: 9, borderRadius: "50%", background: d.col, flexShrink: 0 }} />
            <span style={{ flex: 1, fontSize: 12, color: TEXT }}>{d.n}</span>
            <span style={{ fontSize: 11, color: MUTED, fontFamily: MONO }}>{d.p}%</span>
          </div>
        ))}
      </div>
    </div>
  );
}

// ── REGIONAL BARS ─────────────────────────────────────────────
function RegionalBars() {
  const ref = useRef();
  const animated = useInView(ref);
  const maxC = Math.max(...regions.map((r) => r.cases));
  return (
    <div ref={ref}>
      {regions.map((r) => (
        <div key={r.n} style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 10 }}>
          <span style={{ width: 88, fontSize: 11, color: TEXT, flexShrink: 0, fontFamily: MONO }}>{r.n}</span>
          <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 3 }}>
            {[[r.cases, "#0969da"], [r.deaths, "#cf222e"], [r.rec, "#1a7f37"]].map(([v, col], i) => (
              <div key={i} style={{ width: animated ? `${(v / maxC) * 100}%` : "0%", height: 7, background: col, borderRadius: 2, transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
            ))}
          </div>
          <span style={{ width: 44, textAlign: "right", fontSize: 11, color: MUTED, flexShrink: 0, fontFamily: MONO }}>{(r.cases / 1000).toFixed(1)}K</span>
        </div>
      ))}
      <div style={{ display: "flex", gap: 14, marginTop: 8, paddingTop: 8, borderTop: BORDER }}>
        {[["#0969da","Cases"],["#cf222e","Deaths"],["#1a7f37","Recovered"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: MUTED }}>
            <div style={{ width: 10, height: 6, background: col, borderRadius: 1 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ── GROWTH BARS ───────────────────────────────────────────────
function GrowthBars() {
  const ref = useRef();
  const animated = useInView(ref);
  const max = growth[0].r;
  return (
    <div ref={ref}>
      {growth.map((d) => (
        <div key={d.c} style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 7 }}>
          <span style={{ width: 130, fontSize: 11, color: TEXT, flexShrink: 0, fontFamily: MONO }}>{d.c}</span>
          <div style={{ flex: 1, height: 20, background: BG_SUBTLE, border: BORDER, borderRadius: 3, overflow: "hidden" }}>
            <div style={{
              width: animated ? `${(d.r / max) * 100}%` : "0%",
              height: "100%",
              background: "#cf222e",
              borderRadius: 2,
              transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)",
              display: "flex", alignItems: "center", justifyContent: "flex-end", paddingRight: 6,
            }}>
              <span style={{ fontSize: 10, fontWeight: 700, color: "#fff", fontFamily: MONO }}>{d.r}%</span>
            </div>
          </div>
        </div>
      ))}
    </div>
  );
}

// ── STACKED BARS ──────────────────────────────────────────────
function StackedBars() {
  const ref = useRef();
  const animated = useInView(ref);
  const maxT = Math.max(...stackedData.map((d) => d.ac + d.rc));
  return (
    <div ref={ref}>
      {stackedData.map((d) => {
        const t = d.ac + d.rc;
        const rp = (d.rc / t) * 100;
        const ap = (d.ac / t) * 100;
        const wp = (t / maxT) * 100;
        return (
          <div key={d.c} style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 7 }}>
            <span style={{ width: 66, textAlign: "right", fontSize: 11, color: TEXT, flexShrink: 0, fontFamily: MONO }}>{d.c}</span>
            <div style={{ width: `${wp}%`, height: 18, display: "flex", borderRadius: 3, overflow: "hidden", border: BORDER }}>
              <div style={{ width: animated ? `${rp}%` : "0%", height: "100%", background: "#1a7f37", transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
              <div style={{ width: animated ? `${ap}%` : "0%", height: "100%", background: "#9a6700", transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
            </div>
          </div>
        );
      })}
      <div style={{ display: "flex", gap: 14, marginTop: 8, paddingTop: 8, borderTop: BORDER }}>
        {[["#1a7f37","Recovered"],["#9a6700","Active"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: MUTED }}>
            <div style={{ width: 10, height: 6, background: col, borderRadius: 1 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ── HEATMAP ───────────────────────────────────────────────────
function HeatmapTable() {
  const maxCFR = Math.max(...heatData.map((d) => d.cfr));
  return (
    <div style={{ overflowX: "auto" }}>
      <table style={{ width: "100%", borderCollapse: "collapse", fontSize: 13 }}>
        <thead>
          <tr>
            {["Country","Confirmed","Deaths","Recovered","Active","CFR %"].map((h) => (
              <th key={h} style={{ background: BG_SUBTLE, border: BORDER, padding: "7px 12px", textAlign: h === "Country" ? "left" : "right", fontWeight: 600, fontSize: 12, color: TEXT }}>{h}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {heatData.map((d, i) => {
            const ci  = (d.cf / 4200000) * 0.4;
            const di  = (d.dt / 145000)  * 0.4;
            const cfrI = d.cfr / maxCFR;
            const cfrCol = cfrI > 0.6 ? "#cf222e" : cfrI > 0.3 ? "#9a6700" : "#1a7f37";
            const Cell = ({ bg, color, val }) => (
              <span style={{ display: "inline-block", padding: "1px 6px", borderRadius: 3, fontSize: 11, fontWeight: 700, fontFamily: MONO, background: bg, color, minWidth: 56, textAlign: "right" }}>{val}</span>
            );
            return (
              <tr key={d.c} style={{ background: i % 2 === 1 ? BG_SUBTLE : "#fff" }}>
                <td style={{ border: BORDER, padding: "6px 12px", fontFamily: MONO, fontSize: 12, fontWeight: 600 }}>{d.c}</td>
                <td style={{ border: BORDER, padding: "6px 12px", textAlign: "right" }}><Cell bg={`rgba(9,105,218,${ci})`}   color={TEXT}      val={fmt(d.cf)} /></td>
                <td style={{ border: BORDER, padding: "6px 12px", textAlign: "right" }}><Cell bg={`rgba(207,34,46,${di})`}   color={di > 0.2 ? "#fff" : TEXT} val={fmt(d.dt)} /></td>
                <td style={{ border: BORDER, padding: "6px 12px", textAlign: "right" }}><Cell bg="rgba(26,127,55,0.1)"       color="#1a7f37"   val={fmt(d.rc)} /></td>
                <td style={{ border: BORDER, padding: "6px 12px", textAlign: "right" }}><Cell bg="rgba(154,103,0,0.1)"       color="#9a6700"   val={fmt(d.ac)} /></td>
                <td style={{ border: BORDER, padding: "6px 12px", textAlign: "right" }}>
                  <span style={{ display: "inline-block", padding: "1px 6px", borderRadius: 3, fontSize: 11, fontWeight: 700, fontFamily: MONO, background: `${cfrCol}18`, color: cfrCol, border: `1px solid ${cfrCol}44`, minWidth: 44, textAlign: "right" }}>{d.cfr}%</span>
                </td>
              </tr>
            );
          })}
        </tbody>
      </table>
    </div>
  );
}

// ── FILE TREE ─────────────────────────────────────────────────
const treeFiles = [
  { icon: "📓", name: "COVID19_Analytics_Dashboard.ipynb", color: "#0969da", desc: "Main analysis notebook" },
  { icon: "🖼️", name: "01_kpi_cards.png",              color: MUTED, desc: "Global KPI summary" },
  { icon: "🖼️", name: "02_top10_countries.png",        color: MUTED, desc: "Top 10 countries bar chart" },
  { icon: "🖼️", name: "03_regional_distribution.png",  color: MUTED, desc: "Regional grouped bar chart" },
  { icon: "🖼️", name: "04_recovery_rate.png",          color: MUTED, desc: "Recovery rate by country" },
  { icon: "🖼️", name: "05_global_trend.png",           color: MUTED, desc: "90-day global growth trends" },
  { icon: "🖼️", name: "05_timeseries.png",             color: MUTED, desc: "Time series Apr–Jun 2020" },
  { icon: "🖼️", name: "06_regional_pie.png",           color: MUTED, desc: "WHO region pie chart" },
  { icon: "🖼️", name: "07_wow_growth.png",             color: MUTED, desc: "Week-over-week growth leaders" },
  { icon: "🖼️", name: "08_active_vs_recovered.png",    color: MUTED, desc: "Active vs Recovered stacked bar" },
  { icon: "🖼️", name: "09_heatmap.png",                color: MUTED, desc: "Country metrics heatmap (CFR%)" },
  { icon: "📋", name: "README.md",                      color: "#1a7f37", desc: "Documentation and insights" },
  { icon: "📄", name: "LICENSE",                        color: MUTED, desc: "MIT License" },
];

function FileTree() {
  return (
    <div style={{ background: BG_SUBTLE, border: BORDER, borderRadius: 6, padding: "16px 20px", fontFamily: MONO, fontSize: 13, lineHeight: 2 }}>
      <div style={{ fontWeight: 700, marginBottom: 4, color: TEXT }}>📁 COVID19-Analytics-Dashboard/</div>
      <div style={{ paddingLeft: 16 }}>
        {treeFiles.map((f, i) => (
          <div key={f.name} style={{ display: "flex", alignItems: "center", gap: 6 }}>
            <span style={{ color: MUTED, flexShrink: 0 }}>{i === treeFiles.length - 1 ? "└──" : "├──"}</span>
            <span>{f.icon}</span>
            <span style={{ color: f.color, flex: 1 }}>{f.name}</span>
            <span style={{ fontSize: 11, color: MUTED }}>{f.desc}</span>
          </div>
        ))}
      </div>
    </div>
  );
}

// ── CHART CARD WRAPPER ────────────────────────────────────────
function ChartCard({ title, caption, children }) {
  return (
    <div style={{ border: BORDER, borderRadius: 6, overflow: "hidden", marginBottom: 16 }}>
      <div style={{ padding: "18px 18px 14px", background: "#fff" }}>
        <div style={{ fontSize: 13, fontWeight: 600, color: TEXT, marginBottom: 14 }}>{title}</div>
        {children}
      </div>
      <div style={{ background: BG_SUBTLE, borderTop: BORDER, padding: "7px 14px", fontSize: 13, color: MUTED, fontStyle: "italic" }}>{caption}</div>
    </div>
  );
}

// ── PLATFORM ROW ──────────────────────────────────────────────
function PlatformRow({ icon, name, desc, tags, dotColor, linkText, href }) {
  return (
    <div style={{ display: "flex", alignItems: "flex-start", gap: 12, border: BORDER, borderRadius: 6, padding: "14px 16px", marginBottom: 10, background: "#fff" }}>
      <span style={{ fontSize: 22, marginTop: 2 }}>{icon}</span>
      <div style={{ flex: 1 }}>
        <div style={{ fontWeight: 600, fontSize: 14, color: TEXT }}>{name}</div>
        <div style={{ fontSize: 12, color: MUTED, marginTop: 2 }}>{desc}</div>
        <div style={{ display: "flex", gap: 5, marginTop: 6, flexWrap: "wrap" }}>
          {tags.map((t) => (
            <span key={t} style={{ fontSize: 11, background: BG_SUBTLE, border: BORDER, borderRadius: 12, padding: "1px 8px", fontFamily: MONO, color: TEXT }}>{t}</span>
          ))}
        </div>
      </div>
      <div style={{ display: "flex", alignItems: "center", gap: 6, flexShrink: 0, marginTop: 2 }}>
        <div style={{ width: 8, height: 8, borderRadius: "50%", background: dotColor }} />
        <a href={href} target="_blank" rel="noreferrer" style={{ color: "#0969da", textDecoration: "none", fontSize: 13 }}>{linkText} ↗</a>
      </div>
    </div>
  );
}

// ── KPI COUNTER ───────────────────────────────────────────────
function KpiBox({ target, dec, suffix, label }) {
  const val = useCounter(target, dec);
  return (
    <div style={{ border: BORDER, borderRadius: 6, padding: "14px 16px", textAlign: "center", background: "#fff" }}>
      <div style={{ fontSize: "1.55rem", fontWeight: 700, color: TEXT, lineHeight: 1 }}>{val}{suffix}</div>
      <div style={{ fontSize: 12, color: MUTED, marginTop: 4 }}>{label}</div>
    </div>
  );
}

// ── BADGE ─────────────────────────────────────────────────────
function Badge({ label, version, bg, text }) {
  return (
    <span style={{ display: "inline-flex", alignItems: "center", borderRadius: 4, overflow: "hidden", fontSize: 12, fontFamily: MONO, height: 20 }}>
      <span style={{ background: "#555", color: "#fff", padding: "0 7px", height: "100%", display: "flex", alignItems: "center" }}>{label}</span>
      <span style={{ background: bg, color: text, padding: "0 7px", height: "100%", display: "flex", alignItems: "center", fontWeight: 600 }}>{version}</span>
    </span>
  );
}

// ── CALLOUT ───────────────────────────────────────────────────
function Callout({ col, icon, tag, children }) {
  return (
    <div style={{ display: "flex", gap: 10, alignItems: "flex-start", borderLeft: `4px solid ${col}`, background: BG_SUBTLE, border: BORDER, borderLeftColor: col, borderRadius: "0 6px 6px 0", padding: "10px 14px", marginBottom: 10, fontSize: 14 }}>
      <span style={{ fontSize: 16, flexShrink: 0, marginTop: 2 }}>{icon}</span>
      <div style={{ color: TEXT, lineHeight: 1.6 }}>
        <span style={{ display: "block", fontFamily: MONO, fontSize: 10, color: col, letterSpacing: 1, textTransform: "uppercase", marginBottom: 3 }}>{tag}</span>
        {children}
      </div>
    </div>
  );
}

// ── DIVIDER ───────────────────────────────────────────────────
const HR = () => <hr style={{ border: "none", borderTop: BORDER, margin: "28px 0" }} />;
const H1 = ({ children }) => <h1 style={{ fontSize: "2em", fontWeight: 600, borderBottom: BORDER, paddingBottom: 10, margin: "24px 0 16px", lineHeight: 1.25, fontFamily: SANS, color: TEXT }}>{children}</h1>;
const H2 = ({ children }) => <h2 style={{ fontSize: "1.5em", fontWeight: 600, borderBottom: BORDER, paddingBottom: 8, margin: "32px 0 16px", lineHeight: 1.25, fontFamily: SANS, color: TEXT }}>{children}</h2>;
const Pre = ({ children }) => <pre style={{ background: BG_SUBTLE, border: BORDER, borderRadius: 6, padding: 16, overflowX: "auto", marginBottom: 16, fontFamily: MONO, fontSize: "87.5%", lineHeight: 1.7, color: TEXT }}>{children}</pre>;
const Code = ({ children }) => <code style={{ background: BG_SUBTLE, border: BORDER, borderRadius: 6, padding: "2px 6px", fontFamily: MONO, fontSize: "85%", color: TEXT }}>{children}</code>;

// ── MAIN APP ──────────────────────────────────────────────────
export default function App() {
  return (
    <div style={{ background: "#fff", minHeight: "100vh", fontFamily: SANS, color: TEXT, lineHeight: 1.6 }}>
      <div style={{ maxWidth: 860, margin: "0 auto", padding: "32px 24px 80px" }}>

        {/* ── TITLE ── */}
        <H1>🦠 COVID-19 Global Analytics Dashboard</H1>
        <blockquote style={{ borderLeft: "4px solid #d1d9e0", padding: "0 16px", color: MUTED, margin: "0 0 20px" }}>
          <p style={{ margin: 0 }}>Analyzing WHO global COVID-19 data covering Apr–Jul 2020 using Python, Power BI, Tableau, and Looker Studio to uncover case growth trends, regional distributions, recovery rates, and CFR outliers across 195 countries.</p>
        </blockquote>

        {/* ── BADGES ── */}
        <div style={{ display: "flex", flexWrap: "wrap", gap: 6, marginBottom: 20 }}>
          <Badge label="Python"       version="3.10"          bg="#3572A5" text="#fff" />
          <Badge label="Jupyter"      version="Notebook"      bg="#F37626" text="#fff" />
          <Badge label="Pandas"       version="Data Analysis" bg="#150458" text="#fff" />
          <Badge label="NumPy"        version="Numerical"     bg="#4DABCF" text="#fff" />
          <Badge label="Matplotlib"   version="Viz"           bg="#11557c" text="#fff" />
          <Badge label="Power BI"     version="Dashboard"     bg="#F2C811" text="#000" />
          <Badge label="Tableau"      version="Public"        bg="#E97627" text="#fff" />
          <Badge label="Looker Studio" version="Live"         bg="#4285F4" text="#fff" />
          <Badge label="License"      version="MIT"           bg="#1a7f37" text="#fff" />
        </div>

        <HR />

        {/* ── GLOBAL SUMMARY ── */}
        <H2>📌 Global Summary</H2>
        <div style={{ display: "grid", gridTemplateColumns: "repeat(4,1fr)", gap: 12, marginBottom: 24 }}>
          <KpiBox target={16.48} dec={2} suffix="M" label="Confirmed Cases" />
          <KpiBox target={654}   dec={0} suffix="K" label="Deaths" />
          <KpiBox target={9.47}  dec={2} suffix="M" label="Recovered" />
          <KpiBox target={6.36}  dec={2} suffix="M" label="Active Cases" />
        </div>

        <HR />

        {/* ── LIVE DASHBOARDS ── */}
        <H2>📊 Live Dashboards</H2>
        <PlatformRow icon="📊" name="Power BI"       desc="Multi-page interactive dashboard with slicers, drill-through, and R visuals"      tags={["DAX","DirectQuery","R Visuals","3 Pages"]}       dotColor="#9a6700" linkText="covid_dashboard.pbix"  href="https://app.powerbi.com" />
        <PlatformRow icon="🔭" name="Looker Studio"  desc="Real-time Google dashboard connected to BigQuery and Google Sheets"               tags={["BigQuery","Sheets","Geo Maps","Scorecards"]}     dotColor="#1a7f37" linkText="link coming soon"      href="https://lookerstudio.google.com" />
        <PlatformRow icon="📈" name="Tableau Public" desc="Published live workbook with LOD calculations, parameters, and filter actions"    tags={["LOD Calc","Parameters","Actions","Sets"]}        dotColor="#8250df" linkText="link coming soon"      href="https://public.tableau.com" />

        <HR />

        {/* ── PROJECT STRUCTURE ── */}
        <H2>🗂️ Project Structure</H2>
        <FileTree />

        <HR />

        {/* ── KEY INSIGHTS ── */}
        <H2>📈 Key Insights</H2>
        <Callout col="#0969da" icon="🌍" tag="Regional Dominance">The <strong>Americas account for 52.8%</strong> of all confirmed cases worldwide — nearly triple Europe's 20.0%. Over 70% of cases concentrated in just two regions.</Callout>
        <Callout col="#cf222e" icon="🇺🇸" tag="Country Leader">The <strong>US recorded ~4.2M confirmed cases</strong>, more than Brazil (~2.5M) and India (~1.5M) combined — 25.5% of the global total.</Callout>
        <Callout col="#1a7f37" icon="💊" tag="Recovery Leader"><strong>Chile tops all countries</strong> with a recovery rate exceeding <strong>95%</strong>, while the UK sits at under 5% — likely reflecting reporting differences.</Callout>
        <Callout col="#cf222e" icon="☠️" tag="CFR Outliers">The <strong>UK has a CFR of 15.33%</strong> and <strong>Mexico 11.14%</strong> — far above the global average, signalling severe healthcare strain.</Callout>
        <Callout col="#9a6700" icon="📈" tag="Exponential Growth">Cases grew from <strong>~1M to ~16M in just 90 days</strong> — a 16× increase. Active cases surged from May as new cases outpaced recoveries.</Callout>
        <Callout col="#cf222e" icon="🚀" tag="Fastest Surges"><strong>Papua New Guinea (212%) and Gambia (194%)</strong> led week-over-week growth, highlighting rapid spread in smaller nations with limited healthcare.</Callout>
        <Callout col="#9a6700" icon="🔥" tag="US Healthcare Pressure">The US had <strong>~2.15M active cases</strong> with recovered only slightly ahead, indicating enormous concurrent burden on healthcare systems.</Callout>
        <Callout col="#8250df" icon="🌐" tag="Rising Regions"><strong>South-East Asia &amp; Eastern Mediterranean</strong> together account for ~20% of global cases, showing spread well beyond initial Western epicentres.</Callout>

        <HR />

        {/* ── TECH STACK ── */}
        <H2>🛠️ Tech Stack</H2>
        <table style={{ width: "100%", borderCollapse: "collapse", marginBottom: 20, fontSize: 14 }}>
          <thead>
            <tr>
              <th style={{ background: BG_SUBTLE, border: BORDER, padding: "8px 16px", textAlign: "left", fontWeight: 600, color: TEXT }}>Category</th>
              <th style={{ background: BG_SUBTLE, border: BORDER, padding: "8px 16px", textAlign: "left", fontWeight: 600, color: TEXT }}>Tools</th>
            </tr>
          </thead>
          <tbody>
            {[
              ["Language",           "Python 3.x"],
              ["Data Analysis",      "Pandas, NumPy"],
              ["Visualization",      "Matplotlib, Seaborn"],
              ["Notebook Environment","Jupyter Notebook"],
              ["BI Platforms",       "Power BI, Tableau Public, Looker Studio"],
              ["Data Source",        "WHO COVID-19 Global Dataset"],
              ["Version Control",    "Git, GitHub"],
            ].map(([cat, tools], i) => (
              <tr key={cat} style={{ background: i % 2 === 1 ? BG_SUBTLE : "#fff" }}>
                <td style={{ border: BORDER, padding: "8px 16px" }}><strong>{cat}</strong></td>
                <td style={{ border: BORDER, padding: "8px 16px" }}>{tools}</td>
              </tr>
            ))}
          </tbody>
        </table>

        <HR />

        {/* ── GETTING STARTED ── */}
        <H2>🚀 Getting Started</H2>
        <p style={{ marginBottom: 8 }}>1. <strong>Clone the repository</strong></p>
        <Pre>{`git clone https://github.com/ZeagelOg/COVID19-Analytics-Dashboard.git\ncd COVID19-Analytics-Dashboard`}</Pre>
        <p style={{ marginBottom: 8 }}>2. <strong>Install dependencies</strong></p>
        <Pre>pip install pandas numpy matplotlib seaborn jupyter</Pre>
        <p style={{ marginBottom: 8 }}>3. <strong>Launch the notebook</strong></p>
        <Pre>jupyter notebook COVID19_Analytics_Dashboard.ipynb</Pre>
        <p style={{ marginBottom: 24 }}>4. <strong>Run all cells</strong> — Charts are auto-saved as <Code>.png</Code> files in the project root.</p>

        <HR />

        {/* ── CHARTS PREVIEW ── */}
        <H2>📁 Charts Preview</H2>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 0 }}>
          <ChartCard title="🏆 Top 10 Countries — Confirmed Cases" caption="02_top10_countries.png — Confirmed cases by country">
            <BarChart data={top10} maxVal={top10[0].v} colorFn={() => "#0969da"} valFn={(d) => fmt(d.v)} />
          </ChartCard>
          <ChartCard title="💚 Recovery Rate by Country (%)" caption="04_recovery_rate.png — Chile leads at 95%+, UK at bottom">
            <BarChart data={recovery} maxVal={100} colorFn={(d) => d.r < 10 ? "#cf222e" : d.r > 70 ? "#1a7f37" : "#0969da"} valFn={(d) => `${d.r}%`} />
          </ChartCard>
        </div>

        <ChartCard title="📈 Global COVID-19 Growth Trends — Apr to Jul 2020" caption="05_global_trend.png — 16× growth in 90 days from 1M to 16.5M">
          <TrendChart />
        </ChartCard>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 0 }}>
          <ChartCard title="🗺️ Regional Distribution of Cases" caption="03_regional_distribution.png">
            <RegionalBars />
          </ChartCard>
          <ChartCard title="🥧 Confirmed Cases by WHO Region" caption="06_regional_pie.png — Americas: 52.8%">
            <DonutChart />
          </ChartCard>
        </div>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 0 }}>
          <ChartCard title="🚀 Top 10 — Fastest WoW Case Growth" caption="07_wow_growth.png — Papua New Guinea & Gambia 200%+">
            <GrowthBars />
          </ChartCard>
          <ChartCard title="⚡ Active vs Recovered Cases per Country" caption="08_active_vs_recovered.png">
            <StackedBars />
          </ChartCard>
        </div>

        <ChartCard title="🔥 Country Metrics Heatmap — CFR%" caption="09_heatmap.png — UK (15.33%) and Mexico (11.14%) are stark CFR outliers">
          <HeatmapTable />
        </ChartCard>

        <HR />

        {/* ── DATA SOURCE ── */}
        <H2>📌 Data Source</H2>
        <p>COVID-19 data sourced from the <a href="https://covid19.who.int/data" style={{ color: "#0969da" }}>WHO COVID-19 Global Dataset</a> covering the Apr–Jul 2020 pandemic period.</p>

        <HR />

        {/* ── LICENSE ── */}
        <H2>📄 License</H2>
        <p>This project is open-source and available under the <a href="#" style={{ color: "#0969da" }}>MIT License</a>.</p>

      </div>
    </div>
  );
}
