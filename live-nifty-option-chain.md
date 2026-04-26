---
layout: page
title: Live Nifty Option Chain
---

<div class="oc-page">

  <div class="oc-header">
    <button class="oc-btn" onclick="load()">Refresh</button>

    <div class="oc-title">GammaGrid Insights</div>
    <div class="oc-subtitle">Precision Options Trading, Systems, and Insights</div>

    <div class="oc-meta" id="meta"></div>
  </div>

  <div class="oc-chart">
    <div class="oc-chart-title">Net Gamma Exposure (GEX) by Strike</div>
    <canvas id="chart" height="120"></canvas>
  </div>

  <div class="table-wrap">
    <table id="table"></table>
  </div>

  <div class="oc-disclaimer" id="disclaimer"></div>

  <div class="gex-panel">

  <h2>Understanding Gamma Exposure (GEX)</h2>

  <p>
    Gamma Exposure (GEX) measures how market makers (dealers) are positioned in options.
    It helps identify key levels where price may stabilize or become volatile.
  </p>

  <h3>🟢 Positive GEX (Dealer Long Gamma)</h3>
  <ul>
    <li>Dealers hedge by <b>selling into rallies</b> and <b>buying dips</b></li>
    <li>Market becomes <b>range-bound</b> and stable</li>
    <li>Volatility is typically <b>low</b></li>
  </ul>

  <h3>🔴 Negative GEX (Dealer Short Gamma)</h3>
  <ul>
    <li>Dealers hedge by <b>buying into rallies</b> and <b>selling dips</b></li>
    <li>Market becomes <b>directional / trending</b></li>
    <li>Volatility expands quickly</li>
  </ul>

  <h3>📍 Key Concepts</h3>
  <ul>
    <li><b>Call Wall:</b> Strike with highest call OI/GEX → resistance</li>
    <li><b>Put Wall:</b> Strike with highest put OI/GEX → support</li>
    <li><b>Gamma Flip:</b> Level where total GEX shifts from +ve to -ve</li>
  </ul>

  <h3>📊 How to Use</h3>
  <ul>
    <li>Trade ranges when GEX is positive</li>
    <li>Expect breakouts when GEX is negative</li>
    <li>Watch Flip level for regime change</li>
  </ul>

  <div class="gex-cta">
    🚀 Want to go deeper?
    Learn advanced GEX strategies, dealer positioning models, and real trade setups.<br>
    <b>Join the GammaGrid course → Coming Soon</b>
  </div>

</div>

</div>

<style>
/* ===== PAGE ===== */
.oc-page{
  max-width:1200px;
  margin:auto;
  padding:20px;
  border-radius:12px;

  background:rgba(2,6,23,0.9);
  border:1px solid rgba(56,189,248,0.15);

  font-family:"JetBrains Mono", monospace;
  color:#e2e8f0;
}

/* ===== HEADER ===== */
.oc-header{
  position:relative;
  margin-bottom:20px;
}

.oc-title{
  color:#38bdf8;
  font-size:18px;
  font-weight:600;
}

.oc-subtitle{
  font-size:12px;
  color:#64748b;
}

.oc-meta{
  font-size:11px;
  color:#94a3b8;
  margin-top:6px;
}

/* BUTTON */
.oc-btn{
  position:absolute;
  right:0;
  top:0;
  padding:6px 10px;
  border-radius:6px;
  border:1px solid #38bdf8;
  background:#020617;
  color:#38bdf8;
  cursor:pointer;
}

/* ===== CHART ===== */
.oc-chart{
  margin-bottom:20px;
}

.oc-chart-title{
  font-size:11px;
  color:#64748b;
  margin-bottom:6px;
}

/* ===== TABLE ===== */
.table-wrap{
  overflow-x:auto;
}

/* MAIN TABLE */
#table{
  width:100%;
  border-collapse:separate;
  border-spacing:0;

  background:rgba(2,6,23,0.7);
  border-radius:10px;
  overflow:hidden;

  font-size:11px;
}

/* HEADER */
#table thead th{
  padding:6px 8px;
  font-size:10px;
  color:#94a3b8;
  border-bottom:1px solid rgba(255,255,255,0.1);
}

