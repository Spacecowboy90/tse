<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BTR Proforma Yield Calculator</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@300;400;500;600&family=IBM+Plex+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
:root {
  --bg:       #0c0c0c;
  --bg2:      #111111;
  --bg3:      #171717;
  --panel:    #1c1c1c;
  --panel2:   #222222;
  --border:   rgba(255,255,255,0.08);
  --border2:  rgba(255,255,255,0.14);
  --white:    #f2f2f2;
  --off:      #d0d0d0;
  --muted:    #6e6e6e;
  --dim:      #444;
  --accent:   #ffffff;
  --auto-bg:  rgba(255,255,255,0.04);
  --auto-bdr: rgba(255,255,255,0.12);
  --auto-txt: #b8b8b8;
  --lock-bg:  rgba(255,255,255,0.03);
  --pos:      #e8e8e8;
  --neg:      #888888;
  --shadow:   0 2px 16px rgba(0,0,0,0.6);
  --mono:     'IBM Plex Mono', monospace;
  --sans:     'IBM Plex Sans', sans-serif;
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

body {
  background: var(--bg);
  color: var(--white);
  font-family: var(--sans);
  font-size: 13px;
  min-height: 100vh;
  line-height: 1.4;
}

/* ── HEADER ── */
.header {
  background: var(--bg2);
  border-bottom: 1px solid var(--border2);
  padding: 16px 32px;
  display: flex; align-items: center; justify-content: space-between;
  position: sticky; top: 0; z-index: 100;
}
.header-brand {
  display: flex; align-items: center; gap: 14px;
}
.logo-block {
  width: 32px; height: 32px;
  border: 1px solid var(--border2);
  display: flex; align-items: center; justify-content: center;
  font-family: var(--mono); font-size: 10px; font-weight: 600;
  color: var(--white); letter-spacing: -0.5px;
}
.header-title { font-family: var(--sans); font-size: 14px; font-weight: 500; color: var(--white); letter-spacing: 0.02em; }

/* ── LAYOUT ── */
.container { max-width: 1440px; margin: 0 auto; padding: 24px; }
.grid-main { display: grid; grid-template-columns: 370px 1fr; gap: 20px; align-items: start; }
.grid-inputs { display: flex; flex-direction: column; gap: 14px; }
.grid-outputs { display: flex; flex-direction: column; gap: 14px; }

/* ── CARDS ── */
.card {
  background: var(--panel);
  border: 1px solid var(--border);
  border-radius: 4px;
  overflow: hidden;
}
.card-header {
  padding: 10px 14px;
  background: var(--panel2);
  border-bottom: 1px solid var(--border);
  display: flex; align-items: center; justify-content: space-between;
}
.card-title {
  font-family: var(--mono); font-size: 9.5px; font-weight: 600;
  letter-spacing: 0.14em; text-transform: uppercase; color: var(--off);
}
.card-body { padding: 14px; }

/* ── FORM ── */
.field { margin-bottom: 10px; }
.field:last-child { margin-bottom: 0; }
.field-row { display: flex; gap: 10px; }
.field-row .field { flex: 1; }

label {
  display: block; font-family: var(--mono); font-size: 9px;
  font-weight: 500; color: var(--muted); letter-spacing: 0.1em;
  text-transform: uppercase; margin-bottom: 4px;
}
.auto-tag {
  display: inline-block; font-size: 7.5px; letter-spacing: 0.06em;
  color: var(--muted); border: 1px solid var(--border);
  padding: 0 4px; border-radius: 2px; margin-left: 6px;
  vertical-align: middle; font-family: var(--mono);
}

input[type="text"], input[type="number"], select {
  width: 100%; background: var(--bg3);
  border: 1px solid var(--border);
  border-radius: 3px; padding: 7px 10px;
  color: var(--white); font-family: var(--mono);
  font-size: 12px; outline: none;
  transition: border-color 0.12s;
}
input:focus, select:focus { border-color: var(--border2); }
input.auto-field { background: var(--auto-bg); border-color: var(--auto-bdr); color: var(--auto-txt); }
input.overridden { border-color: rgba(255,255,255,0.3); color: var(--white); }
input.locked {
  background: var(--lock-bg); border-color: transparent;
  color: var(--dim); cursor: not-allowed; pointer-events: none;
}
select option { background: #1c1c1c; }
.pfx { position: relative; }
.pfx span { position: absolute; left: 10px; top: 50%; transform: translateY(-50%); color: var(--muted); font-family: var(--mono); font-size: 11px; pointer-events: none; }
.pfx input { padding-left: 18px; }

/* ── RENT TABLE ── */
.data-table { width: 100%; border-collapse: collapse; }
.data-table th {
  font-family: var(--mono); font-size: 9px; color: var(--muted);
  letter-spacing: 0.1em; text-transform: uppercase; padding: 7px 8px;
  text-align: left; border-bottom: 1px solid var(--border);
  background: var(--bg3); font-weight: 500;
}
.data-table td { padding: 7px 8px; border-bottom: 1px solid rgba(255,255,255,0.03); vertical-align: middle; }
.data-table tr:last-child td { border-bottom: none; }
.data-table input {
  background: var(--auto-bg); border: 1px solid var(--auto-bdr);
  border-radius: 3px; padding: 5px 8px; color: var(--auto-txt);
  font-family: var(--mono); font-size: 11.5px; width: 100%; outline: none;
}
.data-table input:focus { border-color: var(--border2); color: var(--white); background: rgba(255,255,255,0.06); }
.data-table input.overridden { border-color: rgba(255,255,255,0.3); color: var(--white); background: rgba(255,255,255,0.05); }
.data-table input.narrow { width: 64px !important; }
.bed-pill {
  font-family: var(--mono); font-size: 10.5px; font-weight: 500;
  color: var(--off); border: 1px solid var(--border); border-radius: 2px;
  padding: 2px 8px; display: inline-block;
}

/* ── IMPACT FEE TABLE ── */
.fee-row td { padding: 5px 4px; border-bottom: 1px solid rgba(255,255,255,0.03); }
.fee-row:last-child td { border-bottom: none; }
.fee-label { font-size: 11px; color: var(--muted); font-family: var(--mono); }
.fee-input {
  background: var(--auto-bg); border: 1px solid var(--auto-bdr);
  border-radius: 3px; padding: 4px 8px; color: var(--auto-txt);
  font-family: var(--mono); font-size: 11.5px; width: 120px;
  text-align: right; outline: none;
}
.fee-input:focus { border-color: var(--border2); color: var(--white); }
.fee-input.overridden { border-color: rgba(255,255,255,0.3); color: var(--white); }
.fee-total-cell { font-family: var(--mono); font-size: 13px; font-weight: 600; color: var(--white); text-align: right; }

/* ── OPERATING ASSUMPTIONS ── */
.opex-section { position: relative; }
.opex-locked .opex-field-input { background: var(--lock-bg) !important; border-color: transparent !important; color: var(--dim) !important; cursor: not-allowed !important; pointer-events: none !important; }
.opex-unlocked .opex-field-input { background: var(--bg3) !important; border-color: var(--border) !important; color: var(--white) !important; pointer-events: auto !important; cursor: text !important; }
.lock-toggle-btn {
  font-family: var(--mono); font-size: 9px; font-weight: 500;
  color: var(--muted); border: 1px solid var(--border);
  background: transparent; border-radius: 2px; padding: 3px 9px;
  cursor: pointer; letter-spacing: 0.08em; text-transform: uppercase;
  transition: color 0.12s, border-color 0.12s;
}
.lock-toggle-btn:hover { color: var(--white); border-color: var(--border2); }
.slider-row { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; }
.slider-lbl { font-family: var(--mono); font-size: 9px; color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; flex: 1; }
.slider-val { font-family: var(--mono); font-size: 12px; color: var(--off); min-width: 42px; text-align: right; }
input[type=range] {
  -webkit-appearance: none; width: 100%; height: 2px;
  background: var(--border2); border-radius: 2px; outline: none; cursor: pointer;
  margin: 5px 0;
}
input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none; width: 12px; height: 12px; border-radius: 50%;
  background: var(--white); cursor: pointer;
}
.slider-container.locked input[type=range] { opacity: 0.25; pointer-events: none; }

/* ── KPI GRID ── */
.kpi-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; margin-bottom: 14px; }
.kpi-card {
  background: var(--panel);
  border: 1px solid var(--border);
  border-top: 2px solid var(--border2);
  border-radius: 4px; padding: 14px;
}
.kpi-card.primary { border-top-color: var(--white); }
.kpi-card.secondary { border-top-color: var(--dim); }
.kpi-lbl { font-family: var(--mono); font-size: 9px; color: var(--muted); letter-spacing: 0.12em; text-transform: uppercase; margin-bottom: 8px; }
.kpi-val { font-family: var(--mono); font-size: 24px; font-weight: 600; color: var(--white); letter-spacing: -0.5px; }
.kpi-val.dim { color: var(--off); font-size: 20px; }
.kpi-sub { font-family: var(--mono); font-size: 9.5px; color: var(--muted); margin-top: 5px; }

