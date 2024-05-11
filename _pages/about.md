---
layout: archive
permalink: /
title: ""
author_profile: true
classes: wide
redirect_from: 
  - /about/
  - /about.html
---
<titlecolor id="about" style="margin-bottom: 20px;">About Me</titlecolor><br>
<text style="font-size: $type-size-4;">
I am a Ph.D. candidate in Finance at the Norwegian School of Economics. My research interests are asset pricing, market microstructure, and financial intermediation. 
 </text><br>
 <text style="font-size: $type-size-4;">
I will be joining the Banco de España as a Research Economist in the fall.
 </text><br>

<titlecolor id="research">Working Papers</titlecolor>


<div style="text-align: justify; margin-bottom: 20px;margin-top: 20px;">
    <div>
       <subtitlecolor style="display: inline; margin-bottom: 0; ">Good Inflation, Bad Inflation: Implications for Risky Asset Prices</subtitlecolor>
        <button onclick="toggleAbstract2()" id="abstractButton2" class="custom-button small">Abstract</button>
        <button onclick="window.location.href='https://papers.ssrn.com/abstract=4299512';" class="custom-button small">SSRN</button><br>
        <text style="font-size: $type-size-6;">April 2024</text> <br>
        <text style="font-size: $type-size-6;">Joint with Berardino Palazzo and Ram Yamarthy</text> 
    </div>
</div>
<div id="abstract2" style="display: none;">
    <text>
    In times of market-perceived “good inflation,” when inflation news is positively correlated with real economic growth, shocks to expected inflation substantially reduce corporate credit spreads and raise equity valuations. Meanwhile in times of “bad inflation,” these effects are attenuated and the opposite can take place. These dynamics naturally arise from an equilibrium asset pricing model with a time-varying inflation-growth relationship and persistent macroeconomic expectations. Using inflation swap prices we study how expected inflation is priced in firm-level credit spreads and equity returns, and uncover evidence of a time-varying inflation beta.
    </text>
</div>

<div style="text-align: justify; margin-bottom: 20px; margin-top: 50px;">
    <div>
        <subtitlecolor style="display: inline; margin-bottom: 0; ">Inflation Risk and Yield Spread Changes</subtitlecolor>
        <button onclick="toggleAbstract()" id="abstractButton" class="custom-button small">Abstract</button>
        <button onclick="window.location.href='https://papers.ssrn.com/abstract=4299512';" class="custom-button small">SSRN</button><br>
                <text style="font-size: $type-size-6;">May 2024</text> 

    </div>
</div>
<div id="abstract" style="display: none;">
    <text>
    Inflation risk explains a significant share of the systematic variation of yield spread changes beyond standard structural factors. Movements in expected inflation directly affect the real value of debt and, consequently, bond prices. I show that shocks to inflation expectation, volatility, and cyclicality are significant determinants of yield spread changes. A model with a stochastic price index and sticky cash flow explains these patterns and delivers additional implications with empirical support. Loading patterns become more pronounced with higher ex-ante default risk and cash-flow flexibility but weaken during periods of high expected inflation.
    </text>
</div>


<div style="text-align: justify; margin-bottom: 50px; margin-top: 50px;">
    <div>
        <subtitlecolor style="display: inline; margin-bottom: 0;">Hedge Funds, Prime Brokers, and Corporate Bond Offerings</subtitlecolor>
        <button onclick="toggleAbstract3()" id="abstractButton3" class="custom-button small">Abstract</button>
    </div>
</div>
<div id="abstract3" style="display: none;">
    <text>
    Hedge funds make abnormally large and profitable trades in stocks before corporate bond announcements when their prime broker serves as a bond underwriter, and these trades outperform other trades. The outperformance is not concentrated in announcement periods, nor in funds serviced by prime brokers whose equity analysts follow the firm, and nor in new positions. Bond-market activity by hedge funds represents one possible channel of information transfer. Bonds of firms held by connected hedge funds are associated with higher secondary market volume and number of transactions during their first six months of trading. Evidence suggesting that hedge funds support underwriters in liquidity provision activities during the first months of bonds’ life when lengthy searches for high-valuation investors in the secondary market might be very costly.
    </text>
</div>







<titlecolor id="research  margin-top: 50px;">Work in Progress</titlecolor>


<div style="text-align: justify; margin-bottom: 20px; margin-top: 20px;">
    <div>
       <subtitlecolor style="display: inline; margin-bottom: 0; ">Momentum Spillovers in Corporate Bonds,</subtitlecolor><br>
        <text style="font-size: $type-size-6;">Joint with Katsiaryna Falkovich and Nils Friewald</text> 
    </div>
</div>



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


    function toggleAbstract3() {
        var abstractDiv = document.getElementById("abstract3");
        var button = document.getElementById("abstractButton3");
        if (abstractDiv.style.display === "none") {
            abstractDiv.style.display = "block";
            button.innerText = "Hide Abstract";
        } else {
            abstractDiv.style.display = "none";
            button.innerText = "Abstract";
        }
    }
</script>


