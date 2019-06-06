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

<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
img {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
</style>
</head>
---

**You are hopefully confident your squad will score highly in each gameweek and given your transfer limitations, perhaps also in a few gameweeks to come. How can you be confident in this? What are the factors you consider/should consider before selecting your squad? These are the ideas we need to quantify before we can move on to any kind of machine learning. In this post I will talk about the general squad structure I aim for and introduce a program I use to optimise this.**

After playing FPL for a number of years, I have developed a squad structure which I am happy with and rarely diverge from. Usually I have:

1. Two starting goalkeepers outside the "Big Six". In recent years I have found "Big Six" goalkeepers overpriced and having two mid-table goalkeepers to rotate with respect to favourable fixtures is usually the better option and allows you to spend money elsewhere.
2. Three premium defenders. These players are usually accompanied by a price tag of £5.5m+, but have proven well worth the investment in recent years. In todays football, we see fullbacks bombing forwards and the likes of Alonso, Robertson, Trippier and Mendy have all been excellent selections this year.
3. Two budget defenders. With the three premium options, there is no need to throw any more cash on the backline. Wan-Bissaka and Bennett always play for their respective teams and with their modest price tag of around £4.0m, they have been trustworthy substitutes for me this season.
4. Three premium options. The squad is not complete without your Hazards, Agueros, Salas, and Kanes. This is where you expect to get the big points.
5. The rest. There are five picks left spanning the attack and midfield. Usually, I select these in the £5.5m-£7.5m range depending on how much I have spent on the above. I will refer to these as the middle men and today we are going to make them count.

Below is a picture of my squad going into gameweek 16. Hopefully the structure mentioned above is clear.

<img src="/images/Making the middle count/My squad.jpg" alt="" width="600" height="450" style="width:50%;">

Unless they have particularly favourable fixtures, Wan-Bissaka and Bennett tend to sit on the bench. The key decisions to make is who out of the middle men joins them. For gameweek 16, it looks like it might be Mooy, as Huddersfield face Arsenal away.

For any FPL analysis, thinking about upcoming fixtures is crucial and the table below outlines how difficult I believe each team is to face home and away (higher score = harder).

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Team</th>
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
      <th>H Difficulty</th>
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
    <th>A Difficulty</th>
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
