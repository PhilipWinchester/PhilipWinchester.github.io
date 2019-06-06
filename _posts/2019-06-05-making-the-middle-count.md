---
title: "Making the middle count"
date: 2019-06-05
tags: [FPL]
excerpt: "But what if Salah and Aguero don't score? This post makes sure you're prepared for when they don't."
mathjax: true
header:
  overlay_image: "/images/Making the middle count/Richarlison.jpg"
  overlay_filter: 0.4
classes: wide
---

**You are hopefully confident your squad will score highly in each gameweek and given your transfer limitations, perhaps also in a few gameweeks to come. How can you be confident in this? What are the factors you consider/should consider before selecting your squad? These are the ideas we need to quantify before we can move on to any kind of machine learning. In this post I will talk about the general squad structure I aim for and introduce a program I use to optimise this.**

After playing FPL for a number of years, I have developed a squad structure which I am happy with and rarely diverge from. Usually I have:

1. Two starting goalkeepers outside the "Big Six". In recent years I have found "Big Six" goalkeepers overpriced and having two mid-table goalkeepers to rotate with respect to favourable fixtures is usually the better option and allows you to spend money elsewhere.
2. Three premium defenders. These players are usually accompanied by a price tag of £5.5m+, but have proven well worth the investment in recent years. In todays football, we see fullbacks bombing forwards and the likes of Alonso, Robertson, Trippier and Mendy have all been excellent selections this year.
3. Two budget defenders. With the three premium options, there is no need to throw any more cash on the backline. Wan-Bissaka and Bennett always play for their respective teams and with their modest price tag of around £4.0m, they have been trustworthy substitutes for me this season.
4. Three premium options. The squad is not complete without your Hazards, Agueros, Salas, and Kanes. This is where you expect to get the big points.
5. The rest. There are five picks left spanning the attack and midfield. Usually, I select these in the £5.5m-£7.5m range depending on how much I have spent on the above. I will refer to these as the middle men and today we are going to make them count.

Below is a picture of my squad going into gameweek 16. Hopefully the structure mentioned above is clear.

![image-center](/images/Making the middle count/My squad.jpg){: .align-center .width-half}

Unless they have particularly favourable fixtures, Wan-Bissaka and Bennett tend to sit on the bench. The key decisions to make is who out of the middle men joins them. For gameweek 16, it looks like it might be Mooy, as Huddersfield face Arsenal away.

For any FPL analysis, thinking about upcoming fixtures is crucial and the table below outlines how difficult I believe each team is to face home and away (higher score = harder).

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Difficulty</th>
      <th>MCI</th>
      <th>LIV</th>
      <th>CHE</th>
      <th>TOT</th>
      <th>ARS</th>
      <th>MNU</th>
      <th>WOL</th>
      <th>EVE</th>
      <th>WHU</th>
      <th>BOU</th>
      <th>LEI</th>
      <th>WAT</th>
      <th>BHA</th>
      <th>BUR</th>
      <th>CRY</th>
      <th>NEW</th>
      <th>FUL</th>
      <th>SOU</th>
      <th>HUD</th>
      <th>CAR</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Home</th>
      <th>80.0</th>
      <th>73.0</th>
      <th>71.0</th>
      <th>70.0</th>
      <th>63.0</th>
      <th>62.0</th>
      <th>55.0</th>
      <th>51.0</th>
      <th>50.0</th>
      <th>46.0</th>
      <th>46.0</th>
      <th>44.0</th>
      <th>42.0</th>
      <th>42.0</th>
      <th>42.0</th>
      <th>40.0</th>
      <th>39.0</th>
      <th>38.0</th>
      <th>34.0</th>
      <th>33.0</th>
    </tr>
    <tr>
    <th>Away</th>
    <th>104.0</th>
    <th>94.9</th>
    <th>92.3</th>
    <th>91.0</th>
    <th>81.9</th>
    <th>80.6</th>
    <th>71.5</th>
    <th>66.3</th>
    <th>65.0</th>
    <th>59.8</th>
    <th>59.8</th>
    <th>57.2</th>
    <th>54.6</th>
    <th>54.6</th>
    <th>54.6</th>
    <th>52.0</th>
    <th>50.7</th>
    <th>49.4</th>
    <th>44.2</th>
    <th>42.9</th>
    </tr>
  </tbody>
