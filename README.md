import { useState, useEffect, useRef } from "react";

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
  { n: "Americas", cases: 8500, deaths: 320, rec: 5200 },
  { n: "Europe", cases: 3300, deaths: 210, rec: 2400 },
  { n: "SE Asia", cases: 2000, deaths: 48, rec: 1200 },
  { n: "E. Medit.", cases: 1400, deaths: 37, rec: 950 },
  { n: "Africa", cases: 870, deaths: 18, rec: 480 },
  { n: "W. Pacific", cases: 310, deaths: 8, rec: 250 },
];
const pieData = [
  { n: "Americas", p: 52.8, col: "#0969da" },
  { n: "Europe", p: 20.0, col: "#cf222e" },
  { n: "SE Asia", p: 11.5, col: "#1a7f37" },
  { n: "E. Medit.", p: 8.5, col: "#9a6700" },
  { n: "Africa", p: 5.3, col: "#8250df" },
  { n: "W. Pacific", p: 1.9, col: "#0550ae" },
];
const growth = [
  { c: "Papua New Guinea", r: 212 }, { c: "Gambia", r: 194 }, { c: "Bahamas", r: 178 },
  { c: "Zimbabwe", r: 165 }, { c: "Libya", r: 153 }, { c: "Ethiopia", r: 142 },
  { c: "Botswana", r: 131 }, { c: "Lesotho", r: 121 }, { c: "Suriname", r: 112 }, { c: "Costa Rica", r: 103 },
];
const stackedData = [
  { c: "USA", ac: 2150000, rc: 1890000 }, { c: "Brazil", ac: 900000, rc: 1450000 },
  { c: "India", ac: 600000, rc: 830000 }, { c: "Russia", ac: 175000, rc: 642000 },
  { c: "UK", ac: 285000, rc: 14000 }, { c: "Mexico", ac: 270000, rc: 110000 },
  { c: "S.Africa", ac: 165000, rc: 281000 }, { c: "Chile", ac: 15000, rc: 323000 },
];
const heatData = [
  { c: "USA",     cf: 4200000, dt: 145000, rc: 1890000, ac: 2150000, cfr: 3.45 },
  { c: "Brazil",  cf: 2500000, dt: 89000,  rc: 1450000, ac: 900000,  cfr: 3.56 },
  { c: "India",   cf: 1500000, dt: 33000,  rc: 830000,  ac: 600000,  cfr: 2.20 },
  { c: "Russia",  cf: 820000,  dt: 13400,  rc: 642000,  ac: 175000,  cfr: 1.63 },
  { c: "S.Africa",cf: 450000,  dt: 7000,   rc: 281000,  ac: 165000,  cfr: 1.56 },
  { c: "Mexico",  cf: 390000,  dt: 43000,  rc: 110000,  ac: 270000,  cfr: 11.14 },
  { c: "Peru",    cf: 370000,  dt: 17000,  rc: 184000,  ac: 170000,  cfr: 4.59 },
  { c: "Chile",   cf: 340000,  dt: 9000,   rc: 323000,  ac: 15000,   cfr: 2.65 },
  { c: "UK",      cf: 300000,  dt: 46000,  rc: 14000,   ac: 285000,  cfr: 15.33 },
  { c: "Iran",    cf: 290000,  dt: 15600,  rc: 246000,  ac: 28000,   cfr: 5.38 },
];

function fmt(n) {
  if (n >= 1000000) return (n / 1e6).toFixed(1) + "M";
  if (n >= 1000) return (n / 1000).toFixed(0) + "K";
  return n;
}

function useCounter(target, dec, duration = 1600) {
  const [val, setVal] = useState(0);
  useEffect(() => {
    let start = null;
    const step = (ts) => {
      if (!start) start = ts;
      const p = Math.min((ts - start) / duration, 1);
      const e = 1 - Math.pow(1 - p, 3);
      setVal(parseFloat((e * target).toFixed(dec)));
      if (p < 1) requestAnimationFrame(step);
    };
    requestAnimationFrame(step);
  }, []);
  return val.toFixed(dec);
}

