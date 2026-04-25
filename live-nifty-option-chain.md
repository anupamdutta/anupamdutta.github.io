---
layout: default
title: Live Nifty Option Chain
---

<style>
body{
    background:#020c1b;
    color:white;
    font-family:monospace;
}

.container{
    max-width:520px;
    margin:20px auto;
    background:#031326;
    padding:15px;
    border-radius:12px;
    box-shadow:0 0 20px rgba(0,0,0,0.6);
    position:relative;
}



/* header */
.title{
    color:#38bdf8;
    font-size:18px;
}
.subtitle{
    font-size:11px;
    color:#9fb3c8;
}

/* meta */
.meta{
    margin-top:10px;
    font-size:12px;
    color:#cbd5e1;
}

/* button */
.btn{
    position:absolute;
    right:15px;
    top:15px;
    background:#1f4aa8;
    border:none;
    color:white;
    padding:6px 10px;
    border-radius:6px;
    cursor:pointer;
}

/* chart */
canvas{
    margin-top:10px;
}

/* table */
table{
    width:100%;
    border-collapse:collapse;
    margin-top:10px;
    font-size:11px;
}

th{
    background:#1f3b5c;
    padding:6px;
}

td{
    padding:4px;
    text-align:center;
}

tr:nth-child(even){
    background:#0a1a33;
}

.atm{
    background:#1e6f3d !important;
}

.pos{ color:#22c55e; }
.neg{ color:#ef4444; }

.footer{
    text-align:center;
    margin-top:10px;
    font-size:10px;
    color:#64748b;
}
</style>

<div class="container" id="capture">

<button class="btn" onclick="load()">Refresh</button>

<div class="title">GammaGrid Insights</div>
<div class="subtitle">Precision Options Trading, Systems, and Insights</div>

<div class="meta" id="meta"></div>
<div style="
    margin-top:10px;
    font-size:12px;
    color:#9fb3c8;
    font-family:monospace;
">
    Net Gamma Exposure (GEX) by Strike
</div>

<canvas id="chart" height="120"></canvas>

<table id="table"></table>


</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
const URL = "https://script.google.com/macros/s/AKfycbzOzfuXYAtNPqzmkZSabUPY_ed2x7TVzVKcSD-wfGRZn3TWcaEQSbMAYqC269r4DNfv/exec";

function format(n){
    if(Math.abs(n)>=1e6) return (n/1e6).toFixed(1)+'M';
    if(Math.abs(n)>=1e3) return (n/1e3).toFixed(1)+'K';
    return n;
}

let chart;

function render(meta, rows){

    // META
    document.getElementById("meta").innerHTML =
    `${meta.symbol} | Exp: ${meta.expiry}<br>
    Spot ${meta.spot.toFixed(2)} | ATM ${meta.atm} | SF ${meta.sf.toFixed(2)} | IV ${meta.iv}%<br>
    ${meta.timestamp}`;

    // CHART DATA
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
                x:{ticks:{color:'#9fb3c8',font:{size:8}}},
                y:{ticks:{
                    color:'#9fb3c8',
                    font:{size:8},
                    callback:val=>format(val)
                }}
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
        const atm = r.strike == meta.atm ? "atm" : "";
        html += `
        <tr class="${atm}">
            <td>${r.call_bid.toFixed(2)}</td>
            <td>${r.call_ltp.toFixed(2)}</td>
            <td>${r.call_ask.toFixed(2)}</td>
            <td>${format(r.call_oi)}</td>
            <td class="pos">${format(r.call_gex)}</td>

            <td>${r.strike}</td>

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
