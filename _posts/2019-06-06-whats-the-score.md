---
title: "What's the score?"
date: 2019-06-06
tags: [FPL, R]
excerpt: "Combining the world’s most popular sport with everyone’s favourite discrete probability distribution, this post predicts football matches using the Poisson distribution."
mathjax: true
header:
  overlay_image: "/images/whats the score/Header.jpg"
  overlay_filter: 0.4
classes: wide
---
**Let me throw some numbers at you. In 2013 (a long time ago) Sport betting was worth up to £625 billion a year, with 70% of that trade reckoned to come from football. During the 2018 football World Cup, bookmakers were estimated to make a profit north of £28 billion. There is a lot of money in the industry which has paved the way for London based companies Starlizard and Mustard Systems who utilise the fact that odds often reflect the market rather then the true probabilities. This leaves enough wiggle room for both the bookies and betting experts to make a profit to the expense of 28 year old Dave who highly overestimates his teams ability and aimlessly places a £50 bet after 6 pints in the pub. The nature of this kind of market is particularly clear with Bet365 which acts like an exchange. They happily take a small cut out of every transaction whilst the users fight for the rest. The key is to accurately predict the actual probabilities in each fixture, in the light of this we find the games were sufficiently many Daves have placed a bet for us to make a profit. That sounds easy enough, but how do we do it?**
