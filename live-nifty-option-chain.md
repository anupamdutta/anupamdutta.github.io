---
layout: default
title: Live Nifty Option Chain
---

<style>
body{
    background:#020c1b !important;
    font-family: 'JetBrains Mono', monospace;
    color:#e6edf3;
}

/* CARD */
.card{
    max-width:520px;
    margin:40px auto;
    border:1px solid #0f2a4d;
    border-radius:10px;
    padding:12px;
    background:#020c1b;
    position:relative;
}

/* HEADER */
.title{
    color:#4cc9f0;
    font-size:14px;
}
.subtitle{
    font-size:9px;
    color:#7fa7c7;
}

/* META */
.meta{
    font-size:10px;
    margin-top:8px;
    text-align:right;
}

/* BUTTON */
.btn{
    position:absolute;
    top:10px;
    right:10px;
    background:#1f4aa8;
    border:none;
    color:white;
    padding:6px 10px;
    border-radius:6px;
    cursor:pointer;
}

/* CHART */
.chart-title{
    font-size:9px;
    color:#7fa7c7;
    margin-top:6px;
}

/* TABLE */
.table-wrap{
    max-width:900px;
    margin:20px auto;
    overflow-x:auto;
}

table{
    width:100%;
    border-collapse:collapse;
    font-size:10px;
    background:#020c1b;
}

th{
    background:#102a4d;
    padding:5px;
}

td{
    padding:4px;
    text-align:center;
}

tr:nth-child(even){
    background:#0a1a33;
}

.atm{
    background:#1f6f4a;
}

.pos{ color:#4ade80; }
.neg{ color:#f87171; }

/* subtle separators */
td:nth-child(5), th:nth-child(5){
    border-right:1px solid rgba(255,255,255,0.08);
}
td:nth-child(6), th:nth-child(6){
    border-right:1px solid rgba(255,255,255,0.08);
}
</style>

<div class="card">

    <button class="btn" onclick="load()">Refresh</button>

    <div class="title">GammaGrid Insights</div>
    <div class="subtitle">Precision Options Trading, Systems, and Insights</div>

    <div class="meta" id="meta"></div>

    <div class="chart-title">Net Gamma Exposure (GEX) by Strike</div>
    <canvas id="chart" height="120"></canvas>

</div>

<div class="table-wrap">
    <table id="table"></table>
</div>

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
    return new Date(ts).toLocaleString('en-IN', {
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
    return new Date(ts).toLocaleDateString('en-IN', {
        timeZone:'Asia/Kolkata',
        day:'2-digit',
        month:'short',
        year:'numeric'
    });
}

function render(meta, rows){

    // META
    document.getElementById("meta").innerHTML =
    `${meta.symbol} | Exp: ${formatDate(meta.expiry)}<br>
    Spot ${meta.spot.toFixed(2)} | ATM ${meta.atm} | SF ${meta.sf.toFixed(2)} | IV ${meta.iv}%<br>
    ${formatTime(meta.timestamp)}`;

    // CHART
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
                x:{
                    ticks:{color:'#9fb3c8',font:{size:8}},
                    grid:{display:false}
                },
                y:{
                    ticks:{
                        color:'#9fb3c8',
                        font:{size:8},
                        callback:val=>format(val)
                    },
                    grid:{color:'rgba(255,255,255,0.05)'}
                }
            }
        }
    });

    // TABLE
    let html = `
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

    document.getElementById("table").innerHTML = html;
}

async function load(){
    const res = await fetch(URL);
    const data = await res.json();
    render(data.meta, data.rows);
}

load();
</script>
