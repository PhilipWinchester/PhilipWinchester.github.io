---
title: "Lets Get Started!"
date: 2019-06-04
tags: [FPL]
excerpt: ""
mathjax: true
header:
  overlay_image: "/images/Lets get started/Logo on right.png"
  overlay_filter: 0.4
classes: wide
---

**Once August comes around and I've picked a witty FPL name, there is only one thing on my mind, to top the office, friends and family FPL table. This is were fame and glory is to be found and if you're anything like me (if you've found this blog, chances are you are), you're willing to go to extreme lengths to earn the FPL bragging rights. I love sports and I love mathematics. Although not obvious at first, FPL is the ideal setting where they both merge. In previous years, I have set up countless excel spreadsheets analysing fixture difficulty and player value. This year, I am hoping to take it one step further.**

Not many days pass without Elon Musk  shouting about AI. There's talk about self-driving cars, drones and DRU (Domino’s Robotic Unit). This all sounds very exciting, some times a little scary. All I want is a program which I can run in August, have it make all my FPL transfers and find myself at the top of the league in May. To make something like this is no easy task. I have often wondered if it's possible to play the perfect FPL game, but I suspect it's much like chess. The seemingly simple game becomes immensely complicated once you consider all the options you have. In fact, throughout the season, simple combinatorics tells us that $$1.1 \times 10^{34}$$ $$^1$$ is an underestimate of the number of possible FPL games. Although many of these are silly nonsense games, picking the best one is not easy and most probably (definitely) impossible due to its stochastic nature.

We are going to try nonetheless and in this blog I will write about ideas and algorithms slowly building towards the final product. These will mostly be written in R and maybe some in Python. I am not expecting to have many (if any) readers. At the very least, this blog will simply act as an audit trail for myself. But if you are reading this, please know that all constructive feedback and criticism on my work is more than welcome as we go.

<hr align="center" width="75%">

$$^1$$ In the selection of your 15 man FPL squad, you can comfortably select all players costing less than £6.6m given your initial £100m budget. There are approximately 450 outfield players and 50 goalkeepers costing less than £6.6m to choose from.
Hence, we have:

- Ways to pick your outfield players = $${450 \choose 13}$$
- Ways to pick your goalkeepers = $${50 \choose 2}$$
- Ways to structure your team = $$2({8 \choose 3} - 3) + 4{8 \choose 2} + 8{5 \choose 3}$$.  First, second and third term inside the main bracket relate to formations with five, four and three defender respectively.
- Ways to pick your captain and vice-captain, allowing for rotation = $$2{12 \choose 2}$$
- 35 gameweeks (allowing for 3 double gameweeks)

Combining the above gives us:

$${450 \choose 13} \times {50 \choose 2} \times \Big(2\Big({8 \choose 3} - 3\Big) + 4{8 \choose 2} + 8{5 \choose 3}\Big) \times 2{12 \choose 2} \times 35 \approx 1.1 \times 10^{34}$$
