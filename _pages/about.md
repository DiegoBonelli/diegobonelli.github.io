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

<div style="text-align: justify; margin-bottom: 50px;">
    <div>
        <h3 style="display: inline;">Inflation Risk and Yield Spread Changes</h3>
        <button onclick="toggleAbstract()" id="abstractButton" class="custom-button">Abstract</button>
        <button onclick="window.location.href='https://papers.ssrn.com/abstract=4299512';" class="custom-button">SSRN</button>
    </div>
</div>
<div id="abstract" style="display: none; margin-top: 20px;">
    <text>
    Inflation risk explains more than 40% of the systematic variation of yield spread changes beyond standard structural factors. I show that changes in expected inflation, volatility, and cyclicality are significant determinants of yield spread changes. A structural model with a stochastic price index and sticky cash flow accounts for these patterns and delivers further implications. In the cross-section, the model predicts increasing loading patterns on leverage and cash-flow flexibility. In the time series, the model predicts diminished effects during periods of high expected inflation. I find empirical support for the model’s predictions.
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
        background-color: rgba(0, 0, 255, 0.5);
        color:white;
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
</script>