#table thead tr:first-child th{
  color:#38bdf8;
  border-bottom:1px solid rgba(56,189,248,0.2);
}

/* CELLS */
#table td{
  padding:5px 8px;
  color:#e2e8f0;
  border-bottom:1px solid rgba(255,255,255,0.04);
  white-space:nowrap;
}

/* ROWS */
#table tbody tr{
  background:rgba(2,6,23,0.5);
  transition:0.15s;
}

#table tbody tr:hover{
  background:rgba(56,189,248,0.06);
}

/* ATM ROW */
#table tr.atm{
  background:rgba(34,197,94,0.25);   /* stronger base */
  box-shadow: inset 0 0 0 1px rgba(34,197,94,0.6); /* crisp border */
}

#table tr.atm td{
  border-top:1px solid rgba(34,197,94,0.6);
  border-bottom:1px solid rgba(34,197,94,0.6);
}

/* STRIKE */
#table td:nth-child(6){
  color:#38bdf8;
  font-weight:600;
}

/* CALL / PUT GEX */
.call-gex{
  color:#22c55e;
  font-weight:600;
  text-shadow:0 0 6px rgba(34,197,94,0.5);
}

.put-gex{
  color:#ef4444;
  font-weight:600;
  text-shadow:0 0 6px rgba(239,68,68,0.5);
}

.call-gex{ color:#00ff00 !important; }
.put-gex{ color:#ff0000 !important; }

/* DIVIDERS */
#table td:nth-child(5),
#table th:nth-child(5){
  border-right:1px solid rgba(255,255,255,0.05);
}

#table td:nth-child(6),
#table th:nth-child(6){
  border-right:1px solid rgba(255,255,255,0.08);
}

/* ROUNDED CORNERS FIX */
#table thead tr:first-child th:first-child {
  border-top-left-radius:10px;
}
#table thead tr:first-child th:last-child {
  border-top-right-radius:10px;
}
#table tbody tr:last-child td:first-child {
  border-bottom-left-radius:10px;
}
#table tbody tr:last-child td:last-child {
  border-bottom-right-radius:10px;
}

/* ===== MOBILE ===== */
@media (max-width:768px){

  .bid,
  .ask{
    display:none;
  }

  .oc-page{
    padding:12px;
  }

  #table{
    font-size:10px;
  }

  #table td, #table th{
    padding:4px 6px;
  }

  .oc-title{
    font-size:14px;
  }

  .oc-subtitle{
    font-size:10px;
  }
  

}

/* DISCLAIMER */
.oc-disclaimer{
  margin-top:20px;
  font-size:11px;
  color:#64748b;
  text-align:center;
}

@media (max-width:768px){
  #table td, #table th{
    text-align:center;
  }
}

.gex-panel{
  margin-top:30px;
  padding:20px;
  border-radius:12px;

  background:rgba(2,6,23,0.85);
  border:1px solid rgba(56,189,248,0.15);

  font-size:13px;
  line-height:1.6;
  color:#cbd5e1;
}

.gex-panel h2{
  color:#38bdf8;
  margin-bottom:10px;
}

.gex-panel h3{
  margin-top:18px;
  color:#94a3b8;
  font-size:13px;
}

.gex-panel ul{
  margin:6px 0 0 18px;
}

.gex-panel li{
  margin-bottom:4px;
}

.gex-cta{
  margin-top:20px;
  padding:12px;
  border-radius:8px;

  background:rgba(34,197,94,0.1);
  border:1px solid rgba(34,197,94,0.3);

  color:#4ade80;
  font-weight:500;
}
</style>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
const URL = "https://script.google.com/macros/s/AKfycbzOzfuXYAtNPqzmkZSabUPY_ed2x7TVzVKcSD-wfGRZn3TWcaEQSbMAYqC269r4DNfv/exec";

let chart;

function format(n){
    if(Math.abs(n)>=1e6) return (n/1e6).toFixed(1)+'M';
    if(Math.abs(n)>=1e3) return (n/1e3).toFixed(1)+'K';
    return Math.round(n);
}

function formatTime(ts){
    return new Date(ts).toLocaleString('en-IN',{
        timeZone:'Asia/Kolkata',
        day:'2-digit',
        month:'short',
        year:'numeric',
        hour:'2-digit',
        minute:'2-digit',
        hour12:true
    });
}