function AnimBar({ pct, color, label, animated }) {
  return (
    <div style={{ width: animated ? `${pct}%` : "0%", height: "100%", background: color, borderRadius: 2, transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)", position: "relative" }}>
      <span style={{ position: "absolute", right: 5, top: "50%", transform: "translateY(-50%)", fontSize: 10, fontWeight: 700, color: "#fff", fontFamily: "monospace", whiteSpace: "nowrap" }}>{label}</span>
    </div>
  );
}

// ─── BAR CHART COMPONENT ───
function BarChart({ data, maxVal, colorFn, valFn }) {
  const [animated, setAnimated] = useState(false);
  const ref = useRef();
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setAnimated(true); obs.disconnect(); } }, { threshold: 0.2 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  return (
    <div ref={ref} style={{ display: "flex", flexDirection: "column", gap: 7 }}>
      {data.map((d, i) => (
        <div key={i} style={{ display: "flex", alignItems: "center", gap: 8 }}>
          <span style={{ width: 18, textAlign: "center", fontSize: 11, color: "#656d76", flexShrink: 0 }}>{i + 1}</span>
          <span style={{ width: 82, textAlign: "right", fontSize: 11, color: "#1f2328", flexShrink: 0, fontFamily: "monospace" }}>{d.c || d.country}</span>
          <div style={{ flex: 1, height: 20, background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 3, overflow: "hidden" }}>
            <AnimBar pct={(maxVal ? (d.v || d.r || d.rate) / maxVal : (d.v || d.r || d.rate) / 100) * 100} color={colorFn(d, i)} label={valFn(d)} animated={animated} />
          </div>
        </div>
      ))}
    </div>
  );
}

