---
permalink: /
title: ""
author_profile: true
classes: wide
redirect_from: 
  - /about/
  - /about.html
---

I am a Ph.D. candidate in Finance at the Norwegian School of Economics. My research interests are asset pricing, market microstructure, and financial intermediation. 

I will be joining the Banco de España as a Research Economist in the fall.
<div style="text-align: justify; margin-bottom: 20px;">
    <div>
        <h3 style="display: inline; margin-bottom: 0;">Inflation Risk and Yield Spread Changes</h3>
        <button onclick="toggleAbstract()" id="abstractButton" class="custom-button">Abstract</button>
        <button onclick="window.location.href='https://papers.ssrn.com/abstract=4299512';" class="custom-button">SSRN Link</button>
    </div>
</div>
<div id="abstract" style="display: none;">
    <text>
    Inflation risk explains more than 40% of the systematic variation of yield spread changes beyond standard structural factors. I show that changes in expected inflation, volatility, and cyclicality are significant determinants of yield spread changes. A structural model with a stochastic price index and sticky cash flow accounts for these patterns and delivers further implications. In the cross-section, the model predicts increasing loading patterns on leverage and cash-flow flexibility. In the time series, the model predicts diminished effects during periods of high expected inflation. I find empirical support for the model’s predictions.
    </text>
</div>

<div style="text-align: justify; margin-bottom: 50px; margin-top: 50px;">
    <div>
       <h3 style="display: inline; margin-bottom: 0;">Good Inflation, Bad Inflation: Implications for Risky Asset Prices,</h3>
        <span style="font-size: 14px;">with Berardino Palazzo and Ram Yamarthy</span>
        <button onclick="toggleAbstract2()" id="abstractButton2" class="custom-button">Abstract</button>
        <button onclick="window.location.href='https://papers.ssrn.com/abstract=4299512';" class="custom-button">SSRN Link</button>
    </div>
</div>
<div id="abstract2" style="display: none;">
    <text>
    In times of market-perceived “good inflation,” when inflation news is positively correlated with real economic growth, shocks to expected inflation substantially reduce corporate credit spreads and raise equity valuations. Meanwhile in times of “bad inflation,” these effects are attenuated and the opposite can take place. These dynamics naturally arise from an equilibrium asset pricing model with a time-varying inflation-growth relationship and persistent macroeconomic expectations. Using inflation swap prices we study how expected inflation is priced in firm-level credit spreads and equity returns, and uncover evidence of a time-varying inflation beta.
    </text>
</div>

<style>
    .custom-button {
        margin-left: 10px;
        padding: 5px 10px;
        font-size: 14px;
        color: blue;
        background-color: white;
        border: 1px solid blue;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s, box-shadow 0.3s;
    }

    .custom-button:hover {
        background-color: rgba(0, 0, 255, 0.1);
        box-shadow: 0 0 5px rgba(0, 0, 255, 0.5);
    }
</style>

<script>
    function toggleAbstract() {
        var abstractDiv = document.getElementById("abstract");
        var button = document.getElementById("abstractButton");
        if (abstractDiv.style.display === "none") {
            abstractDiv.style.display = "block";
            button.innerText = "Hide Abstract";
        } else {
            abstractDiv.style.display = "none";
            button.innerText = "Abstract";
        }
    }
    
    function toggleAbstract2() {
        var abstractDiv = document.getElementById("abstract2");
        var button = document.getElementById("abstractButton2");
        if (abstractDiv.style.display === "none") {
            abstractDiv.style.display = "block";
            button.innerText = "Hide Abstract";
        } else {
            abstractDiv.style.display = "none";
            button.innerText = "Abstract";
        }
    }
</script>
