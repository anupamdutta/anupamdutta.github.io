---
layout: page
title: Live Nifty Option Chain
---

<div class="option-chain-page">

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

</div>

<!-- ===== STYLES (SCOPED ONLY) ===== -->
<style>
.option-chain-page{
  max-width:1200px;
  margin:auto;
  font-family:"JetBrains Mono", monospace;
  color:#cbd5e1;

  background: rgba(2,6,23,0.88);
  padding: 20px;
  border-radius: 12px;
  border: 1px solid rgba(56,189,248,0.15);
}

/* HEADER */
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
  margin-bottom:6px;
}

.oc-meta{
  font-size:11px;
  color:#94a3b8;
}

/* BUTTON */
.oc-btn{
  position:absolute;
  right:0;
  top:0;
  background:#020617;
  border:1px solid #38bdf8;
  color:#38bdf8;
  padding:6px 10px;
  border-radius:6px;
  cursor:pointer;
}
.oc-btn:hover{
  box-shadow:0 0 10px rgba(56,189,248,0.5);
}

/* CHART */
.oc-chart{
  margin-bottom:20px;
}

.oc-chart-title{
  font-size:11px;
  color:#64748b;
  margin-bottom:6px;
}

/* TABLE */
.table-wrap{
  overflow-x:auto;
}

#table{
  width:100%;
  border-collapse:collapse;
  font-size:11px;

  background: rgba(2,6,23,0.65);
}

/* HEADER */
#table thead th{
  font-size:10px;
  color:#64748b;
  padding:6px 8px;
  border-bottom:1px solid rgba(255,255,255,0.1);
}

#table thead tr:first-child th{
  color:#38bdf8;
  border-bottom:1px solid rgba(56,189,248,0.2);
}

/* CELLS */
#table td{
  padding:5px 8px;
  border-bottom:1px solid rgba(255,255,255,0.04);
}

#table tbody tr{
  background: rgba(2,6,23,0.45);
}

/* ROW HOVER */
#table tbody tr:hover{
  background:rgba(56,189,248,0.05);
}

/* ATM */
#table tr.atm{
  background:rgba(34,197,94,0.12);
}

/* STRIKE COLUMN */
#table td:nth-child(6){
  color:#38bdf8;
  font-weight:600;
}

/* COLORS */
.pos{
  color:#22c55e;
}
.neg{
  color:#ef4444;
}

/* DIVIDERS */
#table td:nth-child(5),
#table th:nth-child(5){
  border-right:1px solid rgba(255,255,255,0.06);
}

#table td:nth-child(6),
#table th:nth-child(6){
  border-right:1px solid rgba(255,255,255,0.08);
}

/* DISCLAIMER */
.oc-disclaimer{
  margin-top:20px;
  font-size:11px;
  color:#64748b;
  text-align:center;
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
    Spot ${meta.spot.toFixed(2)} | ATM ${meta.atm} | SF ${meta.sf.toFixed(2)} | IV ${meta.iv}%<br>
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

    let html = `
    <thead>
    <tr>
        <th colspan="5">CALL</th>
        <th>STRIKE</th>
        <th colspan="5">PUT</th>
    </tr>
    <tr>
        <th>Bid</th><th>LTP</th><th>Ask</th><th>OI</th><th>GEX</th>
        <th></th>
        <th>Bid</th><th>LTP</th><th>Ask</th><th>OI</th><th>GEX</th>
    </tr>
    </thead>
    <tbody>
    `;

    rows.forEach(r=>{
        const atm = r.strike === meta.atm ? "atm" : "";

        html += `
        <tr class="${atm}">
            <td>${r.call_bid.toFixed(2)}</td>
            <td>${r.call_ltp.toFixed(2)}</td>
            <td>${r.call_ask.toFixed(2)}</td>
            <td>${format(r.call_oi)}</td>
            <td class="pos">${format(r.call_gex)}</td>

            <td><b>${r.strike}</b></td>

            <td>${r.put_bid.toFixed(2)}</td>
            <td>${r.put_ltp.toFixed(2)}</td>
            <td>${r.put_ask.toFixed(2)}</td>
            <td>${format(r.put_oi)}</td>
            <td class="neg">${format(r.put_gex)}</td>
        </tr>
        `;
    });

    html += `</tbody>`;

    document.getElementById("table").innerHTML = html;

    document.getElementById("disclaimer").innerHTML =
    `⚠️ Data may be delayed. Last updated: ${formatTime(meta.timestamp)}<br>
     Not financial advice. For informational purposes only.`;
}

async function load(){
    const res = await fetch(URL);
    const data = await res.json();
    render(data.meta, data.rows);
}

load();
</script>