</table>
</div>

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;border-color:#aabcfe;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aabcfe;color:#669;background-color:#e8edff;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aabcfe;color:#039;background-color:#b9c9fe;}
.tg .tg-hmp3{background-color:#D2E4FC;text-align:left;vertical-align:top}
.tg .tg-baqh{text-align:center;vertical-align:top}
.tg .tg-mb3i{background-color:#D2E4FC;text-align:right;vertical-align:top}
.tg .tg-lqy6{text-align:right;vertical-align:top}
.tg .tg-0lax{text-align:left;vertical-align:top}
.tg-sort-header::-moz-selection{background:0 0}.tg-sort-header::selection{background:0 0}.tg-sort-header{cursor:pointer}.tg-sort-header:after{content:'';float:right;margin-top:7px;border-width:0 5px 5px;border-style:solid;border-color:#404040 transparent;visibility:hidden}.tg-sort-header:hover:after{visibility:visible}.tg-sort-asc:after,.tg-sort-asc:hover:after,.tg-sort-desc:after{visibility:visible;opacity:.4}.tg-sort-desc:after{border-bottom:none;border-width:5px 5px 0}</style>
<table id="tg-lP7tU" class="tg">
  <tr>
    <th class="tg-baqh" colspan="6">Results</th>
  </tr>
  <tr>
    <td class="tg-hmp3">No</td>
    <td class="tg-hmp3">Competition</td>
    <td class="tg-hmp3">John</td>
    <td class="tg-hmp3">Adam</td>
    <td class="tg-hmp3">Robert</td>
    <td class="tg-hmp3">Paul</td>
  </tr>
  <tr>
    <td class="tg-0lax">1</td>
    <td class="tg-0lax">Swimming</td>
    <td class="tg-lqy6">1:30</td>
    <td class="tg-lqy6">2:05</td>
    <td class="tg-lqy6">1:15</td>
    <td class="tg-lqy6">1:41</td>
  </tr>
  <tr>
    <td class="tg-hmp3">2</td>
    <td class="tg-hmp3">Running</td>
    <td class="tg-mb3i">15:30</td>
    <td class="tg-mb3i">14:10</td>
    <td class="tg-mb3i">15:45</td>
    <td class="tg-mb3i">16:00</td>
  </tr>
  <tr>
    <td class="tg-0lax">3</td>
    <td class="tg-0lax">Shooting</td>
    <td class="tg-lqy6">70%</td>
    <td class="tg-lqy6">55%</td>
    <td class="tg-lqy6">90%</td>
    <td class="tg-lqy6">88%</td>
  </tr>
</table>
<script charset="utf-8">var TGSort=window.TGSort||function(n){"use strict";function r(n){return n.length}function t(n,t){if(n)for(var e=0,a=r(n);a>e;++e)t(n[e],e)}function e(n){return n.split("").reverse().join("")}function a(n){var e=n[0];return t(n,function(n){for(;!n.startsWith(e);)e=e.substring(0,r(e)-1)}),r(e)}function o(n,r){return-1!=n.map(r).indexOf(!0)}function u(n,r){return function(t){var e="";return t.replace(n,function(n,t,a){return e=t.replace(r,"")+"."+(a||"").substring(1)}),l(e)}}function i(n){var t=l(n);return!isNaN(t)&&r(""+t)+1>=r(n)?t:NaN}function s(n){var e=[];return t([i,m,g],function(t){var a;r(e)||o(a=n.map(t),isNaN)||(e=a)}),e}function c(n){var t=s(n);if(!r(t)){var o=a(n),u=a(n.map(e)),i=n.map(function(n){return n.substring(o,r(n)-u)});t=s(i)}return t}function f(n){var r=n.map(Date.parse);return o(r,isNaN)?[]:r}function v(n,r){r(n),t(n.childNodes,function(n){v(n,r)})}function d(n){var r,t=[],e=[];return v(n,function(n){var a=n.nodeName;"TR"==a?(r=[],t.push(r),e.push(n)):("TD"==a||"TH"==a)&&r.push(n)}),[t,e]}function p(n){if("TABLE"==n.nodeName){for(var e=d(n),a=e[0],o=e[1],u=r(a),i=u>1&&r(a[0])<r(a[1])?1:0,s=i+1,v=a[i],p=r(v),l=[],m=[],g=[],h=s;u>h;++h){for(var N=0;p>N;++N){r(m)<p&&m.push([]);var T=a[h][N],C=T.textContent||T.innerText||"";m[N].push(C.trim())}g.push(h-s)}var L="tg-sort-asc",E="tg-sort-desc",b=function(){for(var n=0;p>n;++n){var r=v[n].classList;r.remove(L),r.remove(E),l[n]=0}};t(v,function(n,t){l[t]=0;var e=n.classList;e.add("tg-sort-header"),n.addEventListener("click",function(){function n(n,r){var t=d[n],e=d[r];return t>e?a:e>t?-a:a*(n-r)}var a=l[t];b(),a=1==a?-1:+!a,a&&e.add(a>0?L:E),l[t]=a;var i=m[t],v=function(n,r){return a*i[n].localeCompare(i[r])||a*(n-r)},d=c(i);(r(d)||r(d=f(i)))&&(v=n);var p=g.slice();p.sort(v);for(var h=null,N=s;u>N;++N)h=o[N].parentNode,h.removeChild(o[N]);for(var N=s;u>N;++N)h.appendChild(o[s+p[N-s]])})})}}var l=parseFloat,m=u(/^(?:\s*)([+-]?(?:\d+)(?:,\d{3})*)(\.\d*)?$/g,/,/g),g=u(/^(?:\s*)([+-]?(?:\d+)(?:\.\d{3})*)(,\d*)?$/g,/\./g);n.addEventListener("DOMContentLoaded",function(){for(var t=n.getElementsByClassName("tg"),e=0;e<r(t);++e)try{p(t[e])}catch(a){}})}(document);</script>
