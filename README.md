<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>NFI Sumner — Market Intelligence Dashboard | Savills</title>
<link href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" rel="stylesheet"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
:root{--Y:#FFDF00;--N:#25273A;--T:#008C95;--K:#15182B;--M:#5D6677;--L:#DDE1E7;--S:#F3F4F6;--G:#1d7a5f;--R:#c0392b;--B:#2c4fa3;--A:#e67e22}
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Segoe UI',Arial,sans-serif;color:var(--K);background:#f0f2f5;font-size:14px}
button{font-family:inherit;cursor:pointer}
/* HEADER */
.hdr{background:var(--N);color:#fff;display:flex;align-items:center;padding:0 28px;height:88px;border-bottom:5px solid var(--Y);position:sticky;top:0;z-index:9000;gap:20px}
.sav-logo{background:var(--Y);color:#e2382c;font-size:20px;font-weight:900;width:72px;height:52px;display:flex;align-items:flex-end;justify-content:center;padding-bottom:8px;border-radius:3px;flex-shrink:0}
.nfi-logo{background:#fff;color:var(--N);font-weight:900;font-size:17px;width:68px;height:52px;display:grid;place-items:center;border-radius:3px;flex-shrink:0}
.vdiv{width:1px;height:44px;background:rgba(255,255,255,.2);flex-shrink:0}
.hdr-eye{font-size:10px;font-weight:700;letter-spacing:.25em;text-transform:uppercase;color:var(--Y);margin-bottom:3px}
.hdr-ttl{font-family:Georgia,serif;font-size:26px;font-weight:400;line-height:1.1}
.hdr-sub{font-size:12px;color:rgba(255,255,255,.6);margin-top:4px}
.hdr-meta{text-align:right;font-size:11px;color:rgba(255,255,255,.5);line-height:1.6;flex-shrink:0}
.hdr-meta b{color:rgba(255,255,255,.85);display:block;font-size:12px}
/* SHELL */
.shell{display:flex;min-height:calc(100vh - 88px)}
.sidebar{width:300px;flex-shrink:0;background:#fff;border-right:1px solid var(--L);position:sticky;top:88px;height:calc(100vh - 88px);overflow-y:auto;padding:16px 12px}
.sidebar::-webkit-scrollbar{width:5px}.sidebar::-webkit-scrollbar-thumb{background:#cbd0d8;border-radius:10px}
.main{flex:1;padding:24px 28px 60px;overflow:auto}
/* NAV */
.nav-lbl{font-size:10px;font-weight:700;letter-spacing:.2em;text-transform:uppercase;color:var(--M);padding:14px 8px 6px;display:block}
.nav-btn{width:100%;border:1px solid transparent;background:transparent;border-radius:8px;padding:9px 12px;margin-bottom:3px;font-size:13px;font-weight:600;text-align:left;color:var(--N);display:flex;align-items:center;gap:8px;transition:all .15s}
.nav-btn:hover{background:var(--S)}.nav-btn.active{background:var(--N);color:#fff;border-color:var(--N)}
.nav-btn .icon{font-size:15px;width:20px;text-align:center}
.ndiv{height:1px;background:var(--L);margin:8px 0}
/* MAP CARDS */
.mcard{display:flex;align-items:flex-start;gap:9px;width:100%;border:1px solid var(--L);background:#fff;border-radius:10px;padding:9px;margin-bottom:6px;text-align:left;transition:all .15s}
.mcard:hover,.mcard.act{border-color:var(--T);box-shadow:0 3px 12px rgba(0,140,149,.15);background:#f0fafa}
.mcard.anc:hover,.mcard.anc.act{border-color:var(--A);background:#fffaf0}
.mcard b{display:block;font-size:11.5px;line-height:1.3;color:var(--N)}
.mcard span{display:block;color:var(--M);font-size:10.5px;margin-top:2px;line-height:1.3}
.pin{min-width:26px;height:26px;border-radius:50%;background:var(--B);color:#fff;display:grid;place-items:center;font-weight:700;font-size:10px;flex-shrink:0}
.pin.c{background:var(--T)}.pin.a{background:var(--A)}.pin.s{background:var(--R)}
/* SECTIONS */
.section{display:none}.section.active{display:block}
/* CARDS */
.card{background:#fff;border:1px solid var(--L);border-radius:16px;padding:20px;box-shadow:0 4px 20px rgba(0,0,0,.07)}
.card-hd{background:var(--N);color:#fff;border-radius:16px 16px 0 0;padding:18px 22px;border-bottom:4px solid var(--Y);margin:-20px -20px 20px}
.eye{font-size:10px;font-weight:700;letter-spacing:.2em;text-transform:uppercase;color:var(--Y)}
.card-hd h2{font-family:Georgia,serif;font-size:28px;font-weight:400;margin-top:5px;line-height:1.1}
.card-hd p{font-size:12px;color:rgba(255,255,255,.6);margin-top:5px}
.shead{margin-bottom:20px}.shead .eye{color:var(--T)}
.shead h2{font-family:Georgia,serif;font-size:30px;font-weight:400;color:var(--N);margin:5px 0}
.shead p{color:var(--M);font-size:13px}
h3{font-size:16px;font-weight:700;color:var(--N);margin-bottom:10px}
h4{font-size:13px;font-weight:700;color:var(--N);margin-bottom:6px}
p{line-height:1.55;color:#3a3d4e}
.grid{display:grid;gap:16px}
.g2{grid-template-columns:1fr 1fr}.g3{grid-template-columns:repeat(3,1fr)}.g4{grid-template-columns:repeat(4,1fr)}.g5{grid-template-columns:repeat(5,1fr)}
/* METRICS */
.met{border-left:5px solid var(--Y);padding:14px 16px;background:#fff;border-radius:10px;box-shadow:0 4px 20px rgba(0,0,0,.07)}
.met .num{font-size:26px;font-weight:800;color:var(--N);line-height:1}
.met .lbl{font-size:10px;text-transform:uppercase;font-weight:700;letter-spacing:.1em;color:var(--M);margin-top:5px}
.met .sub{font-size:11px;color:var(--M);margin-top:4px;line-height:1.3}
.met.gr{border-color:var(--G)}.met.rd{border-color:var(--R)}.met.tl{border-color:var(--T)}.met.bl{border-color:var(--B)}
/* CALLOUTS */
.co{border-radius:10px;padding:14px 16px;margin-bottom:14px;line-height:1.55;font-size:13px}
.co b{color:var(--N)}
.co-y{background:#fffde7;border:1px solid #f0d000}
.co-t{background:#e0f5f5;border:1px solid var(--T)}
.co-r{background:#fdecea;border:1px solid #e57373}
.co-b{background:#e8eef8;border:1px solid #90a4d4}
/* BADGES */
.bdg{display:inline-block;border-radius:999px;padding:3px 10px;font-size:11px;font-weight:700;margin:2px}
.b-new{background:#dff0eb;color:#1d7a5f}.b-ren{background:#e8eef8;color:var(--B)}.b-amd{background:#fef3e2;color:#b7620c}
.b-sub{background:#fdecea;color:var(--R)}.b-dir{background:#dff0eb;color:#1d7a5f}.b-ref{background:#fef3e2;color:#b7620c}
/* TABLES */
table{width:100%;border-collapse:collapse;font-size:12px}
thead th{background:var(--N);color:#fff;text-align:left;padding:9px 10px;font-weight:600;font-size:11px;letter-spacing:.04em}
tbody td{border-bottom:1px solid var(--L);padding:9px 10px;vertical-align:top;color:#3a3d4e}
tbody tr:hover td{background:var(--S)}.ta{font-weight:600;color:var(--N)}.tm{color:var(--M);font-size:11px}
.rh{color:var(--R);font-weight:700}.rm{color:var(--A);font-weight:700}.rl{color:var(--G);font-weight:700}
/* MAPS */
#optionsMap,#compsMap{height:500px;border-radius:12px;border:1px solid var(--L)}
.map-layout{display:grid;grid-template-columns:290px 1fr;gap:16px;align-items:start}
/* DETAIL PANEL */
.dh{background:var(--N);color:#fff;border-radius:10px;padding:14px 16px;margin-bottom:14px;border-left:5px solid var(--Y)}
.dh.am{border-left-color:var(--A)}.dh.tl{border-left-color:var(--T)}
.dh h4{color:#fff;font-size:15px;margin:0 0 4px}.dh p{color:rgba(255,255,255,.65);font-size:11px;margin:0}
.dgrid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:14px}
.dlbl{font-size:10px;font-weight:700;letter-spacing:.1em;text-transform:uppercase;color:var(--M);margin-bottom:3px}
.dval{font-size:14px;font-weight:700;color:var(--N)}.dsub{font-size:11px;color:var(--M)}
/* CHARTS */
.cw{position:relative;height:320px}.cwt{position:relative;height:420px}
/* STEPS */
.step{display:flex;gap:14px;margin-bottom:16px;padding-bottom:16px;border-bottom:1px solid var(--L)}
.step:last-child{border-bottom:none;margin-bottom:0;padding-bottom:0}
.snum{min-width:32px;height:32px;border-radius:50%;background:var(--N);color:#fff;display:grid;place-items:center;font-weight:800;font-size:14px;flex-shrink:0}
/* REPORT IMAGES */
img.rpg{width:100%;border:1px solid var(--L);border-radius:10px;cursor:zoom-in;display:block;transition:box-shadow .2s}
img.rpg:hover{box-shadow:0 8px 24px rgba(0,0,0,.2)}
.sav-bdg{background:var(--N);color:var(--Y);font-weight:900;font-size:12px;padding:4px 12px;border-radius:6px;display:inline-block}
</style>
</head>
<body>

<header class="hdr">
  <div class="sav-logo">Savills</div>
  <div class="nfi-logo">NFI</div>
  <div class="vdiv"></div>
  <div style="flex:1">
    <div class="hdr-eye">Market Intelligence Dashboard</div>
    <div class="hdr-ttl">NFI Sumner — Lease Strategy</div>
    <div class="hdr-sub">4301 West Valley Highway East &middot; Sumner, WA 98390</div>
  </div>
  <div class="hdr-meta"><b>Savills</b>John Leskow &middot; Ian O'Brien<br>Updated June 1, 2026</div>
</header>

<div class="shell">
<aside class="sidebar">
  <span class="nav-lbl">Navigation</span>
  <button class="nav-btn active" onclick="showSection('summary')" data-sec="summary"><span class="icon">&#128203;</span>Executive Summary</button>
  <button class="nav-btn" onclick="showSection('market')" data-sec="market"><span class="icon">&#128202;</span>Market Overview</button>
  <button class="nav-btn" onclick="showSection('options')" data-sec="options"><span class="icon">&#128506;</span>Options Map</button>
  <button class="nav-btn" onclick="showSection('comps')" data-sec="comps"><span class="icon">&#128209;</span>Lease Comps</button>
  <button class="nav-btn" onclick="showSection('pricing')" data-sec="pricing"><span class="icon">&#128176;</span>Pricing Landscape</button>
  <button class="nav-btn" onclick="showSection('research')" data-sec="research"><span class="icon">&#128240;</span>Savills Research</button>
  <button class="nav-btn" onclick="showSection('approach')" data-sec="approach"><span class="icon">&#127919;</span>Recommended Approach</button>
  <div class="ndiv"></div>
  <div id="sb-opts" style="display:none">
    <span class="nav-lbl">Relocation Options</span>
    <div>
      <button class="mcard" onclick="selectOption('O1')"><div class="pin">O1</div><div><b>Fife Commerce Center</b><span>2511 70th Ave E &middot; 255,070 SF &middot; $0.78/mo</span></div></button>
      <button class="mcard" onclick="selectOption('O2')"><div class="pin">O2</div><div><b>Pioneer Logistics Center</b><span>14021 E Pioneer Way &middot; 211,153 SF &middot; $0.86/mo</span></div></button>
      <button class="mcard" onclick="selectOption('O3')"><div class="pin">O3</div><div><b>Bridge Point i5 &mdash; Bldg 2</b><span>80 5th Ave, Milton &middot; 239&ndash;478K SF &middot; $1.25/mo</span></div></button>
      <button class="mcard" onclick="selectOption('O4')"><div class="pin">O4</div><div><b>LogistiCenter at 167</b><span>26th St E, Fife &middot; 225,972 SF &middot; $0.86/mo</span></div></button>
      <button class="mcard" onclick="selectOption('O5')"><div class="pin">O5</div><div><b>Sumner Central</b><span>142nd Ave E &middot; 249K now / 427K Aug &lsquo;26</span></div></button>
      <button class="mcard" onclick="selectOption('O6')"><div class="pin">O6</div><div><b>Norwesco Building</b><span>3011 70th Ave E, Fife &middot; 323,967 SF</span></div></button>
      <button class="mcard" onclick="selectOption('O7')"><div class="pin">O7</div><div><b>Portside Distribution Center</b><span>1401 St. Paul Ave, Tacoma &middot; 325,000 SF</span></div></button>
      <button class="mcard" onclick="selectOption('O8')"><div class="pin">O8</div><div><b>Prologis Blair Distribution Ctr.</b><span>2340 Taylor Way, Tacoma &middot; 205,121 SF</span></div></button>
    </div>
    <span class="nav-lbl">Market Rate References</span>
    <div>
      <button class="mcard anc" onclick="selectOption('A1')"><div class="pin a">A1</div><div><b>Fife Commerce Center &mdash; Sublease</b><span>125&ndash;255K SF &middot; $0.72/mo &middot; Market reference</span></div></button>
      <button class="mcard anc" onclick="selectOption('A2')"><div class="pin a">A2</div><div><b>Fife I-5 Commerce Center</b><span>250,490 SF &middot; $0.65/mo &middot; Market reference</span></div></button>
    </div>
  </div>
  <div id="sb-comps" style="display:none">
    <span class="nav-lbl">Market Comps</span>
    <div>
      <button class="mcard" onclick="selectComp('C1')"><div class="pin c">C1</div><div><b>Crane Worldwide Logistics</b><span>3101 W Valley Hwy E &middot; 264,344 SF &middot; $0.92/mo</span></div></button>
      <button class="mcard" onclick="selectComp('C2')"><div class="pin c">C2</div><div><b>Western Container</b><span>2205 70th Ave E, Fife &middot; 202,250 SF &middot; $0.97/mo</span></div></button>
      <button class="mcard" onclick="selectComp('C3')"><div class="pin c">C3</div><div><b>Tigers (USA) Global Logistics</b><span>4801 E 8th St, Tacoma &middot; 160,000 SF &middot; $0.76/mo</span></div></button>
      <button class="mcard" onclick="selectComp('C4')"><div class="pin a">C4</div><div><b>Cencora &mdash; SeaTac Market</b><span>2141 S 211th St, Des Moines &middot; $1.39/mo</span></div></button>
      <button class="mcard" onclick="selectComp('C5')"><div class="pin c">C5</div><div><b>Yusen Logistics</b><span>13501 38th St E &middot; 114,000 SF &middot; $0.90/mo</span></div></button>
      <button class="mcard" onclick="selectComp('C6')"><div class="pin c">C6</div><div><b>McLane Company</b><span>4301 W Valley Hwy E &middot; 145,812 SF &middot; $0.92/mo</span></div></button>
    </div>
  </div>
</aside>
<main class="main">

<section class="section active" id="summary">
<div class="shead"><div class="eye">Executive Summary</div><h2>NFI Sumner Lease Strategy</h2><p>4301 West Valley Highway East &middot; Sumner, WA 98390 &middot; Landlord: CenterPoint Properties</p></div>
<div class="grid g5" style="margin-bottom:20px">
  <div class="met"><div class="num">257,775</div><div class="lbl">SF Occupied</div><div class="sub">Within 506,925 SF building</div></div>
  <div class="met rd"><div class="num">Feb 2028</div><div class="lbl">Lease Expiration</div><div class="sub">~20 months remaining</div></div>
  <div class="met tl"><div class="num">$0.87</div><div class="lbl">Est. In-Place Rate</div><div class="sub">PSF / month (NNN)</div></div>
  <div class="met bl"><div class="num">16.1%</div><div class="lbl">Submarket Vacancy</div><div class="sub">Puyallup/Sumner Q1 2026</div></div>
  <div class="met gr"><div class="num">$0.90</div><div class="lbl">Submarket Ask</div><div class="sub">PSF/mo &middot; Savills Research Q1 2026</div></div>
</div>
<div class="grid g2" style="margin-bottom:20px">
  <div class="card">
    <div class="card-hd"><div class="eye">Market Context</div><h2>Tenant-Favorable Conditions in the Sumner/Fife Corridor</h2></div>
    <div class="co co-y" style="margin-bottom:14px"><b>Pricing Convention:</b> All rates are quoted <b>monthly PSF (NNN)</b>. Office space is an add-on to the shell rate. NNNs typically range $0.22&ndash;$0.30/mo on modern buildings in this submarket.</div>
    <h3>Puget Sound Big-Box Industrial &mdash; June 2026</h3>
    <p style="margin-bottom:12px">The Sumner/Fife/Puyallup corridor trades as a single competitive set for 200,000+ SF requirements. Supply has materially outpaced demand, with market conditions consistently favoring tenants across the 200&ndash;300K SF segment.</p>
    <div class="grid g2" style="margin-bottom:14px">
      <div>
        <h4>Rate Benchmarks (200&ndash;300K SF)</h4>
        <table>
          <tr><td style="padding:6px 4px;font-weight:600">First-generation ask</td><td style="padding:6px 4px"><span class="rm">~$1.00/mo</span></td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">First-generation deals</td><td style="padding:6px 4px"><span class="rm">$0.88&ndash;$0.92/mo</span></td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">Second-generation ask</td><td style="padding:6px 4px"><span class="rl">~$0.85/mo</span></td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">Second-generation deals</td><td style="padding:6px 4px"><span class="rl">~$0.77/mo</span></td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">Sublease</td><td style="padding:6px 4px"><span class="rl">$0.60&ndash;$0.72/mo</span></td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">NNNs</td><td style="padding:6px 4px">$0.22&ndash;$0.30/mo</td></tr>
        </table>
      </div>
      <div>
        <h4>Submarket Snapshot (Q1 2026 &mdash; Savills Research)</h4>
        <table>
          <tr><td style="padding:6px 4px;font-weight:600">Puyallup/Sumner</td><td style="padding:6px 4px">30.3 MSF &middot; 16.1% vac. &middot; $0.90/mo ask</td></tr>
          <tr><td style="padding:6px 4px;font-weight:600">Port of Tacoma/Fife</td><td style="padding:6px 4px">17.3 MSF &middot; 11.4% vac. &middot; $0.75/mo ask</td></tr>
        </table>
        <div class="co co-t" style="margin-top:10px;font-size:12px">Savills Research tracks sub-200K SF buildings; reported asking rates skew modestly above deal rates for the 200&ndash;300K SF segment NFI occupies.</div>
      </div>
    </div>
    <div class="co co-b">Supply is projected to meet demand for 5+ years. Rates are expected to stabilize within approximately 18 months, with market conditions shifting by mid-2028. <b>NFI's February 2028 expiration aligns with a period of sustained favorable conditions for tenants.</b></div>
  </div>
  <div style="display:flex;flex-direction:column;gap:16px">
    <div class="card">
      <h3>NFI Sumner &mdash; Property Profile</h3>
      <table>
        <tr><td class="tm">Address</td><td class="ta">4301 W Valley Hwy E, Sumner WA 98390</td></tr>
        <tr><td class="tm">Landlord</td><td><b>CenterPoint Properties</b></td></tr>
        <tr><td class="tm">Building SF</td><td>506,925 SF total</td></tr>
        <tr><td class="tm">NFI Premises</td><td><b>257,775 SF</b></td></tr>
        <tr><td class="tm">Clear Height</td><td>30'</td></tr>
        <tr><td class="tm">Dock Doors</td><td>116 dock-high + 103 trailer stalls</td></tr>
        <tr><td class="tm">Access</td><td>SR-167 frontage &middot; fenced/secured</td></tr>
        <tr><td class="tm">Expiration</td><td><b style="color:var(--R)">February 2028</b></td></tr>
      </table>
    </div>
    <div class="card">
      <h3>NFI Fife &mdash; Second Location</h3>
      <table>
        <tr><td class="tm">Address</td><td class="ta">4179 70th Ave E, Fife WA 98424</td></tr>
        <tr><td class="tm">Operates as</td><td>NFI Cal Cartage (drayage / intermodal)</td></tr>
        <tr><td class="tm">Building</td><td>Pacific Coast Corporate Park &mdash; Phase II &middot; Class B &middot; 501,250 SF total</td></tr>
        <tr><td class="tm">NFI Footprint</td><td>~402,342 SF</td></tr>
        <tr><td class="tm">Specs</td><td>30' clear &middot; 78 dock-high &middot; 8 drive-ins &middot; 363 parking</td></tr>
        <tr><td class="tm">Landlord</td><td>Clarion Partners</td></tr>
        <tr><td class="tm">Expiration</td><td style="color:var(--A)"><b>Q1 2029 (est.) &mdash; pending confirmation</b></td></tr>
      </table>
      <div class="co co-y" style="margin-top:10px;font-size:12px">Savills is confirming the lease expiration timeline for this location. A coordinated strategy across both Sumner and Fife assets is available upon confirmation.</div>
    </div>
    <div class="card">
      <h3>Portfolio Strategy</h3>
      <p style="font-size:13px">With two major Puget Sound assets approaching renewal in a coordinated timeframe, Savills has developed a <b>portfolio-level strategy</b> for NFI's Pacific Northwest industrial footprint &mdash; addressing both the Sumner and Fife locations within a single, submarket-informed framework.</p>
    </div>
  </div>
</div>
</section>

<section class="section" id="market">
<div class="shead"><div class="eye">Market Overview</div><h2>Puget Sound South End Industrial &mdash; Q1 2026</h2><p>Source: Savills Research &middot; Sumner &middot; Fife &middot; Puyallup &middot; Tacoma &middot; Kent submarkets</p></div>

<div class="card" style="margin-bottom:20px">
  <div style="display:flex;align-items:center;gap:12px;margin-bottom:16px">
    <span class="sav-bdg">Savills Research</span>
    <h3 style="margin:0">Seattle/Puget Sound Industrial &mdash; Market in Minutes Q1 2026</h3>
    <span style="font-size:11px;color:var(--M);margin-left:auto">Click to open full size</span>
  </div>
  <div class="grid g2">
    <div>
      <p style="font-size:11px;font-weight:700;color:var(--M);margin-bottom:8px;text-transform:uppercase;letter-spacing:.08em">Page 1 &mdash; Market Overview &amp; Key Statistics</p>
      <a href="assets/savills_research_p1.jpg" target="_blank"><img src="assets/savills_research_p1.jpg" class="rpg" alt="Savills Research Q1 2026 Page 1"/></a>
    </div>
    <div>
      <p style="font-size:11px;font-weight:700;color:var(--M);margin-bottom:8px;text-transform:uppercase;letter-spacing:.08em">Page 2 &mdash; Submarket Vacancy &amp; Rental Rates</p>
      <a href="assets/savills_research_p2.jpg" target="_blank"><img src="assets/savills_research_p2.jpg" class="rpg" alt="Savills Research Q1 2026 Page 2"/></a>
    </div>
  </div>
</div>

<div class="grid g5" style="margin-bottom:20px">
  <div class="met rd"><div class="num">11.5%</div><div class="lbl">Regional Vacancy</div><div class="sub">+230 bps YoY &middot; New high</div></div>
  <div class="met"><div class="num">$1.05</div><div class="lbl">Regional Ask ($/mo)</div><div class="sub">Flat YoY &middot; All submarkets</div></div>
  <div class="met rd"><div class="num">-0.8 MSF</div><div class="lbl">Net Absorption YTD</div><div class="sub">Reversal from +0.2 MSF in Q1 2025</div></div>
  <div class="met"><div class="num">2.5 MSF</div><div class="lbl">Deliveries YTD</div><div class="sub">Highest since late 2023</div></div>
  <div class="met gr"><div class="num">3.1 MSF</div><div class="lbl">Under Construction</div><div class="sub">Down 3.0 MSF YoY &middot; Record low pipeline</div></div>
</div>

<div class="grid g2" style="margin-bottom:20px">
  <div class="card">
    <h3>Submarket Vacancy vs. Asking Rate (Q1 2026)</h3>
    <div class="cw"><canvas id="vacancyChart"></canvas></div>
  </div>
  <div class="card">
    <h3>Rate Tier Breakdown &mdash; 200&ndash;300K SF</h3>
    <div class="cw"><canvas id="tierChart"></canvas></div>
  </div>
</div>

<div class="grid g3">
  <div class="card">
    <h3>Supply Pipeline</h3>
    <div class="met rd" style="margin-bottom:10px"><div class="num">~5 MSF</div><div class="lbl">Delivered Q4 2025</div></div>
    <div class="met" style="margin-bottom:10px"><div class="num">~3 MSF</div><div class="lbl">Still Under Construction</div></div>
    <div class="co co-y" style="font-size:12px">Supply projected to exceed demand for <b>5+ years</b>. Limited new spec construction started in the past two quarters.</div>
  </div>
  <div class="card">
    <h3>Typical Lease Package &mdash; New 200K+ SF</h3>
    <table>
      <tr><td class="tm">Free Rent</td><td style="font-weight:700">4&ndash;6 months</td></tr>
      <tr><td class="tm">TI (new lease)</td><td style="font-weight:700">$1.75&ndash;$8.00 PSF</td></tr>
      <tr><td class="tm">Escalations</td><td style="font-weight:700">3.00&ndash;4.00% annually</td></tr>
      <tr><td class="tm">Lease Term</td><td style="font-weight:700">5&ndash;7 years typical</td></tr>
    </table>
  </div>
  <div class="card">
    <h3>Notable Q1 2026 Transactions</h3>
    <table>
      <thead><tr><th>Tenant</th><th>SF</th><th>Type</th></tr></thead>
      <tbody>
        <tr><td class="ta">HD Supply</td><td>434,000</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Mobis Parts America</td><td>181,000</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Pacific Pride</td><td>122,993</td><td><span class="bdg b-new">New Lease</span></td></tr>
        <tr><td class="ta">Goodwill</td><td>107,999</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Stryder Distribution</td><td>102,368</td><td><span class="bdg b-new">New Lease</span></td></tr>
      </tbody>
    </table>
    <div class="co co-y" style="margin-top:10px;font-size:12px">Q1 2026 activity is concentrated in renewals and sub-200K SF transactions. No large new-lease deals in the 200K+ SF segment this quarter.</div>
  </div>
</div>
</section>

<section class="section" id="options">
<div class="shead"><div class="eye">Relocation / Leverage Options</div><h2>Competitive Option Set</h2><p>Eight direct relocation alternatives and two market rate reference properties &middot; Updated June 2026</p></div>
<div class="co co-b" style="margin-bottom:16px">Two sublease properties below (A1, A2) are presented as <b>market rate references</b> reflecting current submarket pricing. Their lease terms preclude them as relocation candidates but they provide important context for evaluating market conditions.</div>
<div class="map-layout" style="margin-bottom:20px">
  <div id="mapDetailPanel">
    <div style="text-align:center;padding:40px 20px;color:var(--M)"><div style="font-size:32px;margin-bottom:10px">&#128506;</div><p style="font-size:13px">Select an option from the sidebar or click a map pin to view details.</p></div>
  </div>
  <div id="optionsMap"></div>
</div>
<div class="card">
  <h3>Relocation Alternatives &mdash; Full Comparison</h3>
  <table>
    <thead><tr><th>#</th><th>Property</th><th>SF</th><th>Clear</th><th>Shell Rate</th><th>Type</th><th>Landlord</th><th>Notes</th></tr></thead>
    <tbody>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O1</div></td><td><div class="ta">Fife Commerce Center</div><div class="tm">2511 70th Ave E (Bldg B), Fife</div></td><td>255,070</td><td>30'</td><td class="rl"><b>$0.78/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>Neil Walter Co.</td><td style="font-size:11px">255,070 SF direct. 72 dock-high doors. Negotiable term.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O2</div></td><td><div class="ta">Pioneer Logistics Center</div><div class="tm">14021 E Pioneer Way, Puyallup</div></td><td>211,153</td><td>36'</td><td class="rl"><b>$0.86/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>&mdash;</td><td style="font-size:11px">36' clear height. Excellent SR-167 access.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O3</div></td><td><div class="ta">Bridge Point i5 &mdash; Building 2</div><div class="tm">80 5th Ave, Milton</div></td><td>239,271&ndash;478,542</td><td>40'</td><td class="rh"><b>$1.25/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>Bridge Dev.</td><td style="font-size:11px">Premium Class A specs. 40' clear. Flexible concession structure available.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O4</div></td><td><div class="ta">LogistiCenter at 167</div><div class="tm">7402&ndash;7490 26th St E, Fife</div></td><td>225,972</td><td>32'</td><td class="rl"><b>$0.86/mo</b></td><td><span class="bdg b-dir">Direct / Sublease</span></td><td>&mdash;</td><td style="font-size:11px">Direct lease structure available. Strong SR-167 location.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O5</div></td><td><div class="ta">Sumner Central</div><div class="tm">2701 142nd Ave E, Sumner</div></td><td>249,209 now / 427,253 by Aug 2026</td><td>30'</td><td class="rl"><b>~$0.85/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>&mdash;</td><td style="font-size:11px">249,209 SF available immediately; full 427,253 SF by August 2026.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O6</div></td><td><div class="ta">Norwesco Building</div><div class="tm">3011 70th Ave E, Fife</div></td><td>323,967</td><td>&mdash;</td><td class="rl"><b>$0.80s/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>&mdash;</td><td style="font-size:11px">Full 323,967 SF building. Strong size match for NFI's operational footprint.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O7</div></td><td><div class="ta">Portside Distribution Center</div><div class="tm">1401 St. Paul Ave, Tacoma</div></td><td>325,000</td><td>&mdash;</td><td class="rl"><b>$0.79/mo</b></td><td><span class="bdg b-dir">Direct</span></td><td>&mdash;</td><td style="font-size:11px">$0.79 shell / $2.14 office add-on. Port of Tacoma adjacent.</td></tr>
      <tr><td><div class="pin" style="width:26px;height:26px;font-size:10px">O8</div></td><td><div class="ta">Prologis Blair Distribution Center</div><div class="tm">2340 Taylor Way (Bldg A), Tacoma</div></td><td>205,121</td><td>&mdash;</td><td class="rl"><b>$0.85/mo</b></td><td><span class="bdg b-dir">Direct / Sublease</span></td><td>Prologis</td><td style="font-size:11px">Prologis-owned. Dominant institutional landlord across Sumner/Fife/Tacoma.</td></tr>
    </tbody>
  </table>
  <div style="margin-top:20px">
    <h4>Market Rate Reference Properties</h4>
    <table>
      <thead><tr><th>#</th><th>Property</th><th>SF</th><th>Rate</th><th>Type</th><th>Term Expiration</th><th>Market context</th></tr></thead>
      <tbody>
        <tr><td><div class="pin a" style="width:26px;height:26px;font-size:10px">A1</div></td><td><div class="ta">Fife Commerce Center &mdash; Sublease</div><div class="tm">2511 70th Ave E, Fife</div></td><td>125,000&ndash;255,000</td><td class="rl"><b>$0.72/mo</b></td><td><span class="bdg b-ref">Market Reference</span></td><td style="color:var(--R)">August 2027</td><td style="font-size:11px">Current sublease rate in the Fife submarket. Reflects lower end of 2027 pricing.</td></tr>
        <tr><td><div class="pin a" style="width:26px;height:26px;font-size:10px">A2</div></td><td><div class="ta">Fife I-5 Commerce Center</div><div class="tm">2801 78th Ave E, Fife</div></td><td>250,490</td><td class="rl"><b>$0.65/mo blended</b></td><td><span class="bdg b-ref">Market Reference</span></td><td style="color:var(--A)">December 2029</td><td style="font-size:11px">Current sublease rate. Represents current lower-end pricing in the 250K+ SF segment.</td></tr>
      </tbody>
    </table>
  </div>
</div>
</section>

<section class="section" id="comps">
<div class="shead"><div class="eye">Lease Comps</div><h2>Recent Market Transactions</h2><p>Comparable transactions &middot; 2024&ndash;2026 &middot; 200K+ SF Class A industrial, Sumner / Fife / Tacoma</p></div>
<div class="co co-t" style="margin-bottom:16px">Class A industrial new leases in the Sumner/Fife submarket are executing at <b>$0.92&ndash;$0.97/mo</b> with 4&ndash;6 months free rent and tenant improvement packages. Renewals in the submarket are clearing in the $0.76&ndash;$0.90/mo range. The Cencora transaction reflects SeaTac submarket pricing &mdash; a distinct market from Puyallup/Sumner and Fife.</div>
<div class="card" style="margin-bottom:16px">
  <h3>Comparable Transaction Locations &mdash; Aerial Map</h3>
  <p style="font-size:12px;color:var(--M);margin-bottom:12px">All comps plotted against NFI subject property (red). Click any pin for full detail.</p>
  <div id="compsMap"></div>
</div>
<div class="card" style="margin-bottom:16px">
  <h3>Comparable Transaction Summary</h3>
  <table>
    <thead><tr><th>#</th><th>Tenant</th><th>Property</th><th>Qtr</th><th>SF</th><th>Type</th><th>Starting Rent</th><th>Escalation</th><th>Free Rent</th><th>TI</th><th>Term</th><th>Landlord</th></tr></thead>
    <tbody>
      <tr><td><div class="pin c" style="width:26px;height:26px;font-size:10px">C1</div></td><td><b>Crane Worldwide Logistics</b></td><td><div class="ta">3101 W Valley Hwy E</div><div class="tm">Sumner, WA &mdash; Toysmith Bldg</div></td><td>Q4 2024</td><td>264,344</td><td><span class="bdg b-new">New Lease</span></td><td class="rm"><b>$0.92/mo</b><div class="tm">$11.04/yr NNN</div></td><td>3.75%/yr</td><td>6 months</td><td>$1.75/SF</td><td>5y 6m</td><td>BentallGreenOak</td></tr>
      <tr><td><div class="pin c" style="width:26px;height:26px;font-size:10px">C2</div></td><td><b>Western Container</b><div class="tm">Coca-Cola subsidiary</div></td><td><div class="ta">2205 70th Ave E</div><div class="tm">Fife, WA</div></td><td>Q1 2025</td><td>202,250</td><td><span class="bdg b-new">New Lease</span></td><td class="rm"><b>$0.97/mo</b><div class="tm">$11.64/yr Net</div></td><td>4.00%/yr</td><td>4 months</td><td>&mdash;</td><td>7y 4m</td><td>Principal RE Investors</td></tr>
      <tr><td><div class="pin c" style="width:26px;height:26px;font-size:10px">C3</div></td><td><b>Tigers (USA) Global Logistics</b></td><td><div class="ta">4801 E 8th St</div><div class="tm">Tacoma, WA &mdash; Prologis Park D</div></td><td>Q2 2025</td><td>160,000</td><td><span class="bdg b-ren">Renewal</span></td><td class="rl"><b>$0.76/mo</b><div class="tm">$9.12/yr NNN</div></td><td>3.00%/yr</td><td>6 months</td><td>$1.87/SF</td><td>5y 6m</td><td><b>Prologis</b></td></tr>
      <tr><td><div class="pin a" style="width:26px;height:26px;font-size:10px">C4</div></td><td><b>Cencora (AmeriSource Bergen)</b></td><td><div class="ta">2141 S 211th St</div><div class="tm">Des Moines, WA &mdash; SeaTac market</div></td><td>Q3 2025</td><td>230,913</td><td><span class="bdg b-amd">Amendment</span></td><td class="rh"><b>$1.39/mo</b><div class="tm">SeaTac premium</div></td><td>4.00%/yr</td><td>2 months</td><td>$0</td><td>7y 2m</td><td>Port of Seattle</td></tr>
      <tr><td><div class="pin c" style="width:26px;height:26px;font-size:10px">C5</div></td><td><b>Yusen Logistics</b></td><td><div class="ta">13501 38th St E</div><div class="tm">Sumner, WA</div></td><td>2023</td><td>114,000</td><td><span class="bdg b-new">New Lease</span></td><td class="rl"><b>$0.90/mo</b></td><td>&mdash;</td><td>&mdash;</td><td>&mdash;</td><td>&mdash;</td><td>&mdash;</td></tr>
      <tr><td><div class="pin c" style="width:26px;height:26px;font-size:10px">C6</div></td><td><b>McLane Company</b><div class="tm">NFI's current building</div></td><td><div class="ta">4301 W Valley Hwy E</div><div class="tm">Sumner, WA &mdash; CenterPoint</div></td><td>2022</td><td>145,812</td><td><span class="bdg b-new">New Lease</span></td><td class="rm"><b>$0.92/mo</b></td><td>&mdash;</td><td>&mdash;</td><td>&mdash;</td><td>&mdash;</td><td>CenterPoint</td></tr>
    </tbody>
  </table>
</div>
<div class="grid g2">
  <div class="card"><div class="dh tl"><h4>C1 &mdash; Crane Worldwide Logistics</h4><p>3101 W Valley Hwy E, Sumner WA &middot; Q4 2024 &middot; 264,344 SF &middot; New Lease</p></div><div class="dgrid"><div><div class="dlbl">Starting Rent</div><div class="dval">$0.92/mo NNN</div><div class="dsub">$11.04/yr annual</div></div><div><div class="dlbl">Net Effective</div><div class="dval">$10.70/yr</div></div><div><div class="dlbl">Escalation</div><div class="dval">3.75%/year</div></div><div><div class="dlbl">Free Rent</div><div class="dval">6 months</div></div><div><div class="dlbl">TI</div><div class="dval">$1.75 PSF</div></div><div><div class="dlbl">Term</div><div class="dval">5 years 6 months</div></div><div><div class="dlbl">Clear Height</div><div class="dval">30'</div></div><div><div class="dlbl">Docks</div><div class="dval">45</div></div></div><div class="co co-b" style="font-size:12px">Same West Valley Highway East corridor as NFI's current location. Class A new lease, Q4 2024. Estimated starting rent today: $12.31/yr (+7.48% rent spread from execution date).</div></div>
  <div class="card"><div class="dh tl"><h4>C2 &mdash; Western Container (Coca-Cola)</h4><p>2205 70th Ave E, Fife WA &middot; Q1 2025 &middot; 202,250 SF &middot; New Lease</p></div><div class="dgrid"><div><div class="dlbl">Starting Rent</div><div class="dval">$0.97/mo Net</div><div class="dsub">$11.64/yr annual</div></div><div><div class="dlbl">Net Effective</div><div class="dval">$12.70/yr</div></div><div><div class="dlbl">Escalation</div><div class="dval">4.00%/year</div></div><div><div class="dlbl">Free Rent</div><div class="dval">4 months</div></div><div><div class="dlbl">TI</div><div class="dval">&mdash;</div></div><div><div class="dlbl">Term</div><div class="dval">7 years 4 months</div></div><div><div class="dlbl">Clear Height</div><div class="dval">30'</div></div><div><div class="dlbl">Docks</div><div class="dval">40</div></div></div><div class="co co-b" style="font-size:12px">Fife submarket Class A new deal. Commencement April 2026, expiration July 2033. Landlord: Principal Real Estate Investors.</div></div>
  <div class="card"><div class="dh tl"><h4>C3 &mdash; Tigers (USA) Global Logistics &mdash; Prologis</h4><p>4801 E 8th St, Tacoma WA &middot; Q2 2025 &middot; 160,000 SF &middot; Renewal</p></div><div class="dgrid"><div><div class="dlbl">Starting Rent</div><div class="dval">$0.76/mo NNN</div><div class="dsub">$9.12/yr annual</div></div><div><div class="dlbl">Net Effective</div><div class="dval">$8.60/yr</div></div><div><div class="dlbl">Escalation</div><div class="dval">3.00%/year</div></div><div><div class="dlbl">Free Rent</div><div class="dval">6 months</div></div><div><div class="dlbl">TI</div><div class="dval">$1.87 PSF</div></div><div><div class="dlbl">Term</div><div class="dval">5 years 6 months</div></div><div><div class="dlbl">Clear Height</div><div class="dval">36'</div></div><div><div class="dlbl">Docks</div><div class="dval">31</div></div></div><div class="co co-t" style="font-size:12px"><b>Prologis transaction.</b> Renewal executed Q2 2025 at a +45.91% rent spread vs. prior rent, reflecting market-rate adjustment. Expires April 30, 2031. Prologis renewals in the Tacoma/Fife submarket are executing in the $0.76&ndash;$0.85/mo range with meaningful concessions.</div></div>
  <div class="card"><div class="dh am"><h4>C4 &mdash; Cencora (AmeriSource Bergen) &mdash; SeaTac Market</h4><p>2141 S 211th St, Des Moines WA &middot; Q3 2025 &middot; 230,913 SF &middot; Amendment</p></div><div class="dgrid"><div><div class="dlbl">Starting Rent</div><div class="dval" style="color:var(--R)">$1.39/mo NNN</div><div class="dsub">$16.63/yr annual</div></div><div><div class="dlbl">Est. Today</div><div class="dval">$13.45/yr</div></div><div><div class="dlbl">Escalation</div><div class="dval">4.00%/year</div></div><div><div class="dlbl">Free Rent</div><div class="dval">2 months</div></div><div><div class="dlbl">TI</div><div class="dval">$0</div></div><div><div class="dlbl">Term</div><div class="dval">7 years 2 months</div></div><div><div class="dlbl">Clear Height</div><div class="dval">36'</div></div><div><div class="dlbl">Docks</div><div class="dval">24</div></div></div><div class="co co-b" style="font-size:12px"><b>SeaTac submarket premium.</b> The Des Moines/SeaTac corridor commands a significant premium over Puyallup/Sumner and Fife due to proximity to SEA-TAC Airport and the Port of Seattle. Illustrates the rate differential between submarkets.</div></div>
</div>
</section>

<section class="section" id="pricing">
<div class="shead"><div class="eye">Pricing Landscape</div><h2>Puget Sound South End &mdash; Asking Rates</h2><p>150,000&ndash;350,000 SF availabilities &middot; Current landlord asking rates (monthly PSF NNN shell rate) &middot; June 2026</p></div>
<div class="co co-y" style="margin-bottom:16px"><b>Source:</b> Savills broker network survey, June 2026. Rates represent current landlord asking rates. Actual transaction rates will vary based on term, concession structure, and credit. Sorted lowest to highest.</div>
<div class="card" style="margin-bottom:16px">
  <h3>Asking Rate Spectrum &mdash; All Availabilities</h3>
  <div style="display:flex;gap:16px;flex-wrap:wrap;margin-bottom:12px;font-size:11px">
    <span style="display:flex;align-items:center;gap:5px;font-weight:600;color:var(--M)"><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#e57373"></span>Above market ($1.00+/mo)</span>
    <span style="display:flex;align-items:center;gap:5px;font-weight:600;color:var(--M)"><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:var(--A)"></span>Market ($0.85&ndash;$0.99/mo)</span>
    <span style="display:flex;align-items:center;gap:5px;font-weight:600;color:var(--M)"><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:#81c784"></span>Below market (&lt;$0.85/mo)</span>
    <span style="display:flex;align-items:center;gap:5px;font-weight:600;color:var(--B)"><span style="display:inline-block;width:10px;height:10px;border-radius:50%;background:var(--B)"></span>NFI target range</span>
  </div>
  <div class="cwt" style="height:460px"><canvas id="pricingChart"></canvas></div>
</div>
</section>

<section class="section" id="research">
<div class="shead"><div class="eye">Savills Research</div><h2>Seattle/Puget Sound Industrial &mdash; Market in Minutes</h2><p>Q1 2026 &middot; Savills Research &middot; Rachael Deckys, Senior Research Analyst</p></div>
<div class="grid g5" style="margin-bottom:20px">
  <div class="met rd"><div class="num">11.5%</div><div class="lbl">Regional Vacancy</div><div class="sub">+230 bps YoY &middot; New high</div></div>
  <div class="met"><div class="num">$1.05</div><div class="lbl">Overall Ask ($/mo)</div><div class="sub">Flat YoY &middot; All submarkets</div></div>
  <div class="met rd"><div class="num">-0.8 MSF</div><div class="lbl">Net Absorption YTD</div><div class="sub">Reversal from +0.2 MSF Q1 2025</div></div>
  <div class="met"><div class="num">2.5 MSF</div><div class="lbl">Deliveries YTD</div><div class="sub">Highest since late 2023</div></div>
  <div class="met gr"><div class="num">3.1 MSF</div><div class="lbl">Under Construction</div><div class="sub">Down 3.0 MSF YoY &middot; Record low</div></div>
</div>
<div class="card" style="margin-bottom:20px">
  <div style="display:flex;align-items:center;gap:12px;margin-bottom:16px">
    <span class="sav-bdg">Savills Research</span>
    <h3 style="margin:0">Full Report &mdash; Q1 2026 &middot; Click any page to open full size</h3>
  </div>
  <div class="grid g2">
    <div>
      <p style="font-size:11px;font-weight:700;color:var(--M);margin-bottom:8px;text-transform:uppercase;letter-spacing:.08em">Page 1 &mdash; Market Overview &amp; Key Statistics</p>
      <a href="assets/savills_research_p1.jpg" target="_blank"><img src="assets/savills_research_p1.jpg" class="rpg" alt="Savills Research Q1 2026 Page 1"/></a>
    </div>
    <div>
      <p style="font-size:11px;font-weight:700;color:var(--M);margin-bottom:8px;text-transform:uppercase;letter-spacing:.08em">Page 2 &mdash; Submarket Vacancy &amp; Rental Rates</p>
      <a href="assets/savills_research_p2.jpg" target="_blank"><img src="assets/savills_research_p2.jpg" class="rpg" alt="Savills Research Q1 2026 Page 2"/></a>
    </div>
  </div>
</div>
<div class="grid g2" style="margin-bottom:20px">
  <div class="card"><h3>Vacancy Rate by Submarket (Q1 2026)</h3><div class="cwt"><canvas id="savVacChart"></canvas></div></div>
  <div class="card"><h3>Asking Rent by Submarket ($/SF Monthly)</h3><div class="cwt"><canvas id="savRentChart"></canvas></div></div>
</div>
<div class="co co-t" style="margin-bottom:20px">Puyallup/Sumner carries the <b>highest vacancy (16.1%) and lowest asking rent ($0.90/mo)</b> among major Puget Sound submarkets. With negative net absorption and a contracting development pipeline, market conditions strongly favor tenants in this submarket. NFI's February 2028 expiration aligns with a period of sustained market softness.</div>
<div class="grid g2">
  <div class="card">
    <h3>Notable Q1 2026 Leases</h3>
    <table>
      <thead><tr><th>Tenant</th><th>SF</th><th>Submarket</th><th>Type</th></tr></thead>
      <tbody>
        <tr><td class="ta">HD Supply Facilities Maintenance</td><td>434,000</td><td>Kent</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Mobis Parts America</td><td>181,000</td><td>Puyallup/Sumner</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Pacific Pride</td><td>122,993</td><td>Lakewood/Dupont</td><td><span class="bdg b-new">New Lease</span></td></tr>
        <tr><td class="ta">Goodwill</td><td>107,999</td><td>Everett</td><td><span class="bdg b-ren">Renewal</span></td></tr>
        <tr><td class="ta">Stryder Distribution</td><td>102,368</td><td>Port of Tacoma/Fife</td><td><span class="bdg b-new">New Lease</span></td></tr>
      </tbody>
    </table>
  </div>
  <div class="card">
    <h3>New Deliveries Q1 2026</h3>
    <table>
      <thead><tr><th>Developer</th><th>SF</th><th>Submarket</th><th>Status</th></tr></thead>
      <tbody>
        <tr><td class="ta">Bridge Industrial</td><td>957,726</td><td>University Place</td><td>Delivered &mdash; available</td></tr>
        <tr><td class="ta">KG Investments</td><td>705,680</td><td>Puyallup/Sumner</td><td>Delivered &mdash; available</td></tr>
        <tr><td class="ta">Bridge Industrial</td><td>517,042</td><td>University Place</td><td>Delivered &mdash; available</td></tr>
        <tr><td class="ta">Bridge Industrial</td><td>662,044</td><td>University Place</td><td>Under Construction</td></tr>
        <tr><td class="ta">Dermody</td><td>443,420</td><td>E Pierce County</td><td>Under Construction</td></tr>
      </tbody>
    </table>
    <div class="co co-r" style="margin-top:12px;font-size:12px">3.1+ MSF delivered with limited preleasing, adding directly to available supply in NFI's submarket. The KG Investments delivery (705,680 SF, Puyallup/Sumner) is direct competition in the same submarket.</div>
  </div>
</div>
</section>

<section class="section" id="approach">
<div class="shead"><div class="eye">Recommended Approach</div><h2>NFI Sumner &mdash; Renewal Strategy</h2><p>Savills recommendation &middot; June 2026</p></div>
<div class="grid g2" style="margin-bottom:20px">
  <div class="card">
    <div class="card-hd"><div class="eye">Strategy</div><h2>Optimizing Renewal Terms in a Tenant-Favorable Market</h2><p>NFI's February 2028 expiration aligns with a period of sustained favorable conditions for tenants in the Sumner/Fife corridor.</p></div>
    <div>
      <h3 style="margin-bottom:16px">Recommended Steps</h3>
      <div class="step"><div class="snum">1</div><div><h4>Confirm Competitive Set (Now &mdash; Q3 2026)</h4><p>Present the revised option set to NFI and confirm operational suitability of each property. Narrow to the strongest candidates before engaging landlords.</p></div></div>
      <div class="step"><div class="snum">2</div><div><h4>Request Proposals from All Relocation Options (Q3 2026)</h4><p>Simultaneously request proposals from O1&ndash;O8. This establishes a current, documented market record and provides a factual basis for renewal negotiations. The Prologis option (O8) is particularly important given Prologis's dominant position across the Sumner/Fife/Tacoma corridor.</p></div></div>
      <div class="step"><div class="snum">3</div><div><h4>Begin Renewal Dialogue with CenterPoint (Q4 2026)</h4><p>Open with a data-backed renewal proposal informed by the comparable set. Target: $0.78&ndash;$0.85/mo shell, 4&ndash;6 months free rent, $5&ndash;8 PSF TI for improvements, 3.5% escalations, 5&ndash;7 year term. Current market data supports this range across all transaction types in the 200,000+ SF segment.</p></div></div>
      <div class="step"><div class="snum">4</div><div><h4>Confirm NFI Fife Expiration + Portfolio Strategy (Parallel)</h4><p>Confirm the lease expiration timeline for NFI's Fife location. If both assets are tracking toward a coordinated expiration window, Savills will present a unified portfolio renewal strategy addressing both CenterPoint (Sumner) and Clarion Partners (Fife) in parallel.</p></div></div>
      <div class="step"><div class="snum">5</div><div><h4>Finalize by Q2 2027</h4><p>Market conditions are expected to stabilize within approximately 18 months. Initiating discussions well in advance of expiration ensures NFI has the time to evaluate all options and secure the best available terms before the market shifts.</p></div></div>
    </div>
  </div>
  <div style="display:flex;flex-direction:column;gap:16px">
    <div class="card">
      <h3>Target Renewal Terms</h3>
      <table>
        <tr><td class="tm">Shell Rate</td><td><b>$0.78&ndash;$0.85/mo NNN</b></td></tr>
        <tr><td class="tm">NNNs</td><td>~$0.24&ndash;$0.26/mo</td></tr>
        <tr><td class="tm">Free Rent</td><td><b>4&ndash;6 months</b></td></tr>
        <tr><td class="tm">TI Allowance</td><td><b>$5&ndash;8 PSF</b></td></tr>
        <tr><td class="tm">Escalations</td><td>3.00&ndash;3.50%/year</td></tr>
        <tr><td class="tm">Term</td><td>5&ndash;7 years</td></tr>
        <tr><td class="tm">Renewal Option</td><td>1 &times; 5-year renewal option</td></tr>
      </table>
      <div class="co co-t" style="margin-top:12px;font-size:12px">Comparable support: Crane Worldwide (same corridor, Q4 2024) at $0.92/mo; Tigers renewal (Prologis, Q2 2025) at $0.76/mo. Target renewal range well-supported by recent transaction evidence.</div>
    </div>
    <div class="card">
      <h3>Portfolio Strategy</h3>
      <div class="co co-b" style="margin-bottom:10px">Savills has mapped both of NFI's major Puget Sound assets, assessed the submarket dynamics around each, and developed a coordinated renewal strategy across the portfolio.</div>
      <p style="font-size:13px">Addressing Sumner (CenterPoint) and Fife (Clarion Partners) as a coordinated two-asset strategy ensures consistent terms across NFI's Pacific Northwest footprint and creates meaningful advantages in parallel negotiations.</p>
    </div>
    <div class="card">
      <h3>Action Timeline</h3>
      <table>
        <tr><td style="padding:8px 6px"><span class="bdg b-sub">Immediate</span></td><td style="font-size:12px;padding:8px 6px">Confirm lease expiration timeline for NFI Fife (4179 70th Ave E)</td></tr>
        <tr><td style="padding:8px 6px"><span class="bdg b-sub">Immediate</span></td><td style="font-size:12px;padding:8px 6px">Confirm current availability for Norwesco Building, 3011 70th Ave E, Fife</td></tr>
        <tr><td style="padding:8px 6px"><span class="bdg b-amd">Q3 2026</span></td><td style="font-size:12px;padding:8px 6px">Present option set to NFI; confirm operational suitability of each property</td></tr>
        <tr><td style="padding:8px 6px"><span class="bdg b-amd">Q3 2026</span></td><td style="font-size:12px;padding:8px 6px">Request proposals from all 8 relocation options simultaneously</td></tr>
        <tr><td style="padding:8px 6px"><span class="bdg b-ren">Q4 2026</span></td><td style="font-size:12px;padding:8px 6px">Open renewal dialogue with CenterPoint (and Clarion if Fife confirmed)</td></tr>
        <tr><td style="padding:8px 6px"><span class="bdg b-new">By Q2 2027</span></td><td style="font-size:12px;padding:8px 6px">Signed renewal agreement at target terms</td></tr>
      </table>
    </div>
  </div>
</div>
</section>

</main>
</div>

<script>
var NFI_LAT=47.220,NFI_LNG=-122.239;
var OPTS={
  O1:{lb:'O1',nm:'Fife Commerce Center',ad:'2511 70th Ave E (Bldg B), Fife WA',la:47.2398,ln:-122.3621,sf:'255,070',cl:"30'",rt:'$0.78/mo NNN',tp:'Direct',ll:'Neil Walter Company',nt:'255,070 SF direct. 72 dock-high doors. Negotiable term.',anc:false},
  O2:{lb:'O2',nm:'Pioneer Logistics Center',ad:'14021 E Pioneer Way, Puyallup WA',la:47.182,ln:-122.248,sf:'211,153',cl:"36'",rt:'$0.86/mo NNN',tp:'Direct',ll:'&mdash;',nt:"36' clear height. Strong functional alternative.",anc:false},
  O3:{lb:'O3',nm:'Bridge Point i5 &mdash; Building 2',ad:'80 5th Ave, Milton WA',la:47.2558,ln:-122.3188,sf:'239,271&ndash;478,542',cl:"40'",rt:'$1.25/mo NNN',tp:'Direct',ll:'Bridge Development',nt:"Premium Class A facility. 40' clear height. Flexible concession structure.",anc:false},
  O4:{lb:'O4',nm:'LogistiCenter at 167',ad:'7402&ndash;7490 26th St E, Fife WA',la:47.2368,ln:-122.3615,sf:'100,000&ndash;225,972',cl:"32'",rt:'$0.86/mo',tp:'Direct / Sublease',ll:'&mdash;',nt:'225,972 SF. Excellent SR-167 access. Direct lease structure available.',anc:false},
  O5:{lb:'O5',nm:'Sumner Central',ad:'2701 142nd Ave E, Sumner WA',la:47.213,ln:-122.207,sf:'249,209 now / 427,253 by Aug 2026',cl:"30'",rt:'~$0.85/mo',tp:'Direct',ll:'&mdash;',nt:"249,209 SF available now; full 427,253 SF by August 2026.",anc:false},
  O6:{lb:'O6',nm:'Norwesco Building',ad:'3011 70th Ave E, Fife WA',la:47.2405,ln:-122.3605,sf:'323,967',cl:'&mdash;',rt:'$0.80s/mo',tp:'Direct',ll:'&mdash;',nt:'Full 323,967 SF building availability.',anc:false},
  O7:{lb:'O7',nm:'Portside Distribution Center',ad:'1401 St. Paul Ave, Tacoma WA',la:47.2502,ln:-122.4472,sf:'325,000',cl:'&mdash;',rt:'$0.79/mo',tp:'Direct',ll:'&mdash;',nt:'$0.79/mo shell / $2.14 office add-on. Port of Tacoma adjacent.',anc:false},
  O8:{lb:'O8',nm:'Prologis Blair Distribution Center',ad:'2340 Taylor Way (Bldg A), Tacoma WA',la:47.2485,ln:-122.4455,sf:'205,121',cl:'&mdash;',rt:'$0.85/mo',tp:'Direct / Sublease',ll:'Prologis',nt:'Prologis-owned. Dominant institutional landlord across Sumner/Fife/Tacoma.',anc:false},
  A1:{lb:'A1',nm:'Fife Commerce Center &mdash; Sublease',ad:'2511 70th Ave E, Fife WA',la:47.239,ln:-122.3628,sf:'125,000&ndash;255,000',cl:"30'",rt:'$0.72/mo NNN',tp:'Market Reference',ll:'Lee &amp; Assoc.',nt:'Current sublease rate in the Fife submarket. Term expires August 2027.',anc:true},
  A2:{lb:'A2',nm:'Fife I-5 Commerce Center',ad:'2801 78th Ave E, Fife WA',la:47.238,ln:-122.356,sf:'250,490',cl:"32'",rt:'$0.65/mo blended',tp:'Market Reference',ll:'&mdash;',nt:'Current lower-end sublease pricing in the 250K+ SF segment. Term expires December 2029.',anc:true}
};
var COMPS={
  C1:{lb:'C1',nm:'Crane Worldwide Logistics',ad:'3101 W Valley Hwy E, Sumner WA',la:47.2205,ln:-122.2395,qt:'Q4 2024',sf:'264,344',rt:'$0.92/mo',tp:'New Lease',es:'3.75%/yr',fr:'6 mo',ti:'$1.75/SF',tm:'5y 6m',ll:'BentallGreenOak'},
  C2:{lb:'C2',nm:'Western Container',ad:'2205 70th Ave E, Fife WA',la:47.2378,ln:-122.3595,qt:'Q1 2025',sf:'202,250',rt:'$0.97/mo',tp:'New Lease',es:'4.00%/yr',fr:'4 mo',ti:'&mdash;',tm:'7y 4m',ll:'Principal RE Investors'},
  C3:{lb:'C3',nm:'Tigers (USA) Global Logistics',ad:'4801 E 8th St, Tacoma WA',la:47.2348,ln:-122.3962,qt:'Q2 2025',sf:'160,000',rt:'$0.76/mo',tp:'Renewal',es:'3.00%/yr',fr:'6 mo',ti:'$1.87/SF',tm:'5y 6m',ll:'Prologis'},
  C4:{lb:'C4',nm:'Cencora (AmeriSource Bergen)',ad:'2141 S 211th St, Des Moines WA',la:47.4025,ln:-122.3285,qt:'Q3 2025',sf:'230,913',rt:'$1.39/mo',tp:'Amendment',es:'4.00%/yr',fr:'2 mo',ti:'$0',tm:'7y 2m',ll:'Port of Seattle'},
  C5:{lb:'C5',nm:'Yusen Logistics',ad:'13501 38th St E, Sumner WA',la:47.225,ln:-122.215,qt:'2023',sf:'114,000',rt:'$0.90/mo',tp:'New Lease',es:'&mdash;',fr:'&mdash;',ti:'&mdash;',tm:'&mdash;',ll:'&mdash;'},
  C6:{lb:'C6',nm:'McLane Company',ad:'4301 W Valley Hwy E, Sumner WA',la:47.2205,ln:-122.2388,qt:'2022',sf:'145,812',rt:'$0.92/mo',tp:'New Lease',es:'&mdash;',fr:'&mdash;',ti:'&mdash;',tm:'&mdash;',ll:'CenterPoint'}
};

function showSection(id){
  document.querySelectorAll('.section').forEach(function(s){s.classList.remove('active');});
  document.querySelectorAll('.nav-btn').forEach(function(b){b.classList.remove('active');});
  var sec=document.getElementById(id); if(sec) sec.classList.add('active');
  var btn=document.querySelector('[data-sec="'+id+'"]'); if(btn) btn.classList.add('active');
  document.getElementById('sb-opts').style.display=(id==='options')?'block':'none';
  document.getElementById('sb-comps').style.display=(id==='comps')?'block':'none';
  if(id==='options'){if(!window._oi){initOptMap();}else if(window._om){setTimeout(function(){window._om.invalidateSize();},150);}}
  if(id==='comps'){if(!window._ci){initCompMap();}else if(window._cm){setTimeout(function(){window._cm.invalidateSize();},150);}}
  if(id==='market'&&!window._mi){initMktCharts();}
  if(id==='pricing'&&!window._pi){initPrChart();}
  if(id==='research'&&!window._ri){initResCharts();}
}

function aerial(map){
  L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',{maxZoom:19,attribution:'Esri, Maxar'}).addTo(map);
  L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/Reference/World_Boundaries_and_Places/MapServer/tile/{z}/{y}/{x}',{maxZoom:19}).addTo(map);
}
function mkPin(lbl,color,sz){
  sz=sz||28;
  return L.divIcon({className:'',html:'<div style="background:'+color+';color:#fff;border-radius:50%;width:'+sz+'px;height:'+sz+'px;display:flex;align-items:center;justify-content:center;font-weight:800;font-size:'+(sz<=28?10:11)+'px;border:2px solid #fff;box-shadow:0 2px 8px rgba(0,0,0,.45)">'+lbl+'</div>',iconSize:[sz,sz],iconAnchor:[sz/2,sz/2]});
}
function addLegend(map,items){
  var leg=L.control({position:'bottomright'});
  leg.onAdd=function(){
    var d=L.DomUtil.create('div');
    d.style.cssText='background:#fff;padding:10px 12px;border-radius:8px;font-size:11px;box-shadow:0 2px 8px rgba(0,0,0,.25);line-height:1.9';
    var h='<b style="display:block;margin-bottom:3px">Legend</b>';
    items.forEach(function(i){h+='<span style="display:inline-block;width:12px;height:12px;border-radius:'+(i[2]||'50%')+';background:'+i[1]+';vertical-align:middle;margin-right:5px"></span>'+i[0]+'<br>';});
    d.innerHTML=h; return d;
  };
  leg.addTo(map);
}

function initOptMap(){
  window._oi=true;
  var m=L.map('optionsMap').setView([47.225,-122.32],11); window._om=m;
  aerial(m);
  L.marker([NFI_LAT,NFI_LNG],{icon:mkPin('NFI','#c0392b',36)}).addTo(m).bindPopup('<b>NFI &mdash; Subject Property</b><br>4301 W Valley Hwy E, Sumner WA<br><b style="color:#c0392b">257,775 SF &middot; Expires Feb 2028</b>');
  Object.keys(OPTS).forEach(function(k){var o=OPTS[k];var c=o.anc?'#e67e22':'#2c4fa3';L.marker([o.la,o.ln],{icon:mkPin(o.lb,c)}).addTo(m).bindPopup('<b>'+o.lb+' &mdash; '+o.nm+'</b><br>'+o.ad+'<br><b>'+o.sf+' SF &middot; '+o.rt+'</b>').on('click',function(){selectOption(k);});});
  Object.keys(COMPS).forEach(function(k){var c=COMPS[k];var col=k==='C4'?'#e67e22':'#008C95';L.marker([c.la,c.ln],{icon:mkPin(c.lb,col,24)}).addTo(m).bindPopup('<b>'+c.lb+' &mdash; '+c.nm+'</b><br>'+c.ad+'<br><b style="color:'+col+'">'+c.rt+' &middot; '+c.qt+'</b>');});
  addLegend(m,[['NFI Subject','#c0392b'],['Relocation Option','#2c4fa3'],['Market Reference','#e67e22'],['Lease Comp (teal)','#008C95']]);
}
function selectOption(k){
  var o=OPTS[k]; if(!o) return;
  var isA=o.anc; var c=isA?'var(--A)':'var(--G)';
  var bCls=isA?'b-ref':'b-dir';
  document.getElementById('mapDetailPanel').innerHTML='<div class="dh'+(isA?' am':'')+'"><h4>'+o.lb+' &mdash; '+o.nm+'</h4><p>'+o.ad+'</p></div><div class="dgrid"><div><div class="dlbl">Size</div><div class="dval">'+o.sf+' SF</div></div><div><div class="dlbl">Shell Rate</div><div class="dval" style="color:'+c+'">'+o.rt+'</div></div><div><div class="dlbl">Clear Height</div><div class="dval">'+o.cl+'</div></div><div><div class="dlbl">Type</div><div class="dval"><span class="bdg '+bCls+'">'+o.tp+'</span></div></div><div style="grid-column:1/-1"><div class="dlbl">Landlord</div><div class="dval">'+o.ll+'</div></div></div><div class="co '+(isA?'co-y':'co-t')+'" style="font-size:12px">'+o.nt+'</div>';
  if(window._om) window._om.setView([o.la,o.ln],13);
}

function initCompMap(){
  window._ci=true;
  var m=L.map('compsMap').setView([47.24,-122.33],11); window._cm=m;
  aerial(m);
  L.marker([NFI_LAT,NFI_LNG],{icon:mkPin('NFI','#c0392b',36)}).addTo(m).bindPopup('<b>NFI &mdash; Subject Property</b><br>4301 W Valley Hwy E, Sumner WA<br><b style="color:#c0392b">257,775 SF &middot; Expires Feb 2028</b>');
  Object.keys(COMPS).forEach(function(k){var c=COMPS[k];var col=k==='C4'?'#e67e22':'#008C95';L.marker([c.la,c.ln],{icon:mkPin(c.lb,col,30)}).addTo(m).bindPopup('<b>'+c.lb+' &mdash; '+c.nm+'</b><br>'+c.ad+'<br><b style="color:'+col+'">'+c.rt+' &middot; '+c.qt+' &middot; '+c.tp+'</b><br>'+c.sf+' SF &middot; Esc: '+c.es+' &middot; Free: '+c.fr+' &middot; TI: '+c.ti);});
  Object.keys(OPTS).forEach(function(k){var o=OPTS[k];L.marker([o.la,o.ln],{icon:L.divIcon({className:'',html:'<div style="background:#25273A;color:#fff;border-radius:4px;width:20px;height:20px;display:flex;align-items:center;justify-content:center;font-weight:700;font-size:9px;border:1px solid rgba(255,255,255,.6);box-shadow:0 1px 4px rgba(0,0,0,.4);opacity:.65">'+o.lb+'</div>',iconSize:[20,20],iconAnchor:[10,10]})}).addTo(m).bindPopup('<b>'+o.lb+' &mdash; '+o.nm+'</b><br>'+o.ad);});
  addLegend(m,[['NFI Subject','#c0392b'],['Lease Comp','#008C95'],['SeaTac Comp','#e67e22'],['Option (context)','#25273A','4px']]);
}
function selectComp(k){}

function initMktCharts(){
  window._mi=true;
  new Chart(document.getElementById('vacancyChart'),{type:'bar',data:{labels:['Puyallup/Sumner','Lakewood/Dupont','Federal Way/Auburn','Port of Tacoma/Fife','E Pierce County','Kent','Seattle/Puget Sound'],datasets:[{label:'Vacancy (%)',data:[16.1,12.8,12.9,11.4,10.8,10.3,11.5],backgroundColor:['#e57373','#90caf9','#90caf9','#ffb74d','#b0bec5','#b0bec5','#90a4d4'],borderRadius:6,yAxisID:'y'},{label:'Asking Rate ($/mo)',data:[0.90,0.79,null,0.75,0.96,1.02,1.05],type:'line',borderColor:'#25273A',borderWidth:2,pointRadius:5,pointBackgroundColor:'#FFDF00',tension:.3,yAxisID:'y2'}]},options:{responsive:true,maintainAspectRatio:false,scales:{y:{title:{display:true,text:'Vacancy (%)'},suggestedMax:20},y2:{position:'right',title:{display:true,text:'$/mo PSF'},suggestedMax:1.4,grid:{display:false}}},plugins:{legend:{position:'bottom'}}}});
  new Chart(document.getElementById('tierChart'),{type:'bar',data:{labels:['First-gen ask','First-gen deals','Second-gen ask','Second-gen deals','Sublease ask','NFI Target'],datasets:[{label:'$/mo PSF NNN',data:[1.00,0.90,0.85,0.77,0.68,0.82],backgroundColor:['#e57373','#ffb74d','#90caf9','#81c784','#ce93d8','#FFDF00'],borderRadius:8}]},options:{responsive:true,maintainAspectRatio:false,indexAxis:'y',scales:{x:{title:{display:true,text:'$/mo PSF'},suggestedMax:1.15},y:{grid:{display:false}}},plugins:{legend:{display:false}}}});
}

var PDATA=[{n:'Portside 55',s:0.40},{n:'Hawks Prairie LC Bldg 2',s:0.59},{n:'Fife I-5 Commerce Ctr (A2)',s:0.65},{n:'Fife Commerce Ctr &mdash; Direct (O1)',s:0.78},{n:'Fife Commerce Ctr &mdash; Sublease (A1)',s:0.72},{n:'Tacoma SC Center Bldg 2',s:0.78},{n:'Portside Distribution Ctr (O7)',s:0.79},{n:'Prologis Blair DC (O8)',s:0.85},{n:'Prologis Park Puyallup Bldg 4',s:0.85},{n:'Sumner Central (O5)',s:0.85},{n:'Canyon East Logistics',s:0.86},{n:'LogistiCenter at 167 (O4)',s:0.86},{n:'Pioneer Logistics (O2)',s:0.86},{n:'Sumner West Logistics',s:0.86},{n:'Canyon Point Industrial Ctr A',s:0.88},{n:'Lakewood-Tacoma LC',s:0.90},{n:'Tumwater Logistics Center',s:0.90},{n:'Clover Creek Logistics',s:0.95},{n:'Covington 18',s:0.95},{n:'Kent 228th LC',s:0.95},{n:'E Lake Business Park A',s:0.96},{n:'Fred310 Bldg D',s:0.97},{n:'Fred310 Bldg G',s:0.97},{n:'Fred310 Bldgs A/B',s:1.00},{n:'SeaPORT Logistics Bldg 6',s:1.05},{n:'Tacoma Logistics Center',s:1.05},{n:'Pacific NW Logistics Ctr A',s:1.12},{n:'Pacific NW Logistics Ctr B',s:1.12},{n:'Bridge Point i5 (O3)',s:1.25},{n:'Emerald Logistics',s:1.25},{n:'Bridge Point Tacoma 2MM-D',s:1.20},{n:'Des Moines Creek BP West',s:1.45}];
PDATA.sort(function(a,b){return a.s-b.s;});
function initPrChart(){
  window._pi=true;
  var cols=PDATA.map(function(p){return p.s>=1.0?'#e57373':p.s>=0.85?'#ffb74d':'#81c784';});
  new Chart(document.getElementById('pricingChart'),{type:'bar',data:{labels:PDATA.map(function(p){return p.n.length>34?p.n.substring(0,32)+'...':p.n;}),datasets:[{label:'Shell Rate ($/mo PSF)',data:PDATA.map(function(p){return p.s;}),backgroundColor:cols,borderRadius:4},{label:'NFI Target (~$0.82)',data:new Array(PDATA.length).fill(0.82),type:'line',borderColor:'#2c4fa3',borderWidth:2,borderDash:[6,4],pointRadius:0,fill:false}]},options:{responsive:true,maintainAspectRatio:false,indexAxis:'y',scales:{x:{title:{display:true,text:'$/mo PSF'},suggestedMin:0.3,suggestedMax:1.55},y:{ticks:{font:{size:10}},grid:{display:false}}},plugins:{legend:{position:'top'},tooltip:{callbacks:{label:function(ctx){return'$'+ctx.raw.toFixed(2)+'/mo PSF';}}}}}});
}
function initResCharts(){
  window._ri=true;
  var vS=['Close-in Eastside','Tacoma','SeaTac','Tukwila/Renton','Everett','North Seattle','Kent','E Pierce County','Snohomish County','Bothell/Woodinville','Port of Tacoma/Fife','Seattle/Puget Sound','East King County','Central Seattle','Lakewood/Dupont','Federal Way/Auburn','South Seattle','Puyallup/Sumner'];
  var vR=[0.7,2.5,6.6,6.8,7.2,7.7,10.3,10.8,11.1,11.4,11.4,11.5,11.6,12.7,12.8,12.9,13.4,16.1];
  var vC=vS.map(function(s){return s==='Puyallup/Sumner'?'#e57373':s==='Port of Tacoma/Fife'?'#ffb74d':s==='Seattle/Puget Sound'?'#90a4d4':'#b0bec5';});
  new Chart(document.getElementById('savVacChart'),{type:'bar',data:{labels:vS,datasets:[{label:'Vacancy (%)',data:vR,backgroundColor:vC,borderRadius:4}]},options:{responsive:true,maintainAspectRatio:false,indexAxis:'y',scales:{x:{title:{display:true,text:'Vacancy (%)'},suggestedMax:20},y:{ticks:{font:{size:10}},grid:{display:false}}},plugins:{legend:{display:false},tooltip:{callbacks:{label:function(ctx){return ctx.raw.toFixed(1)+'%';}}}}}});
  var rS=['Tacoma','Port of Tacoma/Fife','Lakewood/Dupont','Puyallup/Sumner','E Pierce County','Kent','Tukwila/Renton','Seattle/Puget Sound','Snohomish County','Federal Way/Auburn','North Seattle','Everett','University Place','East King County','SeaTac','Central Seattle','Bothell/Woodinville','South Seattle','Close-in Eastside'];
  var rR=[0.72,0.75,0.79,0.90,0.96,1.02,1.04,1.05,1.07,1.11,1.21,1.22,1.23,1.28,1.28,1.31,1.57,1.69,1.80];
  var rC=rS.map(function(s){return s==='Puyallup/Sumner'?'#e57373':s==='Port of Tacoma/Fife'?'#ffb74d':s==='Seattle/Puget Sound'?'#90a4d4':'#80cbc4';});
  new Chart(document.getElementById('savRentChart'),{type:'bar',data:{labels:rS,datasets:[{label:'Asking Rent ($/mo PSF)',data:rR,backgroundColor:rC,borderRadius:4},{label:'Regional Avg ($1.05)',data:new Array(rS.length).fill(1.05),type:'line',borderColor:'#25273A',borderWidth:2,borderDash:[5,4],pointRadius:0,fill:false}]},options:{responsive:true,maintainAspectRatio:false,indexAxis:'y',scales:{x:{title:{display:true,text:'$/mo PSF'},suggestedMax:2.0},y:{ticks:{font:{size:10}},grid:{display:false}}},plugins:{legend:{position:'top'},tooltip:{callbacks:{label:function(ctx){return typeof ctx.raw==='number'?'$'+ctx.raw.toFixed(2)+'/mo':'';}}}}}});
}
</script>
</body>
</html>