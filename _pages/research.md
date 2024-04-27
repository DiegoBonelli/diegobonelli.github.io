---
layout: archive
title: ""
permalink: /research/
author_profile: true
classes: wide
---
<div style="text-align: center;">
    <h1>Working Papers</h1>
</div>

<div style="overflow: auto;">
    <img src="/images/model_plot_all.png" alt="Image for Inflation Risk and Yield Spread Changes" style="float: right; margin-right: 15px; width: 300px; height: 225px;">
<div style="text-align: left;">
    <h2>Inflation Risk and Yield Spread Changes, (SSRN)</h2>
</div>
    <div>
        <text>
        Inflation risk explains more than 40% of the systematic variation of yield spread changes beyond standard structural factors. I show that changes in expected inflation, volatility, and cyclicality are significant determinants of yield spread changes. A structural model with a stochastic price index and sticky cash flow accounts for these patterns and delivers further implications. In the cross-section, the model predicts increasing loading patterns on leverage and cash-flow flexibility. In the time series, the model predicts diminished effects during periods of high expected inflation. I find empirical support for the model’s predictions.
        </text>

    </div>
</div>



<div style="overflow: auto;">
<img src="/images/bondstockcorr_xcpicov.png" alt="Image for Good Inflation, Bad Inflation: Implications for Risky Asset Prices" style="float: left; margin-right: 15px; width: 300px; height: 225px;">
<div style="text-align: left;">
    <h2>Good Inflation, Bad Inflation: Implications for Risky Asset Prices, with Berardino Palazzo and Ram Yamarthy (SSRN)</h2>
</div>
    <div>
        <text>

In times of market-perceived “good inflation,” when inflation news is positively correlated with real economic growth, shocks to expected inflation substantially reduce corporate credit spreads and raise equity valuations. Meanwhile in times of “bad inflation,” these effects are attenuated and the opposite can take place. These dynamics naturally arise from an equilibrium asset pricing model with a time-varying inflation-growth relationship and persistent macroeconomic expectations. Using inflation swap prices we study how expected inflation is priced in firm-level credit spreads and equity returns, and uncover evidence of a time-varying inflation beta.
        </text>

    </div>
</div>






{% if site.author.googlescholar %}
  <div class="wordwrap">You can also find my articles on <a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.research reversed %}
  {% include archive-single.html %}
{% endfor %}
