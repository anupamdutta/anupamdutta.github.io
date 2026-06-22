---
layout: page
title: Risk Terminal Web
permalink: /risk-terminal-web/
---

<div class="mmr-layout">

  <!-- ================= LEFT PANEL ================= -->
  <div class="mmr-left">

    <div class="iv-container">

      <p class="subtext">
        Risk Strike Estimation Engine
      </p>

      <!-- AUTH -->
      <div class="iv-grid">

        <div class="field">
          <label>App Key</label>
          <input id="appKey">
        </div>

        <div class="field">
          <label>App Token</label>

          <div class="auth-row">
            <input id="appToken" type="password">
            <button class="btn-small" onclick="toggleToken(event)">Show</button>
          </div>

        </div>

      </div>

      <br>

      <!-- INPUTS -->
      <div class="iv-grid">
      
        <div class="full-width">
          <div class="triple-row">
      
            <div class="field">
              <label>Spot Future</label>
              <input id="sf" type="number">
            </div>
      
            <div class="field">
              <label>ATM Strike</label>
              <input id="atm" type="number">
            </div>
      
          </div>
        </div>
      
        <div class="full-width">
          <div class="triple-row">
      
            <div class="field">
              <label>ATM Option Type</label>
              <select id="kind">
                <option>PUT</option>
                <option>CALL</option>
              </select>
            </div>
      
            <div class="field">
              <label>ATM Option Premium</label>
              <input id="premium" type="number">
            </div>
      
          </div>
        </div>
      
        <div class="full-width">
          <div class="triple-row">
      
            <div class="field">
              <label>Strike Difference</label>
              <input id="diff" type="number" value="50">
            </div>
      
            <div class="field">
              <!-- spacer -->
            </div>
      
          </div>
        </div>
      
      </div>
      <button class="run-btn" onclick="runRiskTerminal()">
        Calculate Risk Strike
      </button>

    </div>

  </div>

  <!-- ================= RIGHT PANEL ================= -->

  <div class="mmr-right">

    <div class="mmr-card" id="box-input">

      <div class="mmr-card-title">
        [ INPUT PARAMETERS ]
      </div>

      Waiting for input...

    </div>

    <div class="mmr-card" id="box-model">

      <div class="mmr-card-title">
        [ RISK ADJUSTED LEVELS ]
      </div>

      Waiting for calculation...

    </div>

  </div>

</div>

<div class="mmr-about-full">

  <div class="mmr-about-box">

    <div class="mmr-about-title">
      [ ABOUT RISK TERMINAL ]
    </div>

    <p>
      <b>Risk Strike Terminal</b> is a proprietary quantitative research tool developed to estimate the <b>Risk Strike</b> that option market makers are implicitly expecting while quoting option premiums. Rather than forecasting market direction, the terminal derives a risk-adjusted strike level from observed option prices using a proprietary mathematical framework designed for advanced options analysis.
    </p>
    
    <p>
      The Risk Strike Terminal is intended to be used alongside the <b>MMR Terminal</b>. While the MMR Terminal helps estimate market maker risk and implied pricing dynamics, the Risk Strike Terminal extends that analysis by identifying the expected risk-adjusted strike, corresponding option values, synthetic forward level, and overall market risk zone. Together, both tools provide a comprehensive quantitative framework for option market analysis.
    </p>
    
    <p>
      To interpret the outputs meaningfully, users should have a solid understanding of <b>Option Pricing Theory</b>, including the <b>Black-Scholes Model</b>, <b>Put-Call Parity</b>, and <b>Implied Volatility</b>. These concepts are essential for practical application of the results in real-world option trading and quantitative research.
    </p>
    
    <p>
      If you are new to quantitative options or would like a deeper understanding of the methodology behind these tools, you are encouraged to join my training program, where the underlying theory, mathematical foundations, and practical market applications are explained in detail using live examples.
    </p>
    
    <p>
      Access to the terminal requires a valid <b>App Key</b> and <b>App Token</b>, available through subscription.
    </p>
    
    <p style="color:#facc15;">
      ⚠ This software is provided strictly for educational and research purposes. It should not be interpreted as financial advice or a recommendation to buy or sell any financial instrument.
    </p>
    
    <p>
      <b>Developer:</b> Anupam Dutta<br>
      📞 +91-8240775462
    </p>

  </div>

</div>

<!-- ================= MODAL ================= -->

<div id="errorModal" class="modal">

  <div class="modal-content">

    <span class="close" onclick="closeModal()">&times;</span>

    <p id="modalText"></p>

  </div>

</div>

<script>

function toggleToken(event){

    const f=document.getElementById("appToken");
    const b=event.target;

    if(f.type==="password"){
        f.type="text";
        b.innerText="Hide";
    }else{
        f.type="password";
        b.innerText="Show";
    }

}

function showError(msg){

    document.getElementById("modalText").innerText=msg;
    document.getElementById("errorModal").style.display="flex";

}

function closeModal(){

    document.getElementById("errorModal").style.display="none";

}

async function runRiskTerminal(){

    const payload={

        appKey:document.getElementById("appKey").value.trim(),
        appToken:document.getElementById("appToken").value.trim(),

        sf:Number(document.getElementById("sf").value),
        atm:Number(document.getElementById("atm").value),
        premium:Number(document.getElementById("premium").value),
        diff:Number(document.getElementById("diff").value),
        kind:document.getElementById("kind").value

    };

    if(!payload.appKey) return showError("App Key required");
    if(!payload.appToken) return showError("Token required");

    document.getElementById("box-input").innerHTML=`
    <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>
    Loading...
    `;

    document.getElementById("box-model").innerHTML=`
    <div class="mmr-card-title">[ RISK ADJUSTED LEVELS ]</div>
    Calculating...
    `;

    try{

        const res=await fetch("https://script.google.com/macros/s/AKfycbwLoAn4ROpQ3ZwwIdVcoUyF6qegaFnMJ8wonTxV0FTzpO6v-0Olu0Yu4P9uJ15HHlfcmA/exec",{

            method:"POST",
            body:JSON.stringify(payload)

        });

        const json=await res.json();

        if(json.error){

            showError(json.message);
            return;

        }

        document.getElementById("box-input").innerHTML=`

        <div class="mmr-card-title">[ INPUT PARAMETERS ]</div>

        Spot Future : ${payload.sf}<br>
        ATM Strike : ${payload.atm}<br>
        Option Type : ${payload.kind}<br>
        Premium : ${payload.premium}<br>
        Strike Difference : ${payload.diff}

        `;

        let color="#22c55e";

        if(json.zone==="MODERATE")
            color="#facc15";

        if(json.zone==="HIGH")
            color="#fb923c";

        if(json.zone==="EXTREME")
            color="#ef4444";

        document.getElementById("box-model").innerHTML=`

        <div class="mmr-card-title">
        [ RISK ADJUSTED LEVELS ]
        </div>

        Risk Strike : ${json.riskStrike}<br>
        Put Price : ${json.putPrice}<br>
        Call Price : ${json.callPrice}<br>
        Synthetic Forward : ${json.syntheticForward}<br><br>

        <b style="color:${color}">
        Risk Zone : ${json.zone}
        </b>

        <br><br>

        <div style="font-size:12px;color:#facc15">
        Educational purposes only.<br>
        Not financial advice.
        </div>

        `;

    }

    catch(e){

        showError("Unable to connect to server.");

    }

}

</script>