function formatDate(ts){
    return new Date(ts).toLocaleDateString('en-IN',{
        timeZone:'Asia/Kolkata',
        day:'2-digit',
        month:'short',
        year:'numeric'
    });
}

function render(meta, rows){

    document.getElementById("meta").innerHTML =
    `${meta.symbol} | Exp: ${formatDate(meta.expiry)}<br>
    Spot ${meta.spot.toFixed(2)} | ATM ${meta.atm} | SF ${meta.sf.toFixed(2)} | ATM IV ${meta.iv}%<br>
    ${formatTime(meta.timestamp)}`;

    const strikes = rows.map(r=>r.strike);
    const net = rows.map(r=>r.net_gex);

    if(chart) chart.destroy();

    chart = new Chart(document.getElementById('chart'), {
        type:'bar',
        data:{
            labels:strikes,
            datasets:[{
                data:net,
                backgroundColor: net.map(x=> x>0?'#22c55e':'#ef4444')
            }]
        },
        options:{
            plugins:{legend:{display:false}},
            scales:{
                x:{ticks:{color:'#94a3b8',font:{size:10}},grid:{display:false}},
                y:{
                    ticks:{color:'#94a3b8',callback:v=>format(v)},
                    grid:{color:'rgba(255,255,255,0.05)'}
                }
            }
        }
    });

    let isMobile = window.innerWidth <= 768;

    let html = "";

    if(isMobile){

        html = `
        <thead>
        <tr>
            <th colspan="3">CALL</th>
            <th>STRIKE</th>
            <th colspan="3">PUT</th>
        </tr>
        <tr>
            <th>LTP</th><th>OI</th><th>GEX</th>
            <th></th>
            <th>LTP</th><th>OI</th><th>GEX</th>
        </tr>
        </thead>
        <tbody>
        `;

    } else {

        html = `
        <thead>
        <tr>
            <th colspan="5">CALL</th>
            <th>STRIKE</th>
            <th colspan="5">PUT</th>
        </tr>
        <tr>
            <th class="bid">Bid</th>
            <th>LTP</th>
            <th class="ask">Ask</th>
            <th>OI</th>
            <th class="gex">GEX</th>
            <th></th>             
            <th class="bid">Bid</th>
            <th>LTP</th>
            <th class="ask">Ask</th>
            <th>OI</th>
            <th class="gex">GEX</th>
        </tr>
        </thead>
        <tbody>
        `;
    }

    rows.forEach(r=>{
        const atm = r.strike === meta.atm ? "atm" : "";

        if(isMobile){

            html += `
            <tr class="${atm}">
                <td>${r.call_ltp.toFixed(2)}</td>
                <td>${format(r.call_oi)}</td>
                <td class="call-gex">${format(r.call_gex)}</td>

                <td><b>${r.strike}</b></td>

                <td>${r.put_ltp.toFixed(2)}</td>
                <td>${format(r.put_oi)}</td>
                <td class="put-gex">${format(r.put_gex)}</td>
            </tr>
            `;

        } else {

            html += `
            <tr class="${atm}">
                <td class="bid">${r.call_bid.toFixed(2)}</td>
                <td>${r.call_ltp.toFixed(2)}</td>
                <td class="ask">${r.call_ask.toFixed(2)}</td>
                <td>${format(r.call_oi)}</td>
                <td class="call-gex">${format(r.call_gex)}</td>

                <td><b>${r.strike}</b></td>

                <td class="bid">${r.put_bid.toFixed(2)}</td>
                <td>${r.put_ltp.toFixed(2)}</td>
                <td class="ask">${r.put_ask.toFixed(2)}</td>
                <td>${format(r.put_oi)}</td>
                <td class="put-gex">${format(r.put_gex)}</td>
            </tr>
            `;
        }
    });

    html += `</tbody>`;

    document.getElementById("table").innerHTML = html;

    document.getElementById("disclaimer").innerHTML = `
    ⚠️ Data may be delayed. Last updated: ${formatTime(meta.timestamp)}<br>
    Not financial advice. For informational and educational purposes only.
    `;
}

async function load(){
    const res = await fetch(URL);
    const data = await res.json();
    render(data.meta, data.rows);
}

load();
</script>
