---
layout: archive
title: ""
permalink: /research/
author_profile: true
classes: wide
---
<div style="text-align: center; margin-bottom: 100px;">
    <h1>Working Papers</h1>
</div>

<div style="text-align: justify; margin-bottom: 50px;">
    <h3>Inflation Risk and Yield Spread Changes, (SSRN)</h3>
</div>
<div style="overflow: auto;">
    <img src="/images/model_plot_all.png" alt="Image for Inflation Risk and Yield Spread Changes" style="float: right;   max-width: 350px; max-height: 250px;">
    <div>
        <text>
        Inflation risk explains more than 40% of the systematic variation of yield spread changes beyond standard structural factors. I show that changes in expected inflation, volatility, and cyclicality are significant determinants of yield spread changes. A structural model with a stochastic price index and sticky cash flow accounts for these patterns and delivers further implications. In the cross-section, the model predicts increasing loading patterns on leverage and cash-flow flexibility. In the time series, the model predicts diminished effects during periods of high expected inflation. I find empirical support for the model’s predictions.
        </text>
    </div>
</div>


<div style="text-align: justify; margin-bottom: 50px;">
    <h3>Good Inflation, Bad Inflation: Implications for Risky Asset Prices, with Berardino Palazzo and Ram Yamarthy (SSRN)</h3>
</div>
<div style="overflow: auto;">
<img src="/images/bondstockcorr_xcpicov.png" alt="Image for Good Inflation, Bad Inflation: Implications for Risky Asset Prices" style="float: right; max-width: 350px; max-height: 250px;">
    <div>
      <text>
      In times of market-perceived “good inflation,” when inflation news is positively correlated with real economic growth, shocks to expected inflation substantially reduce corporate credit spreads and raise equity valuations. Meanwhile in times of “bad inflation,” these effects are attenuated and the opposite can take place. These dynamics naturally arise from an equilibrium asset pricing model with a time-varying inflation-growth relationship and persistent macroeconomic expectations. Using inflation swap prices we study how expected inflation is priced in firm-level credit spreads and equity returns, and uncover evidence of a time-varying inflation beta.
      </text>
    </div>
</div>



<div style="text-align: justify; margin-bottom: 50px;">
    <h3>Hedge Funds, Prime Brokers, and Corporate Bond Offerings</h3>
</div>
<div style="overflow: auto;">
<img src="/images/Plot_HF_PB_2019.jpg" alt="Image for Hedge Funds, Prime Brokers, and Corporate Bond Offerings" style="float: right; max-width: 350px; max-height: 250px;">
    <div>
<text>
Hedge funds make abnormally large and profitable trades in stocks before corporate bond announcements when their prime broker serves as a bond underwriter, and these trades outperform other trades. The outperformance is not concentrated in announcement periods, nor in funds serviced by prime brokers whose equity analysts follow the firm, and nor in new positions. Bond-market activity by hedge funds represents one possible channel of information transfer. Bonds of firms held by connected hedge funds are associated with higher secondary market volume and number of transactions during their first six months of trading. Evidence suggesting that hedge funds support underwriters in liquidity provision activities during the first months of bonds’ life when lengthy searches for high-valuation investors in the secondary market might be very costly. 
</text>
    </div>
</div>

<div style="text-align: center;  margin-bottom: 100px;">
    <h1>Work in Progress</h1>
</div>

<div style="text-align: justify; margin-bottom: 50px;">
    <h5>Momentum Spillovers in Corporate Bonds, with Katsiaryna Falkovich and Nils Friewald</h5>
</div>
<div style="overflow: auto;">
<img src="/images/network_graph.png" alt="Image for Momentum Spillovers in Corporate Bonds" style="float: right; max-width: 350px; max-height: 250px;">
    <div>
<text>
Connected firms in the stock market respond to common information with a lag, leading to momentum spillovers. While this effect had been significantly reduced in the stock market, we still find strong cross-asset momentum spillovers among corporate bonds. A strategy that buys bonds whose peers had high stock returns last month and sells bonds with underperforming peers generates an excess return of 38 basis points. Consistent with delayed response due to trading frictions, we find that momentum spillovers are larger for bonds characterized by higher search frictions, particularly those intermediated by dealers at the periphery of the dealer network.
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