/* ── YIELD METER ── */
.yield-meter-wrap { margin-top: 10px; }
.yield-track {
  height: 4px; border-radius: 2px; position: relative;
  background: linear-gradient(to right, #333 0%, #555 40%, #888 60%, #bbb 80%, #fff 100%);
}
.yield-pip {
  position: absolute; top: -5px;
  width: 14px; height: 14px; border-radius: 50%;
  background: var(--white); border: 2px solid var(--bg);
  transform: translateX(-50%);
  transition: left 0.4s cubic-bezier(0.4,0,0.2,1);
}
.yield-scale { display: flex; justify-content: space-between; font-family: var(--mono); font-size: 8.5px; color: var(--muted); margin-top: 5px; }

/* ── FORMULA BUTTON ── */
.formula-btn {
  font-family: var(--mono); font-size: 9px; color: var(--muted);
  border: 1px solid var(--border); background: transparent; border-radius: 2px;
  padding: 2px 8px; cursor: pointer; letter-spacing: 0.06em;
  transition: color 0.12s, border-color 0.12s;
}
.formula-btn:hover { color: var(--white); border-color: var(--border2); }

/* ── FORMULA MODAL ── */
.modal-overlay {
  position: fixed; inset: 0; background: rgba(0,0,0,0.85);
  z-index: 1000; display: none; align-items: center; justify-content: center;
}
.modal-overlay.open { display: flex; }
.modal {
  background: var(--panel); border: 1px solid var(--border2);
  border-radius: 4px; width: 520px; max-width: 95vw; padding: 28px;
  position: relative;
}
.modal-close {
  position: absolute; top: 14px; right: 16px;
  background: none; border: none; color: var(--muted); cursor: pointer;
  font-size: 18px; line-height: 1; transition: color 0.12s;
}
.modal-close:hover { color: var(--white); }
.modal h2 { font-family: var(--mono); font-size: 11px; font-weight: 600; letter-spacing: 0.14em; text-transform: uppercase; color: var(--off); margin-bottom: 20px; }
.formula-block {
  background: var(--bg3); border: 1px solid var(--border);
  border-radius: 3px; padding: 16px; margin-bottom: 14px;
  font-family: var(--mono); font-size: 12px; line-height: 2;
}
.formula-block .eq { color: var(--white); font-weight: 500; }
.formula-block .comment { color: var(--muted); font-size: 10px; }
.formula-defs { font-size: 11px; color: var(--muted); line-height: 2; }
.formula-defs dt { font-family: var(--mono); color: var(--off); float: left; width: 48px; }
.formula-defs dd { margin-left: 56px; }
.divider { height: 1px; background: var(--border); margin: 10px 0; }

/* ── PROFORMA TABLE ── */
.pf-table { width: 100%; border-collapse: collapse; }
.pf-table thead th {
  font-family: var(--mono); font-size: 9px; color: var(--muted);
  letter-spacing: 0.1em; text-transform: uppercase; padding: 9px 12px;
  text-align: right; border-bottom: 1px solid var(--border2); background: var(--bg3);
}
.pf-table thead th:first-child { text-align: left; }
.pf-table tbody td {
  padding: 6px 12px; border-bottom: 1px solid rgba(255,255,255,0.025);
  font-family: var(--mono); font-size: 11.5px;
}
.pf-table tbody tr:hover td { background: rgba(255,255,255,0.015); }
.pf-section td { font-family: var(--mono); font-size: 9px; color: var(--muted); letter-spacing: 0.12em; text-transform: uppercase; padding: 10px 12px 5px; border-bottom: none; }
.pf-indent td:first-child { padding-left: 26px; color: var(--muted); }
.pf-subtotal td { border-top: 1px solid var(--border); color: var(--off); font-weight: 500; padding: 8px 12px; }
.pf-final td { background: rgba(255,255,255,0.04); border-top: 1px solid var(--border2); border-bottom: 1px solid var(--border2); color: var(--white); font-weight: 600; font-size: 12.5px; padding: 9px 12px; }
.num { text-align: right; }
.neg-c { color: var(--dim) !important; }
.pos-c { color: var(--off); }

/* ── WATERFALL ── */
.wf-row { display: flex; align-items: center; gap: 10px; margin-bottom: 6px; }
.wf-lbl { font-family: var(--mono); font-size: 9.5px; color: var(--muted); width: 130px; text-align: right; flex-shrink: 0; }
.wf-track { flex: 1; height: 16px; background: rgba(255,255,255,0.03); border-radius: 2px; position: relative; overflow: hidden; }
.wf-bar { height: 100%; border-radius: 2px; position: absolute; left: 0; top: 0; transition: width 0.35s ease; }
.wf-val { font-family: var(--mono); font-size: 10px; color: var(--muted); width: 86px; text-align: right; flex-shrink: 0; }
.wf-total .wf-lbl { color: var(--white); font-weight: 500; }
.wf-total .wf-val { color: var(--white); font-weight: 500; }

/* ── SENSITIVITY ── */
.sens-table { width: 100%; border-collapse: collapse; }
.sens-table th, .sens-table td { padding: 6px 8px; text-align: center; font-family: var(--mono); font-size: 10.5px; }
.sens-table th { font-size: 9px; color: var(--muted); border-bottom: 1px solid var(--border); background: var(--bg3); font-weight: 500; letter-spacing: 0.08em; }
.sens-table th:first-child { text-align: left; }
.sens-table td:first-child { text-align: left; font-size: 9px; color: var(--muted); }
.s-hi  { color: #f0f0f0; background: rgba(255,255,255,0.12); }
.s-mid { color: #aaa; background: rgba(255,255,255,0.06); }
.s-lo  { color: #666; background: rgba(255,255,255,0.02); }
.s-vlo { color: #444; background: transparent; }
.s-cur { color: #fff !important; background: rgba(255,255,255,0.18) !important; font-weight: 700; outline: 1px solid rgba(255,255,255,0.3); }
.sens-legend { font-family: var(--mono); font-size: 9px; color: var(--muted); padding: 8px 12px; border-top: 1px solid var(--border); }

/* ── BREAKEVEN GRID ── */
.be-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; }
.be-card { background: var(--bg3); border: 1px solid var(--border); border-radius: 3px; padding: 12px; text-align: center; }
.be-lbl { font-family: var(--mono); font-size: 8.5px; color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; margin-bottom: 8px; line-height: 1.5; }
.be-val { font-family: var(--mono); font-size: 17px; font-weight: 600; color: var(--white); }

/* ── BUTTONS ── */
.btn { padding: 7px 14px; border: 1px solid var(--border); border-radius: 3px; font-family: var(--mono); font-size: 10px; font-weight: 500; cursor: pointer; letter-spacing: 0.06em; transition: all 0.12s; background: transparent; color: var(--muted); }
.btn:hover { color: var(--white); border-color: var(--border2); }
.btn-solid { background: var(--white); color: var(--bg); border-color: var(--white); }
.btn-solid:hover { background: var(--off); }
.btn-sm { padding: 4px 10px; font-size: 9.5px; }
.reset-lnk { font-family: var(--mono); font-size: 9px; color: var(--muted); cursor: pointer; text-decoration: underline; background: none; border: none; }
.reset-lnk:hover { color: var(--white); }

/* ── AI PANEL ── */
.ai-panel { background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: 3px; padding: 12px; margin-top: 10px; display: none; }
.ai-panel.open { display: block; }
.ai-panel-title { font-family: var(--mono); font-size: 9px; color: var(--muted); letter-spacing: 0.12em; text-transform: uppercase; margin-bottom: 8px; }
.ai-panel p { font-size: 11.5px; color: var(--off); line-height: 1.65; }
.ai-dot { display: inline-block; width: 5px; height: 5px; border-radius: 50%; background: var(--muted); margin: 0 2px; animation: pulse 1s infinite; }
.ai-dot:nth-child(2) { animation-delay: 0.2s; }
.ai-dot:nth-child(3) { animation-delay: 0.4s; }
@keyframes pulse { 0%,80%,100%{opacity:0.2} 40%{opacity:0.8} }

/* ── STATUS ── */
.status-bar {
  background: var(--bg2); border-top: 1px solid var(--border);
  padding: 8px 32px; display: flex; gap: 28px; align-items: center;
  font-family: var(--mono); font-size: 9.5px; color: var(--muted);
  position: sticky; bottom: 0;
}
.status-item span { color: var(--off); }
.status-live { display: flex; align-items: center; gap: 6px; }
.live-dot { width: 5px; height: 5px; border-radius: 50%; background: var(--white); opacity: 0.8; }
.source-note { margin-left: auto; color: var(--dim); }

/* ── SCROLL ── */
::-webkit-scrollbar { width: 5px; height: 5px; }
::-webkit-scrollbar-track { background: var(--bg); }
::-webkit-scrollbar-thumb { background: var(--border2); border-radius: 3px; }

/* ── MISC ── */
.note { font-size: 10px; color: var(--muted); margin-top: 6px; line-height: 1.55; font-style: italic; }
.market-src { font-family: var(--mono); font-size: 8.5px; color: var(--muted); border: 1px solid var(--border); border-radius: 2px; padding: 1px 6px; margin-left: 6px; }

@media (max-width: 1100px) { .grid-main { grid-template-columns: 1fr; } .kpi-grid { grid-template-columns: 1fr 1fr; } }
@media (max-width: 700px) { .kpi-grid { grid-template-columns: 1fr; } .container { padding: 12px; } }
</style>
</head>
<body>

<!-- HEADER -->
<div class="header">
  <div class="header-brand">
    <div class="logo-block">BTR</div>
    <span class="header-title">Build-to-Rent Proforma Yield Calculator</span>
  </div>
  <button class="btn btn-solid btn-sm" onclick="exportCSV()">Export CSV</button>
</div>

<!-- FORMULA MODAL -->
<div class="modal-overlay" id="formulaModal">
  <div class="modal">
    <button class="modal-close" onclick="closeFormula()">×</button>
    <h2>Yield on Cost — Formula</h2>
    <div class="formula-block">
      <div class="eq">Yield on Cost  =  NOI  ÷  TDC</div>
      <div style="height:10px;"></div>
      <div class="eq">NOI  =  EGI  −  Total OpEx</div>
      <div class="eq">EGI  =  GPR  ×  (1 − Vacancy Rate)</div>
      <div class="eq">GPR  =  Σ (Units<sub>n</sub> × Rent<sub>n</sub>) × 12</div>
      <div style="height:10px;"></div>
      <div class="eq">TDC  =  Land  +  Horizontal  +  Vertical  +  Amenities</div>
      <div class="eq" style="padding-left:52px;">+  Soft Costs  +  Contingency  +  Impact Fees</div>
    </div>
    <div class="formula-defs">
      <dl>
        <div style="overflow:hidden;margin-bottom:4px;"><dt>NOI</dt><dd>Net Operating Income — EGI minus all stabilized operating expenses</dd></div>
        <div style="overflow:hidden;margin-bottom:4px;"><dt>TDC</dt><dd>Total Development Cost — all-in cost to develop and stabilize the community</dd></div>
        <div style="overflow:hidden;margin-bottom:4px;"><dt>EGI</dt><dd>Effective Gross Income — GPR adjusted for vacancy loss</dd></div>
        <div style="overflow:hidden;margin-bottom:4px;"><dt>GPR</dt><dd>Gross Potential Rent — 100% occupancy at contracted rents annualized</dd></div>
        <div style="overflow:hidden;"><dt>OpEx</dt><dd>Mgmt fee + taxes + insurance + repairs + CapEx reserve + admin + leasing</dd></div>
      </dl>
    </div>
    <div class="divider"></div>
    <div style="font-family:var(--mono);font-size:9.5px;color:var(--muted);line-height:1.8;">
      Target yield range: 5.0% – 6.5%+ for stabilized BTR communities (institutional grade).<br>
      Yield on cost is calculated on an unlevered basis — no debt service is deducted.
    </div>
  </div>
</div>

<div class="container">
  <div class="grid-main">

    <!-- ═══ LEFT INPUTS ═══ -->
    <div class="grid-inputs">

      <!-- MARKET SELECTION -->
      <div class="card">
        <div class="card-header"><span class="card-title">Market Selection</span></div>
        <div class="card-body">
          <div class="field">
            <label>County / Submarket</label>
            <select id="county" onchange="onCountyChange()">
              <option value="">— Select County —</option>
              <option value="orange">Orange County (Orlando / Metro Core)</option>
              <option value="osceola">Osceola County (Kissimmee / St. Cloud)</option>
              <option value="seminole">Seminole County (Sanford / Longwood)</option>
              <option value="lake">Lake County (Clermont / Leesburg)</option>
              <option value="polk">Polk County (Lakeland / Winter Haven)</option>
              <option value="volusia">Volusia County (DeLand / Deltona)</option>
              <option value="brevard">Brevard County (Viera / Melbourne)</option>
            </select>
          </div>
          <div class="field">
            <label>Development Status</label>
            <select id="devStatus" onchange="onDevStatusChange()">
              <option value="raw">Raw Land (Unentitled)</option>
              <option value="entitled">Entitled / Rezoned</option>
              <option value="devlots" selected>Fully Developed Lots (Ready to Build)</option>
            </select>
          </div>
          <div class="field-row">
            <div class="field">
              <label>Project Name</label>
              <input type="text" id="projectName" placeholder="Project name" />
            </div>
            <div class="field">
              <label>Analysis Date</label>
              <input type="text" id="analysisDate" readonly style="color:var(--muted);cursor:default;" />
            </div>
          </div>
        </div>
      </div>

      <!-- DEAL ASSUMPTIONS -->
      <div class="card">
        <div class="card-header"><span class="card-title">Deal Assumptions</span></div>
        <div class="card-body">
          <div class="field">
            <label>Land / Lot Purchase Price</label>
            <div class="pfx"><span>$</span><input type="number" id="landPrice" value="7500000" step="100000" oninput="recalc()" /></div>
          </div>
          <div class="field-row">
            <div class="field">
              <label>Total Lots</label>
              <input type="number" id="totalUnits" value="110" min="1" max="1000" oninput="recalc()" />
            </div>
            <div class="field">
              <label>Avg Lot Width (ft)</label>
              <input type="number" id="lotWidth" value="45" oninput="recalc()" />
            </div>
          </div>

          <div class="divider"></div>
          <div style="font-family:var(--mono);font-size:9px;color:var(--muted);letter-spacing:0.12em;text-transform:uppercase;margin-bottom:8px;">Unit Mix</div>

          <table class="data-table">
            <thead>
              <tr>
                <th>Plan</th>
                <th>Units</th>
                <th>SF</th>
                <th style="text-align:right;">Mix %</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td><span class="bed-pill">3 BR</span></td>
                <td><input class="narrow" type="number" id="units3" value="30" min="0" oninput="recalc()" /></td>
                <td><input type="number" id="sf3" value="1600" min="0" oninput="recalc()" /></td>
                <td class="num" id="pct3" style="font-family:var(--mono);font-size:10.5px;color:var(--muted);">—</td>
              </tr>
              <tr>
                <td><span class="bed-pill">4 BR</span></td>
                <td><input class="narrow" type="number" id="units4" value="60" min="0" oninput="recalc()" /></td>
                <td><input type="number" id="sf4" value="1900" min="0" oninput="recalc()" /></td>
                <td class="num" id="pct4" style="font-family:var(--mono);font-size:10.5px;color:var(--muted);">—</td>
              </tr>
              <tr>
                <td><span class="bed-pill">5 BR</span></td>
                <td><input class="narrow" type="number" id="units5" value="20" min="0" oninput="recalc()" /></td>
                <td><input type="number" id="sf5" value="2200" min="0" oninput="recalc()" /></td>
                <td class="num" id="pct5" style="font-family:var(--mono);font-size:10.5px;color:var(--muted);">—</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- CONSTRUCTION COSTS -->
      <div class="card">
        <div class="card-header">
          <span class="card-title">Construction Costs</span>
          <button class="reset-lnk" onclick="resetConstruction()">Reset to market</button>
        </div>
        <div class="card-body">
          <div id="horizSection">
            <div class="field">
              <label>Horizontal / Site Dev Cost (per lot) <span class="auto-tag">AUTO</span></label>
              <div class="pfx"><span>$</span><input type="number" id="horizCost" class="auto-field" value="35000" oninput="markOverride('horizCost');recalc()" /></div>
            </div>
          </div>
          <div class="field">
            <label>Vertical Hard Cost (per SF) <span class="auto-tag">AUTO</span></label>
            <div class="pfx"><span>$</span><input type="number" id="vertCost" class="auto-field" value="115" oninput="markOverride('vertCost');recalc()" /></div>
          </div>
          <div class="field-row">
            <div class="field">
              <label>Soft Costs (% of hard)</label>
              <input type="number" id="softPct" value="10" min="0" max="30" oninput="recalc()" />
            </div>
            <div class="field">
              <label>Contingency (%)</label>
              <input type="number" id="contPct" value="5" min="0" max="20" oninput="recalc()" />
            </div>
          </div>
          <div class="field">
            <label>Amenity Budget (total)</label>
            <div class="pfx"><span>$</span><input type="number" id="amenityBudget" value="1500000" step="100000" oninput="recalc()" /></div>
          </div>
          <p class="note">Horizontal cost auto-adjusts by development status. Override any field to customize.</p>
        </div>
      </div>

      <!-- IMPACT FEES -->
      <div class="card">
        <div class="card-header">
          <span class="card-title">Impact Fees <span class="market-src" id="ifSource">SELECT COUNTY</span></span>
          <button class="reset-lnk" onclick="resetImpactFees()">Reset to county</button>
        </div>
        <div class="card-body">
          <table style="width:100%;border-collapse:collapse;">
            <tbody>
              <tr class="fee-row"><td class="fee-label">Transportation / Mobility</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_transport" value="0" oninput="markFeeOverride('if_transport');recalc()" /></td></tr>
              <tr class="fee-row"><td class="fee-label">School</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_school" value="0" oninput="markFeeOverride('if_school');recalc()" /></td></tr>
              <tr class="fee-row"><td class="fee-label">Parks &amp; Recreation</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_parks" value="0" oninput="markFeeOverride('if_parks');recalc()" /></td></tr>
              <tr class="fee-row"><td class="fee-label">Fire / EMS</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_fire" value="0" oninput="markFeeOverride('if_fire');recalc()" /></td></tr>
              <tr class="fee-row"><td class="fee-label">Law Enforcement</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_law" value="0" oninput="markFeeOverride('if_law');recalc()" /></td></tr>
              <tr class="fee-row"><td class="fee-label">Water / Sewer Connection</td><td style="text-align:right;"><input type="number" class="fee-input" id="if_utility" value="0" oninput="markFeeOverride('if_utility');recalc()" /></td></tr>
              <tr style="border-top:1px solid var(--border2);">
                <td style="padding:8px 4px 4px;font-family:var(--mono);font-size:10px;font-weight:600;color:var(--off);">Total / Lot</td>
                <td style="padding:8px 4px 4px;" class="fee-total-cell" id="ifTotal">$0</td>
              </tr>
            </tbody>
          </table>
          <p class="note" id="ifNote">Select a county above to auto-populate current impact fee schedule.</p>
        </div>
      </div>

      <!-- MARKET RENTS -->
      <div class="card">
        <div class="card-header">
          <span class="card-title">Market Rents <span class="market-src" id="rentSource">SELECT COUNTY</span></span>
          <button class="btn btn-sm" id="fetchRentsBtn" onclick="fetchLiveRents()">Fetch Live Rents</button>
        </div>
        <div class="card-body">
          <table class="data-table">
            <thead>
              <tr>
                <th>Plan</th>
                <th>AMH Rent</th>
                <th>BTR Comp</th>
                <th>Used / Override</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td><span class="bed-pill">3 BR</span></td>
                <td id="amh3" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td id="btr3" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td><input type="number" id="rent3" class="auto-field" value="0" oninput="markRentOverride('rent3');recalc()" /></td>
              </tr>
              <tr>
                <td><span class="bed-pill">4 BR</span></td>
                <td id="amh4" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td id="btr4" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td><input type="number" id="rent4" class="auto-field" value="0" oninput="markRentOverride('rent4');recalc()" /></td>
              </tr>
              <tr>
                <td><span class="bed-pill">5 BR</span></td>
                <td id="amh5" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td id="btr5" style="font-family:var(--mono);font-size:11px;color:var(--muted);">—</td>
                <td><input type="number" id="rent5" class="auto-field" value="0" oninput="markRentOverride('rent5');recalc()" /></td>
              </tr>
            </tbody>
          </table>
          <div class="ai-panel" id="aiPanel">
            <div class="ai-panel-title">Live Rent Intelligence</div>
            <div id="aiContent"><div style="text-align:center;padding:12px;"><span class="ai-dot"></span><span class="ai-dot"></span><span class="ai-dot"></span></div></div>
          </div>
          <p class="note" id="rentNote" style="margin-top:8px;">Select a county to load AMH submarket and BTR comp rents. Click Fetch Live Rents for real-time lookup.</p>
        </div>
      </div>

      <!-- OPERATING ASSUMPTIONS -->
      <div class="card">
        <div class="card-header">
          <span class="card-title">Operating Assumptions</span>
          <button class="lock-toggle-btn" id="opexLockBtn" onclick="toggleOpexLock()">Override</button>
        </div>
        <div class="card-body opex-section opex-locked" id="opexSection">
          <!-- Vacancy + Mgmt sliders -->
          <div id="sliderVacancy" class="slider-container locked">
            <div class="slider-row">
              <span class="slider-lbl">Vacancy Rate</span>
              <span class="slider-val" id="vacancyVal">5.0%</span>
            </div>
            <input type="range" id="vacancy" min="1" max="20" value="5" step="0.5" oninput="document.getElementById('vacancyVal').textContent=parseFloat(this.value).toFixed(1)+'%';recalc()" />
          </div>
          <div id="sliderMgmt" class="slider-container locked" style="margin-bottom:12px;">
            <div class="slider-row">
              <span class="slider-lbl">Management Fee (% EGI)</span>
              <span class="slider-val" id="mgmtVal">8.0%</span>
            </div>
            <input type="range" id="mgmt" min="4" max="15" value="8" step="0.5" oninput="document.getElementById('mgmtVal').textContent=parseFloat(this.value).toFixed(1)+'%';recalc()" />
          </div>

          <div class="divider"></div>

          <div class="field-row">
            <div class="field">
              <label>Property Taxes ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="taxes" value="3200" oninput="recalc()" /></div>
            </div>
            <div class="field">
              <label>Insurance ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="insurance" value="1800" oninput="recalc()" /></div>
            </div>
          </div>
          <div class="field-row">
            <div class="field">
              <label>Repairs &amp; Maint. ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="repairs" value="900" oninput="recalc()" /></div>
            </div>
            <div class="field">
              <label>CapEx Reserve ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="capex" value="500" oninput="recalc()" /></div>
            </div>
          </div>
          <div class="field-row">
            <div class="field">
              <label>Admin / HOA ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="admin" value="600" oninput="recalc()" /></div>
            </div>
            <div class="field">
              <label>Leasing ($/yr/unit)</label>
              <div class="pfx"><span>$</span><input type="number" class="opex-field-input" id="leasing" value="400" oninput="recalc()" /></div>
            </div>
          </div>

          <div class="divider"></div>
          <div class="field-row">
            <div class="field">
              <label>Exit Cap Rate (%)</label>
              <input type="number" class="opex-field-input" id="exitCap" value="5.25" step="0.25" oninput="recalc()" />
            </div>
            <div class="field">
              <label>Hold Period (years)</label>
              <input type="number" class="opex-field-input" id="holdYears" value="7" min="1" max="20" oninput="recalc()" />
            </div>
          </div>
        </div>
      </div>

    </div><!-- /grid-inputs -->

    <!-- ═══ RIGHT OUTPUTS ═══ -->
    <div class="grid-outputs">

      <!-- KPI CARDS -->
      <div class="kpi-grid">
        <div class="kpi-card primary">
          <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:6px;">
            <div class="kpi-lbl" style="margin-bottom:0;">Stabilized Yield on Cost</div>
            <button class="formula-btn" onclick="openFormula()">Formula</button>
          </div>
          <div class="kpi-val" id="kpiYield">—</div>
          <div class="yield-meter-wrap">
            <div class="yield-track"><div class="yield-pip" id="yieldPip" style="left:0%"></div></div>
            <div class="yield-scale"><span>3%</span><span>4%</span><span>5%</span><span>6%</span><span>7%+</span></div>
          </div>
        </div>
        <div class="kpi-card secondary">
          <div class="kpi-lbl">Stabilized NOI (Annual)</div>
          <div class="kpi-val dim" id="kpiNOI">—</div>
          <div class="kpi-sub" id="kpiNOIsub">Net operating income</div>
        </div>
        <div class="kpi-card secondary">
          <div class="kpi-lbl">Total Development Cost</div>
          <div class="kpi-val dim" id="kpiTDC">—</div>
          <div class="kpi-sub" id="kpiTDCsub">All-in cost</div>
        </div>
        <div class="kpi-card">
          <div class="kpi-lbl">Cost Per Lot</div>
          <div class="kpi-val dim" id="kpiCPL">—</div>
          <div class="kpi-sub">TDC ÷ total lots</div>
        </div>
        <div class="kpi-card">
          <div class="kpi-lbl">Effective Gross Income</div>
          <div class="kpi-val dim" id="kpiEGI">—</div>
          <div class="kpi-sub" id="kpiEGIsub">Annual EGI</div>
        </div>
        <div class="kpi-card">
          <div class="kpi-lbl">Estimated Exit Value</div>
          <div class="kpi-val dim" id="kpiExit">—</div>
          <div class="kpi-sub" id="kpiExitsub">NOI ÷ exit cap</div>
        </div>
      </div>

      <!-- PROFORMA TABLE -->
      <div class="card">
        <div class="card-header">
          <span class="card-title">Full Proforma</span>
          <span style="font-family:var(--mono);font-size:9.5px;color:var(--muted);" id="pfHeader">—</span>
        </div>
        <div style="overflow-x:auto;">
          <table class="pf-table">
            <thead>
              <tr>
                <th style="text-align:left;width:46%;">Line Item</th>
                <th>Per Lot</th>
                <th>Total</th>
                <th>% TDC</th>
              </tr>
            </thead>
            <tbody id="pfBody">
              <tr><td colspan="4" style="text-align:center;padding:28px;color:var(--muted);font-family:var(--mono);font-size:11px;">Select a county and enter assumptions to generate proforma</td></tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- COST WATERFALL -->
      <div class="card">
        <div class="card-header"><span class="card-title">Cost Waterfall — Per Lot</span></div>
        <div class="card-body" id="waterfall">
          <div style="text-align:center;color:var(--muted);font-size:11px;padding:12px;font-family:var(--mono);">Enter assumptions above to generate waterfall.</div>
        </div>
      </div>

      <!-- SENSITIVITY -->
      <div class="card">
        <div class="card-header"><span class="card-title">Yield Sensitivity (Yield on Cost %)</span></div>
        <div style="overflow-x:auto;">
          <div id="sensWrap" style="padding:14px;color:var(--muted);font-family:var(--mono);font-size:11px;">Generate proforma first to see sensitivity analysis.</div>
        </div>
      </div>

      <!-- BREAKEVEN -->
      <div class="card">
        <div class="card-header"><span class="card-title">Breakeven Analysis</span></div>
        <div class="card-body">
          <div style="font-family:var(--mono);font-size:9px;color:var(--muted);letter-spacing:0.1em;text-transform:uppercase;margin-bottom:10px;">Target: 5.0% Yield</div>
          <div class="be-grid" style="margin-bottom:12px;">
            <div class="be-card"><div class="be-lbl">Min Avg Rent/Unit</div><div class="be-val" id="beRent5">—</div></div>
            <div class="be-card"><div class="be-lbl">Max Land Cost</div><div class="be-val" id="beLand5">—</div></div>
            <div class="be-card"><div class="be-lbl">Max TDC / Lot</div><div class="be-val" id="beTDC5">—</div></div>
          </div>
          <div style="font-family:var(--mono);font-size:9px;color:var(--muted);letter-spacing:0.1em;text-transform:uppercase;margin-bottom:10px;">Target: 6.0% Yield</div>
          <div class="be-grid">
            <div class="be-card"><div class="be-lbl">Min Avg Rent/Unit</div><div class="be-val" id="beRent6">—</div></div>
            <div class="be-card"><div class="be-lbl">Max Land Cost</div><div class="be-val" id="beLand6">—</div></div>
            <div class="be-card"><div class="be-lbl">Max TDC / Lot</div><div class="be-val" id="beTDC6">—</div></div>
          </div>
        </div>
      </div>

    </div><!-- /grid-outputs -->
  </div>
</div>

<!-- STATUS BAR -->
<div class="status-bar">
  <div class="status-live"><div class="live-dot"></div><span style="font-family:var(--mono);font-size:9px;color:var(--muted);">LIVE</span></div>
  <div class="status-item">Market: <span id="statusMarket">—</span></div>
  <div class="status-item">Units: <span id="statusUnits">—</span></div>
  <div class="status-item">TDC: <span id="statusTDC">—</span></div>
  <div class="status-item">Yield: <span id="statusYield">—</span></div>
  <div class="source-note">AMH Communities · FL County Impact Fee Schedules 2025 · BTR Comp Database</div>
</div>

<script>
// ═══════════════════════════════════════════════════════
// DATA
// ═══════════════════════════════════════════════════════
const IMPACT_FEES = {
  orange:   { transport:7800,  school:9500,  parks:2434, fire:462, law:665,  utility:8500, name:"Orange County", note:"Effective May 2026. School fee shown for 2,000–2,999 SF avg. Water/sewer via OC Utilities avg." },
  osceola:  { transport:21710, school:12923, parks:2305, fire:780, law:0,    utility:9000, name:"Osceola County", note:"Effective May 19, 2025. Mobility $21,710/SFD — highest total IF in FL. Utility estimated." },
  seminole: { transport:8500,  school:7800,  parks:1600, fire:320, law:350,  utility:8000, name:"Seminole County", note:"Estimated 2025 rates including Seminole County School Board fees." },
  lake:     { transport:9800,  school:8200,  parks:1900, fire:350, law:400,  utility:7500, name:"Lake County", note:"Estimated 2025 rates. Verify current schedule at Lake County Building Services." },
  polk:     { transport:13400, school:6800,  parks:1450, fire:400, law:450,  utility:7000, name:"Polk County", note:"Transport under revision; may increase to ~$22,300. Verify with Polk BCC." },
  volusia:  { transport:5432,  school:2942,  parks:600,  fire:294, law:0,    utility:6000, name:"Volusia County", note:"Published 2021 rates (most recent available). Total SFD unincorporated: ~$9,264 + utility." },
  brevard:  { transport:3500,  school:4200,  parks:700,  fire:250, law:0,    utility:5500, name:"Brevard County", note:"One of the lower impact fee counties in Florida. Verify current rates with Brevard BCC." }
};

const MARKET_RENTS = {
  orange:   { amh3:2200, amh4:2550, amh5:2800, btr3:2150, btr4:2400, btr5:2700, communities:["Binion Reserve (Apopka)","Windward Hills (Apopka)","Leela Reserve (Orlando)","Zarabrooke (Apopka)"] },
  osceola:  { amh3:2175, amh4:2450, amh5:2600, btr3:2100, btr4:2370, btr5:2740, communities:["Sedona (Kissimmee)","Sunbelt (Kissimmee)","Sky Lakes (St. Cloud)","Pine Grove Reserve (St. Cloud)","Sandhill Preserve (St. Cloud)"] },
  seminole: { amh3:2200, amh4:2500, amh5:2750, btr3:2150, btr4:2400, btr5:2650, communities:["Celery Cove (Sanford)","Riverbank Place (Sanford)"] },
  lake:     { amh3:2100, amh4:2300, amh5:2550, btr3:2050, btr4:2250, btr5:2500, communities:["Crestridge at Leesburg","Grafton Ridge (Eustis)","Lake Landing (Tavares)","Roper Trails (Mascotte)"] },
  polk:     { amh3:1950, amh4:2150, amh5:2400, btr3:1900, btr4:2100, btr5:2350, communities:["AMH scattered Lakeland / Winter Haven submarket"] },
  volusia:  { amh3:1900, amh4:2150, amh5:2400, btr3:1850, btr4:2050, btr5:2300, communities:["Limited AMH presence — DeLand / Deltona BTR comps"] },
  brevard:  { amh3:1950, amh4:2200, amh5:2450, btr3:1900, btr4:2150, btr5:2400, communities:["Viera / Melbourne BTR market"] }
};

const CONSTRUCTION = {
  raw:      { horiz:55000, vert:120 },
  entitled: { horiz:45000, vert:118 },
  devlots:  { horiz:0,     vert:115 }
};

// ═══════════════════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════════════════
const overrides={}, feeOverrides={}, rentOverrides={};
let opexLocked = true;

function markOverride(id){ overrides[id]=true; document.getElementById(id).classList.remove('auto-field'); document.getElementById(id).classList.add('overridden'); }
function markFeeOverride(id){ feeOverrides[id]=true; document.getElementById(id).classList.add('overridden'); }
function markRentOverride(id){ rentOverrides[id]=true; document.getElementById(id).classList.add('overridden'); }

// ═══════════════════════════════════════════════════════
// OPEX LOCK TOGGLE
// ═══════════════════════════════════════════════════════
function toggleOpexLock(){
  opexLocked = !opexLocked;
  const sec = document.getElementById('opexSection');
  const btn = document.getElementById('opexLockBtn');
  if(opexLocked){
    sec.classList.remove('opex-unlocked'); sec.classList.add('opex-locked');
    btn.textContent = 'Override';
    document.querySelectorAll('#sliderVacancy,#sliderMgmt').forEach(s=>s.classList.add('locked'));
  } else {
    sec.classList.remove('opex-locked'); sec.classList.add('opex-unlocked');
    btn.textContent = 'Lock Values';
    document.querySelectorAll('#sliderVacancy,#sliderMgmt').forEach(s=>s.classList.remove('locked'));
  }
}

// ═══════════════════════════════════════════════════════
// FORMULA MODAL
// ═══════════════════════════════════════════════════════
function openFormula(){ document.getElementById('formulaModal').classList.add('open'); }
function closeFormula(){ document.getElementById('formulaModal').classList.remove('open'); }
document.getElementById('formulaModal').addEventListener('click', function(e){ if(e.target===this) closeFormula(); });

// ═══════════════════════════════════════════════════════
// COUNTY / STATUS CHANGE
// ═══════════════════════════════════════════════════════
function onCountyChange(){
  const c = document.getElementById('county').value;
  if(!c) return;
  applyImpactFees(c);
  applyRents(c);
  applyConstruction(document.getElementById('devStatus').value);
  recalc();
}
function onDevStatusChange(){
  applyConstruction(document.getElementById('devStatus').value);
  recalc();
}

function applyImpactFees(c){
  if(!IMPACT_FEES[c]) return;
  const f=IMPACT_FEES[c];
  const map={if_transport:f.transport,if_school:f.school,if_parks:f.parks,if_fire:f.fire,if_law:f.law,if_utility:f.utility};
  Object.entries(map).forEach(([id,v])=>{ if(!feeOverrides[id]){ document.getElementById(id).value=v; document.getElementById(id).classList.remove('overridden'); }});
  document.getElementById('ifSource').textContent = f.name;
  document.getElementById('ifNote').textContent = f.note;
  updateFeeTotal();
}

function applyRents(c){
  if(!MARKET_RENTS[c]) return;
  const r=MARKET_RENTS[c];
  document.getElementById('amh3').textContent = '$'+r.amh3.toLocaleString();
  document.getElementById('amh4').textContent = '$'+r.amh4.toLocaleString();
  document.getElementById('amh5').textContent = '$'+r.amh5.toLocaleString();
  document.getElementById('btr3').textContent = '$'+r.btr3.toLocaleString();
  document.getElementById('btr4').textContent = '$'+r.btr4.toLocaleString();
  document.getElementById('btr5').textContent = '$'+r.btr5.toLocaleString();
  if(!rentOverrides['rent3']) document.getElementById('rent3').value=r.amh3;
  if(!rentOverrides['rent4']) document.getElementById('rent4').value=r.amh4;
  if(!rentOverrides['rent5']) document.getElementById('rent5').value=r.amh5;
  document.getElementById('rentSource').textContent = IMPACT_FEES[c]?.name || c;
  document.getElementById('rentNote').textContent = 'AMH communities in submarket: '+r.communities.join(', ')+'.';
}

function applyConstruction(s){
  if(!CONSTRUCTION[s]) return;
  const c=CONSTRUCTION[s];
  const hs=document.getElementById('horizSection');
  hs.style.opacity = s==='devlots'?'0.35':'1';
  if(!overrides['horizCost']){ const el=document.getElementById('horizCost'); el.value=c.horiz; el.className='auto-field'; }
  if(!overrides['vertCost']){ const el=document.getElementById('vertCost'); el.value=c.vert; el.className='auto-field'; }
}

function resetConstruction(){
  delete overrides['horizCost']; delete overrides['vertCost'];
  applyConstruction(document.getElementById('devStatus').value); recalc();
}
function resetImpactFees(){
  ['if_transport','if_school','if_parks','if_fire','if_law','if_utility'].forEach(id=>{ delete feeOverrides[id]; document.getElementById(id).classList.remove('overridden'); });
  applyImpactFees(document.getElementById('county').value); recalc();
}

// ═══════════════════════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════════════════════
function fmt(n){ if(!n&&n!==0||isNaN(n)) return '—'; return '$'+Math.round(n).toLocaleString(); }
function fmtM(n){ if(!n||isNaN(n)) return '—'; return '$'+(n/1e6).toFixed(2)+'M'; }
function fmtP(n){ if(!n||isNaN(n)) return '—'; return n.toFixed(2)+'%'; }
function pctOf(a,b){ if(!b) return '—'; return (a/b*100).toFixed(1)+'%'; }
function gv(id){ return parseFloat(document.getElementById(id).value)||0; }

function updateFeeTotal(){
  const t=['if_transport','if_school','if_parks','if_fire','if_law','if_utility'].reduce((s,id)=>s+(parseFloat(document.getElementById(id).value)||0),0);
  document.getElementById('ifTotal').textContent='$'+Math.round(t).toLocaleString();
  return t;
}

// ═══════════════════════════════════════════════════════
// MAIN RECALC
// ═══════════════════════════════════════════════════════
function recalc(){
  updateFeeTotal();
  const N=gv('totalUnits'); if(N<=0) return;
  const u3=gv('units3'),u4=gv('units4'),u5=gv('units5');
  const sf3=gv('sf3'),sf4=gv('sf4'),sf5=gv('sf5');
  const mix=u3+u4+u5;
  document.getElementById('pct3').textContent=mix?(u3/mix*100).toFixed(0)+'%':'—';
  document.getElementById('pct4').textContent=mix?(u4/mix*100).toFixed(0)+'%':'—';
  document.getElementById('pct5').textContent=mix?(u5/mix*100).toFixed(0)+'%':'—';

  // COSTS
  const land=gv('landPrice');
  const horiz=gv('horizCost')*N;
  const vert=(u3*sf3+u4*sf4+u5*sf5)*gv('vertCost');
  const amenity=gv('amenityBudget');
  const hard=horiz+vert+amenity;
  const soft=hard*(gv('softPct')/100);
  const cont=hard*(gv('contPct')/100);
  const ifee=updateFeeTotal();
  const ifeeTotal=ifee*N;
  const hsc=hard+soft+cont;
  const tdc=land+hsc+ifeeTotal;
  const tdcPL=tdc/N;

  // INCOME
  const r3=gv('rent3'),r4=gv('rent4'),r5=gv('rent5');
  const avgR=N>0?(u3*r3+u4*r4+u5*r5)/N:0;
  const gpr=(u3*r3+u4*r4+u5*r5)*12;
  const vac=gv('vacancy')/100;
  const egi=gpr*(1-vac);
  const mgmtP=gv('mgmt')/100;
  const taxes=gv('taxes')*N, ins=gv('insurance')*N, rep=gv('repairs')*N;
  const cpx=gv('capex')*N, adm=gv('admin')*N, lse=gv('leasing')*N;
  const mgmt=egi*mgmtP;
  const opex=mgmt+taxes+ins+rep+cpx+adm+lse;
  const noi=egi-opex;
  const yoc=tdc>0?noi/tdc*100:0;
  const exitCap=gv('exitCap')/100;
  const exitVal=exitCap>0?noi/exitCap:0;

  // KPIs
  document.getElementById('kpiYield').textContent=yoc>0?fmtP(yoc):'—';
  const yPct=Math.min(Math.max((yoc-3)/4*100,0),100);
  document.getElementById('yieldPip').style.left=yPct+'%';
  document.getElementById('kpiNOI').textContent=noi>0?fmtM(noi):'—';
  document.getElementById('kpiNOIsub').textContent=noi>0?fmt(noi/N)+' / lot / yr':'Net operating income';
  document.getElementById('kpiTDC').textContent=tdc>0?fmtM(tdc):'—';
  document.getElementById('kpiTDCsub').textContent=tdc>0?fmt(tdcPL)+' / lot':'All-in cost';
  document.getElementById('kpiCPL').textContent=tdcPL>0?fmt(tdcPL):'—';
  document.getElementById('kpiEGI').textContent=egi>0?fmtM(egi):'—';
  document.getElementById('kpiEGIsub').textContent=egi>0?fmt(avgR)+' avg rent / unit':'Annual EGI';
  document.getElementById('kpiExit').textContent=exitVal>0?fmtM(exitVal):'—';
  document.getElementById('kpiExitsub').textContent=exitVal>0?fmt(exitVal/N)+' / lot':'NOI ÷ exit cap';

  const proj=document.getElementById('projectName').value||'Untitled';
  const date=document.getElementById('analysisDate').value;
  document.getElementById('pfHeader').textContent=proj+' · '+date+' · '+N+' Lots';

  // PROFORMA ROWS
  const devS=document.getElementById('devStatus').value;
  const rows=[];
  rows.push({t:'sec',l:'Total Development Cost'});
  rows.push({l:'Land Cost',pu:land/N,tot:land});
  if(gv('horizCost')>0&&devS!=='devlots') rows.push({l:'Horizontal / Site Development',pu:horiz/N,tot:horiz,ind:true});
  rows.push({l:'Vertical Construction',pu:vert/N,tot:vert,ind:true});
  rows.push({l:'Amenity Budget',pu:amenity/N,tot:amenity,ind:true});
  rows.push({l:'Soft Costs ('+gv('softPct')+'% of hard)',pu:soft/N,tot:soft,ind:true});
  rows.push({l:'Contingency ('+gv('contPct')+'%)',pu:cont/N,tot:cont,ind:true});
  rows.push({l:'Impact Fees',pu:ifee,tot:ifeeTotal,ind:true});
  rows.push({t:'sub',l:'Total Development Cost',pu:tdcPL,tot:tdc});
  rows.push({t:'sp'});
  rows.push({t:'sec',l:'Stabilized Income'});
  rows.push({l:'3 BR — '+u3+' units × $'+r3.toLocaleString()+' / mo',pu:r3*12,tot:u3*r3*12,ind:true});
  rows.push({l:'4 BR — '+u4+' units × $'+r4.toLocaleString()+' / mo',pu:r4*12,tot:u4*r4*12,ind:true});
  rows.push({l:'5 BR — '+u5+' units × $'+r5.toLocaleString()+' / mo',pu:r5*12,tot:u5*r5*12,ind:true});
  rows.push({l:'Gross Potential Rent (GPR)',pu:gpr/N,tot:gpr});
  rows.push({l:'Vacancy ('+gv('vacancy')+'%)',pu:-gpr*vac/N,tot:-gpr*vac,neg:true,ind:true});
  rows.push({t:'sub',l:'Effective Gross Income (EGI)',pu:egi/N,tot:egi});
  rows.push({t:'sp'});
  rows.push({t:'sec',l:'Operating Expenses'});
  rows.push({l:'Property Management ('+gv('mgmt')+'% of EGI)',pu:mgmt/N,tot:mgmt,neg:true,ind:true});
  rows.push({l:'Property Taxes',pu:gv('taxes'),tot:taxes,neg:true,ind:true});
  rows.push({l:'Insurance',pu:gv('insurance'),tot:ins,neg:true,ind:true});
  rows.push({l:'Repairs & Maintenance',pu:gv('repairs'),tot:rep,neg:true,ind:true});
  rows.push({l:'CapEx Reserve',pu:gv('capex'),tot:cpx,neg:true,ind:true});
  rows.push({l:'Admin / HOA',pu:gv('admin'),tot:adm,neg:true,ind:true});
  rows.push({l:'Leasing',pu:gv('leasing'),tot:lse,neg:true,ind:true});
  rows.push({t:'sub',l:'Total Operating Expenses',pu:-opex/N,tot:-opex,neg:true});
  rows.push({t:'fin',l:'Net Operating Income (NOI)',pu:noi/N,tot:noi});
  rows.push({t:'yoc',l:'Yield on Cost',pct:fmtP(yoc)});

  let h='';
  rows.forEach(r=>{
    if(r.t==='sp'){h+='<tr><td colspan="4" style="height:5px;"></td></tr>';return;}
    if(r.t==='sec'){h+=`<tr class="pf-section"><td colspan="4">${r.l}</td></tr>`;return;}
    if(r.t==='yoc'){h+=`<tr class="pf-final"><td>${r.l}</td><td colspan="2" class="num" style="font-size:15px;">${r.pct}</td><td class="num">—</td></tr>`;return;}
    if(r.t==='fin'){const nc=r.tot<0?'neg-c':'pos-c';h+=`<tr class="pf-final"><td>${r.l}</td><td class="num ${nc}">${fmt(r.pu)}</td><td class="num ${nc}">${fmtM(r.tot)}</td><td class="num">—</td></tr>`;return;}
    if(r.t==='sub'){h+=`<tr class="pf-subtotal"><td>${r.l}</td><td class="num">${fmt(r.pu)}</td><td class="num">${fmtM(r.tot)}</td><td class="num">${pctOf(r.tot,tdc)}</td></tr>`;return;}
    const ind=r.ind?' pf-indent':'';const nc=r.neg?' neg-c':'';
    const puS=r.neg?fmt(-Math.abs(r.pu)):fmt(r.pu);
    const totS=Math.abs(r.tot||0)>1e6?fmtM(r.tot):fmt(r.tot);
    const c4=(!r.neg&&r.tot)?pctOf(r.tot,tdc):'—';
    h+=`<tr class="${ind}"><td>${r.l}</td><td class="num${nc}">${puS}</td><td class="num${nc}">${totS}</td><td class="num">${c4}</td></tr>`;
  });
  document.getElementById('pfBody').innerHTML=h;

  // WATERFALL
  const wItems=[
    {l:'Land',v:land/N,g:'#aaa'},
    {l:'Horizontal',v:devS!=='devlots'?gv('horizCost'):0,g:'#888'},
    {l:'Vertical',v:vert/N,g:'#ccc'},
    {l:'Amenities',v:amenity/N,g:'#777'},
    {l:'Soft Costs',v:soft/N,g:'#555'},
    {l:'Impact Fees',v:ifee,g:'#666'},
    {l:'Contingency',v:cont/N,g:'#444'},
  ].filter(w=>w.v>0);
  const maxW=Math.max(...wItems.map(w=>w.v));
  let wh=wItems.map(w=>`<div class="wf-row"><div class="wf-lbl">${w.l}</div><div class="wf-track"><div class="wf-bar" style="width:${Math.max(w.v/maxW*100,1.5)}%;background:${w.g};"></div></div><div class="wf-val">${fmt(w.v)}</div></div>`).join('');
  wh+=`<div class="wf-row wf-total" style="margin-top:8px;padding-top:8px;border-top:1px solid var(--border);"><div class="wf-lbl">Total / Lot</div><div class="wf-track"><div class="wf-bar" style="width:100%;background:var(--white);"></div></div><div class="wf-val">${fmt(tdcPL)}</div></div>`;
  document.getElementById('waterfall').innerHTML=wh;

  // SENSITIVITY
  const bR3=r3,bR4=r4,bR5=r5,bLP=land;
  const opfixed=taxes+ins+rep+cpx+adm+lse;
  function calcY(rf,lf){
    const l2=bLP*(1+lf/100),r32=bR3*(1+rf/100),r42=bR4*(1+rf/100),r52=bR5*(1+rf/100);
    const gpr2=(u3*r32+u4*r42+u5*r52)*12,egi2=gpr2*(1-vac);
    const n2=egi2-egi2*mgmtP-opfixed,t2=l2+hsc+ifeeTotal;
    return t2>0?n2/t2*100:0;
  }
  const rD=[-10,-5,0,5,10],lD=[-20,-10,0,10,20];
  let st=`<table class="sens-table"><thead><tr><th>Rent / Land</th>`;
  rD.forEach(r=>st+=`<th>${r>0?'+':''}${r}% Rent</th>`);
  st+=`</tr></thead><tbody>`;
  lD.forEach(ld=>{
    st+=`<tr><td>${ld>0?'+':''}${ld}% Land</td>`;
    rD.forEach(rd=>{
      const y=calcY(rd,ld);
      const isCur=rd===0&&ld===0;
      const cls=isCur?'s-cur':y>=6?'s-hi':y>=5?'s-mid':y>=4?'s-lo':'s-vlo';
      st+=`<td class="${cls}">${y.toFixed(2)}%</td>`;
    });
    st+=`</tr>`;
  });
  st+=`</tbody></table><div class="sens-legend">White = 6%+  &nbsp;|&nbsp;  Mid = 5–6%  &nbsp;|&nbsp;  Dim = 4–5%  &nbsp;|&nbsp;  Dark = below 4%  &nbsp;|&nbsp;  <strong style="color:#fff;">Bold = current assumptions</strong></div>`;
  document.getElementById('sensWrap').innerHTML=st;

  // BREAKEVEN
  function minRent(ty){ const rNOI=tdc*(ty/100),rEGI=(rNOI+opfixed)/(1-mgmtP),rGPR=rEGI/(1-vac); return rGPR/N/12; }
  function maxLand(ty){ if(noi<=0) return 0; return noi/(ty/100)-(hsc+ifeeTotal); }
  function maxTDCPL(ty){ if(noi<=0) return 0; return (noi/(ty/100))/N; }
  document.getElementById('beRent5').textContent=fmt(minRent(5));
  document.getElementById('beLand5').textContent=fmtM(maxLand(5));
  document.getElementById('beTDC5').textContent=fmt(maxTDCPL(5));
  document.getElementById('beRent6').textContent=fmt(minRent(6));
  document.getElementById('beLand6').textContent=fmtM(maxLand(6));
  document.getElementById('beTDC6').textContent=fmt(maxTDCPL(6));

  // STATUS
  const cOpt=document.getElementById('county').options[document.getElementById('county').selectedIndex];
  document.getElementById('statusMarket').textContent=cOpt?.value?cOpt.text:'—';
  document.getElementById('statusUnits').textContent=N;
  document.getElementById('statusTDC').textContent=fmtM(tdc);
  document.getElementById('statusYield').textContent=yoc>0?fmtP(yoc):'—';
}

// ═══════════════════════════════════════════════════════
// LIVE RENT FETCH
// ═══════════════════════════════════════════════════════
async function fetchLiveRents(){
  const c=document.getElementById('county').value;
  if(!c){alert('Please select a county first.');return;}
  const cn=IMPACT_FEES[c]?.name||c;
  const btn=document.getElementById('fetchRentsBtn');
  btn.textContent='Fetching...'; btn.disabled=true;
  const panel=document.getElementById('aiPanel'); panel.classList.add('open');
  document.getElementById('aiContent').innerHTML='<div style="text-align:center;padding:12px;"><span class="ai-dot"></span><span class="ai-dot"></span><span class="ai-dot"></span></div>';
  const prompt=`You are a BTR real estate analyst. Search for current 2025 market rent data for ${cn}, Florida.\n\nFind:\n1. AMH (American Homes 4 Rent) community rents in this submarket for 3BR, 4BR, 5BR homes. List community names.\n2. Other BTR operator rents (Invitation Homes, Tricon, local) in the same submarket.\n3. Year-over-year rent trend.\n4. Notable new BTR supply pipeline.\n\nBe concise. Include specific rent ranges where possible (e.g. "$2,150–$2,350 for 3BR"). End with a table:\nBedroom Type | AMH Rent Range | BTR Market Range | Recommended Baseline`;
  try{
    const res=await fetch("https://api.anthropic.com/v1/messages",{method:"POST",headers:{"Content-Type":"application/json"},body:JSON.stringify({model:"claude-sonnet-4-20250514",max_tokens:1000,tools:[{type:"web_search_20250305",name:"web_search"}],messages:[{role:"user",content:prompt}]})});
    const data=await res.json();
    const text=data.content.filter(b=>b.type==='text').map(b=>b.text).join('\n');
    const m3=text.match(/3.?BR.*?\$(\d{1,2},?\d{3})/i),m4=text.match(/4.?BR.*?\$(\d{1,2},?\d{3})/i),m5=text.match(/5.?BR.*?\$(\d{1,2},?\d{3})/i);
    if(m3){const v=parseInt(m3[1].replace(/,/g,''));if(v>500&&v<9999&&!rentOverrides['rent3'])document.getElementById('rent3').value=v;}
    if(m4){const v=parseInt(m4[1].replace(/,/g,''));if(v>500&&v<9999&&!rentOverrides['rent4'])document.getElementById('rent4').value=v;}
    if(m5){const v=parseInt(m5[1].replace(/,/g,''));if(v>500&&v<9999&&!rentOverrides['rent5'])document.getElementById('rent5').value=v;}
    document.getElementById('aiContent').innerHTML=`<p>${text.replace(/\n/g,'<br>')}</p>`;
    recalc();
  }catch(e){
    document.getElementById('aiContent').innerHTML=`<p style="color:var(--muted);">Fetch error: ${e.message}. Using database values.</p>`;
  }
  btn.textContent='Fetch Live Rents'; btn.disabled=false;
}

// ═══════════════════════════════════════════════════════
// CSV EXPORT
// ═══════════════════════════════════════════════════════
function exportCSV(){
  const c=document.getElementById('county').value;
  const proj=document.getElementById('projectName').value||'BTR Proforma';
  const rows=[
    ['BTR Proforma Yield Calculator'],
    ['Project',proj],['County',IMPACT_FEES[c]?.name||'—'],['Date',document.getElementById('analysisDate').value],
    [''],['=== DEAL ==='],
    ['Land Price',document.getElementById('landPrice').value],
    ['Total Lots',document.getElementById('totalUnits').value],
    ['Dev Status',document.getElementById('devStatus').options[document.getElementById('devStatus').selectedIndex].text],
    [''],['Plan','Units','SF','Rent/mo'],
    ['3 BR',document.getElementById('units3').value,document.getElementById('sf3').value,document.getElementById('rent3').value],
    ['4 BR',document.getElementById('units4').value,document.getElementById('sf4').value,document.getElementById('rent4').value],
    ['5 BR',document.getElementById('units5').value,document.getElementById('sf5').value,document.getElementById('rent5').value],
    [''],['=== IMPACT FEES (Per Lot) ==='],
    ['Transportation',document.getElementById('if_transport').value],['School',document.getElementById('if_school').value],
    ['Parks',document.getElementById('if_parks').value],['Fire',document.getElementById('if_fire').value],
    ['Law',document.getElementById('if_law').value],['Utility',document.getElementById('if_utility').value],
    [''],['=== RESULTS ==='],
    ['Yield on Cost',document.getElementById('kpiYield').textContent],
    ['NOI Annual',document.getElementById('kpiNOI').textContent],
    ['Total Dev Cost',document.getElementById('kpiTDC').textContent],
    ['Cost Per Lot',document.getElementById('kpiCPL').textContent],
    ['Est Exit Value',document.getElementById('kpiExit').textContent],
  ];
  const csv=rows.map(r=>r.map(c=>`"${c||''}"`).join(',')).join('\n');
  const a=document.createElement('a');
  a.href=URL.createObjectURL(new Blob([csv],{type:'text/csv'}));
  a.download=(proj.replace(/\s+/g,'_'))+'_'+new Date().toISOString().slice(0,10)+'.csv';
  a.click();
}

// ═══════════════════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════════════════
(function init(){
  const now=new Date();
  const months=['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  document.getElementById('analysisDate').value=months[now.getMonth()]+' '+now.getFullYear();
  // Apply initial opex lock state
  document.getElementById('opexSection').classList.add('opex-locked');
  document.querySelectorAll('#sliderVacancy,#sliderMgmt').forEach(s=>s.classList.add('locked'));
  recalc();
})();
</script>
</body>
</html>