// ─── TREND SVG ───
function TrendChart() {
  const [animated, setAnimated] = useState(false);
  const ref = useRef();
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setTimeout(() => setAnimated(true), 100); obs.disconnect(); } }, { threshold: 0.2 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);

  const W = 760, H = 200, pl = 58, pr = 16, pt = 12, pb = 28;
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

  const areaD = trendData.map((d, i) => `${i === 0 ? "M" : "L"}${X(i)},${Y(d.cf)}`).join(" ") + ` L${X(n - 1)},${pt + iH} L${X(0)},${pt + iH} Z`;

  return (
    <div ref={ref}>
      <svg viewBox={`0 0 ${W} ${H}`} style={{ width: "100%", display: "block" }}>
        <defs>
          <linearGradient id="ag" x1="0" x2="0" y1="0" y2="1">
            <stop offset="0%" stopColor="#0969da" stopOpacity="0.12" />
            <stop offset="100%" stopColor="#0969da" stopOpacity="0.01" />
          </linearGradient>
        </defs>
        {[0, 0.25, 0.5, 0.75, 1].map((p) => (
          <g key={p}>
            <line x1={pl} x2={W - pr} y1={pt + iH * (1 - p)} y2={pt + iH * (1 - p)} stroke="#d1d9e0" strokeWidth="1" />
            <text x={pl - 5} y={pt + iH * (1 - p) + 4} textAnchor="end" fill="#656d76" fontSize="9" fontFamily="monospace">{fmt(p * maxV)}</text>
          </g>
        ))}
        {trendData.map((d, i) => (
          <text key={i} x={X(i)} y={H - 4} textAnchor="middle" fill="#656d76" fontSize="9" fontFamily="monospace">{d.l}</text>
        ))}
        <path d={areaD} fill="url(#ag)" />
        {series.map((s) => {
          const pts = trendData.map((d, i) => `${i === 0 ? "M" : "L"}${X(i)},${Y(d[s.key])}`).join(" ");
          const len = 2000;
          return (
            <g key={s.key}>
              <path d={pts} fill="none" stroke={s.col} strokeWidth={s.w} strokeLinecap="round" strokeLinejoin="round"
                strokeDasharray={len} strokeDashoffset={animated ? 0 : len}
                style={{ transition: "stroke-dashoffset 1.8s ease" }} />
              {trendData.map((d, i) => <circle key={i} cx={X(i)} cy={Y(d[s.key])} r="3" fill={s.col} />)}
            </g>
          );
        })}
      </svg>
      <div style={{ display: "flex", gap: 16, marginTop: 10, paddingTop: 10, borderTop: "1px solid #d1d9e0", flexWrap: "wrap" }}>
        {[["#0969da", "Confirmed"], ["#9a6700", "Active"], ["#1a7f37", "Recovered"], ["#cf222e", "Deaths"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: "#656d76" }}>
            <div style={{ width: 10, height: 7, background: col, borderRadius: 2 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── DONUT ───
function DonutChart() {
  const cx = 82.5, cy = 82.5, R = 72, r = 46;
  let a = -Math.PI / 2;
  const slices = pieData.map((d) => {
    const sw = (d.p / 100) * Math.PI * 2;
    const x1 = cx + R * Math.cos(a), y1 = cy + R * Math.sin(a);
    const x2 = cx + R * Math.cos(a + sw), y2 = cy + R * Math.sin(a + sw);
    const xi1 = cx + r * Math.cos(a), yi1 = cy + r * Math.sin(a);
    const xi2 = cx + r * Math.cos(a + sw), yi2 = cy + r * Math.sin(a + sw);
    const lg = sw > Math.PI ? 1 : 0;
    const path = `M${xi1},${yi1} L${x1},${y1} A${R},${R} 0 ${lg} 1 ${x2},${y2} L${xi2},${yi2} A${r},${r} 0 ${lg} 0 ${xi1},${yi1} Z`;
    a += sw;
    return { ...d, path };
  });
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 20 }}>
      <svg width="165" height="165" viewBox="0 0 165 165" style={{ flexShrink: 0 }}>
        {slices.map((s) => <path key={s.n} d={s.path} fill={s.col} opacity="0.85" />)}
        <text x="82.5" y="78" textAnchor="middle" fill="#1f2328" fontSize="14" fontWeight="700" fontFamily="sans-serif">16.5M</text>
        <text x="82.5" y="93" textAnchor="middle" fill="#656d76" fontSize="9" fontFamily="monospace">CASES</text>
      </svg>
      <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 7 }}>
        {pieData.map((d) => (
          <div key={d.n} style={{ display: "flex", alignItems: "center", gap: 8, fontSize: 12 }}>
            <div style={{ width: 9, height: 9, borderRadius: "50%", background: d.col, flexShrink: 0 }} />
            <span style={{ flex: 1, color: "#1f2328" }}>{d.n}</span>
            <span style={{ fontFamily: "monospace", fontSize: 11, color: "#656d76" }}>{d.p}%</span>
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── STACKED BAR ───
function StackedBars() {
  const [animated, setAnimated] = useState(false);
  const ref = useRef();
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setAnimated(true); obs.disconnect(); } }, { threshold: 0.2 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  const maxT = Math.max(...stackedData.map((d) => d.ac + d.rc));
  return (
    <div ref={ref} style={{ display: "flex", flexDirection: "column", gap: 7 }}>
      {stackedData.map((d) => {
        const t = d.ac + d.rc;
        const rp = (d.rc / t) * 100;
        const ap = (d.ac / t) * 100;
        const wp = (t / maxT) * 100;
        return (
          <div key={d.c} style={{ display: "flex", alignItems: "center", gap: 8 }}>
            <span style={{ width: 68, textAlign: "right", fontSize: 11, color: "#1f2328", flexShrink: 0, fontFamily: "monospace" }}>{d.c}</span>
            <div style={{ flex: 1, height: 18, display: "flex", borderRadius: 3, overflow: "hidden", border: "1px solid #d1d9e0", width: `${wp}%` }}>
              <div style={{ width: animated ? `${rp}%` : "0%", height: "100%", background: "#1a7f37", transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
              <div style={{ width: animated ? `${ap}%` : "0%", height: "100%", background: "#9a6700", transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
            </div>
          </div>
        );
      })}
      <div style={{ display: "flex", gap: 14, marginTop: 8, paddingTop: 8, borderTop: "1px solid #d1d9e0" }}>
        {[["#1a7f37", "Recovered"], ["#9a6700", "Active"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: "#656d76" }}>
            <div style={{ width: 10, height: 7, background: col, borderRadius: 2 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── REGIONAL BARS ───
function RegionalBars() {
  const [animated, setAnimated] = useState(false);
  const ref = useRef();
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setAnimated(true); obs.disconnect(); } }, { threshold: 0.2 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  const maxC = Math.max(...regions.map((r) => r.cases));
  return (
    <div ref={ref} style={{ display: "flex", flexDirection: "column", gap: 10 }}>
      {regions.map((r) => (
        <div key={r.n} style={{ display: "flex", alignItems: "center", gap: 8 }}>
          <span style={{ width: 90, fontSize: 11, color: "#1f2328", flexShrink: 0, fontFamily: "monospace" }}>{r.n}</span>
          <div style={{ flex: 1, display: "flex", flexDirection: "column", gap: 3 }}>
            {[[r.cases, "#0969da"], [r.deaths, "#cf222e"], [r.rec, "#1a7f37"]].map(([v, col], i) => (
              <div key={i} style={{ width: animated ? `${(v / maxC) * 100}%` : "0%", height: 7, background: col, borderRadius: 2, transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)" }} />
            ))}
          </div>
          <span style={{ width: 46, textAlign: "right", fontSize: 11, color: "#656d76", flexShrink: 0, fontFamily: "monospace" }}>{(r.cases / 1000).toFixed(1)}K</span>
        </div>
      ))}
      <div style={{ display: "flex", gap: 14, marginTop: 8, paddingTop: 8, borderTop: "1px solid #d1d9e0" }}>
        {[["#0969da", "Cases"], ["#cf222e", "Deaths"], ["#1a7f37", "Recovered"]].map(([col, lbl]) => (
          <div key={lbl} style={{ display: "flex", alignItems: "center", gap: 5, fontSize: 12, color: "#656d76" }}>
            <div style={{ width: 10, height: 7, background: col, borderRadius: 2 }} />{lbl}
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── GROWTH BARS ───
function GrowthBars() {
  const [animated, setAnimated] = useState(false);
  const ref = useRef();
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setAnimated(true); obs.disconnect(); } }, { threshold: 0.2 });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, []);
  const max = growth[0].r;
  return (
    <div ref={ref} style={{ display: "flex", flexDirection: "column", gap: 7 }}>
      {growth.map((d) => (
        <div key={d.c} style={{ display: "flex", alignItems: "center", gap: 8 }}>
          <span style={{ width: 132, fontSize: 11, color: "#1f2328", flexShrink: 0, fontFamily: "monospace" }}>{d.c}</span>
          <div style={{ flex: 1, height: 20, background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 3, overflow: "hidden", position: "relative" }}>
            <div style={{ width: animated ? `${(d.r / max) * 100}%` : "0%", height: "100%", background: "#cf222e", borderRadius: 2, transition: "width 1.4s cubic-bezier(0.16,1,0.3,1)", position: "relative" }}>
              <span style={{ position: "absolute", right: 5, top: "50%", transform: "translateY(-50%)", fontSize: 10, fontWeight: 700, color: "#fff", fontFamily: "monospace" }}>{d.r}%</span>
            </div>
          </div>
        </div>
      ))}
    </div>
  );
}

// ─── HEATMAP TABLE ───
function HeatmapTable() {
  const maxCFR = Math.max(...heatData.map((d) => d.cfr));
  return (
    <div style={{ overflowX: "auto" }}>
      <table style={{ width: "100%", borderCollapse: "collapse", fontSize: 13 }}>
        <thead>
          <tr>
            {["Country", "Confirmed", "Deaths", "Recovered", "Active", "CFR %"].map((h) => (
              <th key={h} style={{ background: "#f6f8fa", border: "1px solid #d1d9e0", padding: "7px 12px", textAlign: h === "Country" ? "left" : "right", fontWeight: 600, fontSize: 12, color: "#1f2328" }}>{h}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {heatData.map((d, i) => {
            const ci = d.cf / 4200000 * 0.4;
            const di = d.dt / 145000 * 0.4;
            const cfrI = d.cfr / maxCFR;
            const cfrCol = cfrI > 0.6 ? "#cf222e" : cfrI > 0.3 ? "#9a6700" : "#1a7f37";
            const cell = (bg, col, v) => (
              <span style={{ display: "inline-block", padding: "1px 6px", borderRadius: 3, fontSize: 11, fontWeight: 700, fontFamily: "monospace", background: bg, color: col, minWidth: 58, textAlign: "right" }}>{v}</span>
            );
            return (
              <tr key={d.c} style={{ background: i % 2 === 1 ? "#f6f8fa" : "#fff" }}>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", fontFamily: "monospace", fontSize: 12, fontWeight: 600 }}>{d.c}</td>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", textAlign: "right" }}>{cell(`rgba(9,105,218,${ci})`, "#1f2328", fmt(d.cf))}</td>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", textAlign: "right" }}>{cell(`rgba(207,34,46,${di})`, di > 0.2 ? "#fff" : "#1f2328", fmt(d.dt))}</td>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", textAlign: "right" }}>{cell("rgba(26,127,55,0.1)", "#1a7f37", fmt(d.rc))}</td>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", textAlign: "right" }}>{cell("rgba(154,103,0,0.1)", "#9a6700", fmt(d.ac))}</td>
                <td style={{ border: "1px solid #d1d9e0", padding: "6px 12px", textAlign: "right" }}>
                  <span style={{ display: "inline-block", padding: "1px 6px", borderRadius: 3, fontSize: 11, fontWeight: 700, fontFamily: "monospace", background: `${cfrCol}18`, color: cfrCol, border: `1px solid ${cfrCol}50`, minWidth: 46, textAlign: "right" }}>{d.cfr}%</span>
                </td>
              </tr>
            );
          })}
        </tbody>
      </table>
    </div>
  );
}

// ─── SHARED STYLES ───
const s = {
  page: { maxWidth: 860, margin: "0 auto", padding: "32px 24px 80px", background: "#fff", fontFamily: "-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif", fontSize: 16, color: "#1f2328", lineHeight: 1.6 },
  h1: { fontSize: "2em", fontWeight: 600, borderBottom: "1px solid #d1d9e0", paddingBottom: 10, margin: "24px 0 16px", lineHeight: 1.25 },
  h2: { fontSize: "1.5em", fontWeight: 600, borderBottom: "1px solid #d1d9e0", paddingBottom: 8, margin: "32px 0 16px", lineHeight: 1.25 },
  bq: { borderLeft: "4px solid #d1d9e0", padding: "0 16px", color: "#656d76", margin: "0 0 20px" },
  hr: { border: "none", borderTop: "1px solid #d1d9e0", margin: "28px 0" },
  code: { background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 6, padding: "2px 6px", fontFamily: "monospace", fontSize: "85%", color: "#1f2328" },
  pre: { background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 6, padding: 16, overflowX: "auto", marginBottom: 16, fontFamily: "monospace", fontSize: "87.5%", lineHeight: 1.7 },
  chartCard: { border: "1px solid #d1d9e0", borderRadius: 6, overflow: "hidden", marginBottom: 16 },
  chartInner: { padding: "18px 18px 12px", background: "#fff" },
  chartCaption: { background: "#f6f8fa", borderTop: "1px solid #d1d9e0", padding: "7px 14px", fontSize: 13, color: "#656d76", fontStyle: "italic" },
  chartTitle: { fontSize: 13, fontWeight: 600, color: "#1f2328", marginBottom: 14 },
  callout: (col) => ({ borderLeft: `4px solid ${col}`, background: "#f6f8fa", border: "1px solid #d1d9e0", borderLeftColor: col, borderRadius: "0 6px 6px 0", padding: "10px 14px", marginBottom: 10, fontSize: 14, display: "flex", gap: 10, alignItems: "flex-start" }),
};

function KpiCounter({ target, dec, suffix }) {
  const val = useCounter(target, dec);
  return <span style={{ fontSize: "1.6rem", fontWeight: 700, color: "#1f2328", lineHeight: 1 }}>{val}{suffix}</span>;
}

// ─── PLATFORM ROW ───
function PlatformRow({ icon, name, type, tags, statusColor, linkText, href }) {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 12, border: "1px solid #d1d9e0", borderRadius: 6, padding: "14px 16px", marginBottom: 10, background: "#fff" }}>
      <span style={{ fontSize: 22 }}>{icon}</span>
      <div style={{ flex: 1 }}>
        <div style={{ fontWeight: 600, fontSize: 14 }}>{name}</div>
        <div style={{ fontSize: 12, color: "#656d76", marginTop: 2 }}>{type}</div>
        <div style={{ display: "flex", gap: 5, marginTop: 5, flexWrap: "wrap" }}>
          {tags.map((t) => <span key={t} style={{ fontSize: 11, background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 12, padding: "1px 8px", fontFamily: "monospace" }}>{t}</span>)}
        </div>
      </div>
      <div style={{ display: "flex", alignItems: "center", gap: 6, flexShrink: 0 }}>
        <div style={{ width: 8, height: 8, borderRadius: "50%", background: statusColor }} />
        <a href={href} target="_blank" rel="noreferrer" style={{ color: "#0969da", textDecoration: "none", fontSize: 13 }}>{linkText} ↗</a>
      </div>
    </div>
  );
}

// ─── FILE TREE ───
function FileTree() {
  const files = [
    { icon: "📓", name: "COVID19_Analytics_Dashboard.ipynb", color: "#0969da", desc: "Main analysis notebook" },
    { icon: "🖼️", name: "01_kpi_cards.png", color: "#656d76", desc: "Global KPI summary" },
    { icon: "🖼️", name: "02_top10_countries.png", color: "#656d76", desc: "Top 10 countries bar chart" },
    { icon: "🖼️", name: "03_regional_distribution.png", color: "#656d76", desc: "Regional grouped bar chart" },
    { icon: "🖼️", name: "04_recovery_rate.png", color: "#656d76", desc: "Recovery rate by country" },
    { icon: "🖼️", name: "05_global_trend.png", color: "#656d76", desc: "90-day global growth trends" },
    { icon: "🖼️", name: "05_timeseries.png", color: "#656d76", desc: "Time series Apr–Jun 2020" },
    { icon: "🖼️", name: "06_regional_pie.png", color: "#656d76", desc: "WHO region pie chart" },
    { icon: "🖼️", name: "07_wow_growth.png", color: "#656d76", desc: "Week-over-week growth leaders" },
    { icon: "🖼️", name: "08_active_vs_recovered.png", color: "#656d76", desc: "Active vs Recovered stacked bar" },
    { icon: "🖼️", name: "09_heatmap.png", color: "#656d76", desc: "Country metrics heatmap (CFR%)" },
    { icon: "📋", name: "README.md", color: "#1a7f37", desc: "Documentation and insights" },
    { icon: "📄", name: "LICENSE", color: "#656d76", desc: "MIT License" },
  ];
  return (
    <div style={{ background: "#f6f8fa", border: "1px solid #d1d9e0", borderRadius: 6, padding: "16px 20px", fontFamily: "monospace", fontSize: 13, lineHeight: 2 }}>
      <div style={{ fontWeight: 700, marginBottom: 2 }}>📁 COVID19-Analytics-Dashboard/</div>
      <div style={{ paddingLeft: 16 }}>
        {files.map((f, i) => (
          <div key={f.name} style={{ display: "flex", alignItems: "center", gap: 6 }}>
            <span style={{ color: "#656d76" }}>{i === files.length - 1 ? "└──" : "├──"}</span>
            <span>{f.icon}</span>
            <span style={{ color: f.color, flex: 1 }}>{f.name}</span>
            <span style={{ fontSize: 11, color: "#656d76" }}>{f.desc}</span>
          </div>
        ))}
      </div>
    </div>
  );
}

// ─── INSIGHTS ───
const insights = [
  { col: "#0969da", icon: "🌍", tag: "Regional Dominance", text: <>The <strong>Americas account for 52.8%</strong> of all confirmed cases worldwide — nearly triple Europe's 20.0%. Over 70% of cases concentrated in just two regions.</> },
  { col: "#cf222e", icon: "🇺🇸", tag: "Country Leader", text: <>The <strong>US recorded ~4.2M confirmed cases</strong>, more than Brazil (~2.5M) and India (~1.5M) combined — 25.5% of the global total.</> },
  { col: "#1a7f37", icon: "💊", tag: "Recovery Leader", text: <><strong>Chile tops all countries</strong> with a recovery rate exceeding <strong>95%</strong>, while the UK sits at under 5% — likely reflecting reporting differences.</> },
  { col: "#cf222e", icon: "☠️", tag: "CFR Outliers", text: <>The <strong>UK has a CFR of 15.33%</strong> and <strong>Mexico 11.14%</strong> — far above the global average, signalling severe healthcare strain.</> },
  { col: "#9a6700", icon: "📈", tag: "Exponential Growth", text: <>Cases grew from <strong>~1M to ~16M in just 90 days</strong> — a 16× increase. Active cases surged from May as new cases outpaced recoveries.</> },
  { col: "#cf222e", icon: "🚀", tag: "Fastest Surges", text: <><strong>Papua New Guinea (212%) and Gambia (194%)</strong> led week-over-week growth, highlighting rapid spread in smaller nations with limited healthcare.</> },
  { col: "#9a6700", icon: "🔥", tag: "US Healthcare Pressure", text: <>The US had <strong>~2.15M active cases</strong> with recovered only slightly ahead, indicating enormous concurrent burden on healthcare systems.</> },
  { col: "#8250df", icon: "🌐", tag: "Rising Regions", text: <><strong>South-East Asia &amp; Eastern Mediterranean</strong> together account for ~20% of global cases, showing spread well beyond initial Western epicentres.</> },
];

// ─── MAIN ───
export default function App() {
  return (
    <div style={{ background: "#fff", minHeight: "100vh" }}>
      <div style={s.page}>

        {/* TITLE */}
        <h1 style={s.h1}>🦠 COVID-19 Global Analytics Dashboard</h1>
        <blockquote style={s.bq}>
          <p style={{ margin: 0, color: "#656d76" }}>Analyzing WHO global COVID-19 data covering Apr–Jul 2020 using Python, SQL, Power BI, Tableau, and Looker Studio to uncover case growth trends, regional distributions, recovery rates, and CFR outliers across 195 countries.</p>
        </blockquote>
        <div style={{ display: "flex", flexWrap: "wrap", gap: 6, marginBottom: 20 }}>
          {[
            { label: "Python", version: "3.10", bg: "#3572A5", text: "#fff" },
            { label: "Jupyter", version: "Notebook", bg: "#F37626", text: "#fff" },
            { label: "Pandas", version: "Data Analysis", bg: "#150458", text: "#fff" },
            { label: "NumPy", version: "Numerical", bg: "#4DABCF", text: "#fff" },
            { label: "Matplotlib", version: "Viz", bg: "#11557c", text: "#fff" },
            { label: "Power BI", version: "Dashboard", bg: "#F2C811", text: "#000" },
            { label: "Tableau", version: "Public", bg: "#E97627", text: "#fff" },
            { label: "Looker Studio", version: "Live", bg: "#4285F4", text: "#fff" },
            { label: "License", version: "MIT", bg: "#1a7f37", text: "#fff" },
          ].map(({ label, version, bg, text }) => (
            <span key={label} style={{ display: "inline-flex", alignItems: "center", borderRadius: 4, overflow: "hidden", fontSize: 12, fontFamily: "monospace", lineHeight: "20px", height: 20 }}>
              <span style={{ background: "#555", color: "#fff", padding: "0 7px", height: "100%", display: "flex", alignItems: "center" }}>{label}</span>
              <span style={{ background: bg, color: text, padding: "0 7px", height: "100%", display: "flex", alignItems: "center", fontWeight: 600 }}>{version}</span>
            </span>
          ))}
        </div>

        <hr style={s.hr} />

        {/* KPI */}
        <h2 style={s.h2}>📌 Global Summary</h2>
        <div style={{ display: "grid", gridTemplateColumns: "repeat(4,1fr)", gap: 12, marginBottom: 24 }}>
          {[{ t: 16.48, d: 2, s: "M", l: "Confirmed Cases" }, { t: 654, d: 0, s: "K", l: "Deaths" }, { t: 9.47, d: 2, s: "M", l: "Recovered" }, { t: 6.36, d: 2, s: "M", l: "Active Cases" }].map((k) => (
            <div key={k.l} style={{ border: "1px solid #d1d9e0", borderRadius: 6, padding: "14px 16px", textAlign: "center" }}>
              <KpiCounter target={k.t} dec={k.d} suffix={k.s} />
              <div style={{ fontSize: 12, color: "#656d76", marginTop: 4 }}>{k.l}</div>
            </div>
          ))}
        </div>

        <hr style={s.hr} />

        {/* LIVE DASHBOARDS */}
        <h2 style={s.h2}>📊 Live Dashboards</h2>
        <PlatformRow icon="📊" name="Power BI" type="Multi-page interactive dashboard with slicers, drill-through, and R visuals" tags={["DAX","DirectQuery","R Visuals","3 Pages"]} statusColor="#9a6700" linkText="covid_dashboard.pbix" href="https://app.powerbi.com" />
        <PlatformRow icon="🔭" name="Looker Studio" type="Real-time Google dashboard connected to BigQuery and Google Sheets" tags={["BigQuery","Sheets","Geo Maps","Scorecards"]} statusColor="#1a7f37" linkText="link coming soon" href="https://lookerstudio.google.com" />
        <PlatformRow icon="📈" name="Tableau Public" type="Published live workbook with LOD calculations, parameters, and filter actions" tags={["LOD Calc","Parameters","Actions","Sets"]} statusColor="#8250df" linkText="link coming soon" href="https://public.tableau.com" />

        <hr style={s.hr} />

        {/* PROJECT STRUCTURE */}
        <h2 style={s.h2}>🗂️ Project Structure</h2>
        <FileTree />

        <hr style={s.hr} />

        {/* KEY INSIGHTS */}
        <h2 style={s.h2}>📈 Key Insights</h2>
        {insights.map((ins, i) => (
          <div key={i} style={s.callout(ins.col)}>
            <span style={{ fontSize: 16, flexShrink: 0, marginTop: 2 }}>{ins.icon}</span>
            <div style={{ fontSize: 14, color: "#1f2328", lineHeight: 1.55 }}>
              <span style={{ fontFamily: "monospace", fontSize: 10, color: ins.col, letterSpacing: 1, textTransform: "uppercase", display: "block", marginBottom: 3 }}>{ins.tag}</span>
              {ins.text}
            </div>
          </div>
        ))}

        <hr style={s.hr} />

        {/* TECH STACK */}
        <h2 style={s.h2}>🛠️ Tech Stack</h2>
        <table style={{ width: "100%", borderCollapse: "collapse", marginBottom: 20, fontSize: 14 }}>
          <thead>
            <tr>{["Category","Tools"].map(h => <th key={h} style={{ background: "#f6f8fa", border: "1px solid #d1d9e0", padding: "8px 16px", textAlign: "left", fontWeight: 600 }}>{h}</th>)}</tr>
          </thead>
          <tbody>
            {[["Language","Python 3.x"],["Data Analysis","Pandas, NumPy"],["Visualization","Matplotlib, Seaborn"],["Notebook Environment","Jupyter Notebook"],["BI Platforms","Power BI, Tableau Public, Looker Studio"],["Data Source","WHO COVID-19 Global Dataset"],["Version Control","Git, GitHub"]].map(([cat,tools],i) => (
              <tr key={cat} style={{ background: i%2===1?"#f6f8fa":"#fff" }}>
                <td style={{ border:"1px solid #d1d9e0",padding:"8px 16px" }}><strong>{cat}</strong></td>
                <td style={{ border:"1px solid #d1d9e0",padding:"8px 16px" }}>{tools}</td>
              </tr>
            ))}
          </tbody>
        </table>

        <hr style={s.hr} />

        {/* GETTING STARTED */}
        <h2 style={s.h2}>🚀 Getting Started</h2>
        <p style={{ marginBottom: 8 }}>1. <strong>Clone the repository</strong></p>
        <pre style={s.pre}>{`git clone https://github.com/ZeagelOg/COVID19-Analytics-Dashboard.git\ncd COVID19-Analytics-Dashboard`}</pre>
        <p style={{ marginBottom: 8 }}>2. <strong>Install dependencies</strong></p>
        <pre style={s.pre}>pip install pandas numpy matplotlib seaborn jupyter</pre>
        <p style={{ marginBottom: 8 }}>3. <strong>Launch the notebook</strong></p>
        <pre style={s.pre}>jupyter notebook COVID19_Analytics_Dashboard.ipynb</pre>
        <p>4. <strong>Run all cells</strong> — Charts are auto-saved as <code style={s.code}>.png</code> files in the project root.</p>

        <hr style={s.hr} />

        {/* CHARTS */}
        <h2 style={s.h2}>📁 Charts Preview</h2>

        {/* Top 10 + Recovery */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>🏆 Top 10 Countries — Confirmed Cases</div>
              <BarChart data={top10} maxVal={top10[0].v} colorFn={() => "#0969da"} valFn={(d) => fmt(d.v)} />
            </div>
            <div style={s.chartCaption}>02_top10_countries.png — Confirmed cases by country</div>
          </div>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>💚 Recovery Rate by Country (%)</div>
              <BarChart data={recovery} maxVal={100} colorFn={(d) => d.r < 10 ? "#cf222e" : d.r > 70 ? "#1a7f37" : "#0969da"} valFn={(d) => `${d.r}%`} />
            </div>
            <div style={s.chartCaption}>04_recovery_rate.png — Chile leads at 95%+, UK at bottom</div>
          </div>
        </div>

        {/* Trend */}
        <div style={s.chartCard}>
          <div style={s.chartInner}>
            <div style={s.chartTitle}>📈 Global COVID-19 Growth Trends — Apr to Jul 2020</div>
            <TrendChart />
          </div>
          <div style={s.chartCaption}>05_global_trend.png — 16× growth in 90 days from 1M to 16.5M</div>
        </div>

        {/* Regional + Pie */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>🗺️ Regional Distribution of Cases</div>
              <RegionalBars />
            </div>
            <div style={s.chartCaption}>03_regional_distribution.png</div>
          </div>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>🥧 Confirmed Cases by WHO Region</div>
              <DonutChart />
            </div>
            <div style={s.chartCaption}>06_regional_pie.png — Americas: 52.8%</div>
          </div>
        </div>

        {/* Growth + Stacked */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>🚀 Top 10 — Fastest WoW Case Growth</div>
              <GrowthBars />
            </div>
            <div style={s.chartCaption}>07_wow_growth.png — Papua New Guinea & Gambia 200%+</div>
          </div>
          <div style={s.chartCard}>
            <div style={s.chartInner}>
              <div style={s.chartTitle}>⚡ Active vs Recovered Cases per Country</div>
              <StackedBars />
            </div>
            <div style={s.chartCaption}>08_active_vs_recovered.png</div>
          </div>
        </div>

        {/* Heatmap */}
        <div style={s.chartCard}>
          <div style={s.chartInner}>
            <div style={s.chartTitle}>🔥 Country Metrics Heatmap — CFR%</div>
            <HeatmapTable />
          </div>
          <div style={s.chartCaption}>09_heatmap.png — UK (15.33%) and Mexico (11.14%) are stark CFR outliers</div>
        </div>

        <hr style={s.hr} />

        {/* DATA SOURCE */}
        <h2 style={s.h2}>📌 Data Source</h2>
        <p>COVID-19 data sourced from the <a href="https://covid19.who.int/data" style={{ color: "#0969da" }}>WHO COVID-19 Global Dataset</a> covering the Apr–Jul 2020 pandemic period.</p>

        <hr style={s.hr} />

        {/* LICENSE */}
        <h2 style={s.h2}>📄 License</h2>
        <p>This project is open-source and available under the <a href="#" style={{ color: "#0969da" }}>MIT License</a>.</p>

      </div>
    </div>
  );
}
