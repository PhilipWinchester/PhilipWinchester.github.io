---
title: "What's the score?"
date: 2019-06-06
tags: [FPL, R]
excerpt: "Combining the world’s most popular sport with everyone’s favourite discrete probability distribution, this post predicts football matches using the Poisson distribution."
mathjax: true
header:
  overlay_image: "/images/Whats the score/Header.jpg"
  overlay_filter: 0.4
classes: wide
---
**Let me throw some numbers at you. In 2013 (a long time ago) Sport betting was worth up to [£625 billion](https://www.bbc.co.uk/sport/football/24354124) a year, with 70% of that trade reckoned to come from football. During the 2018 football World Cup, bookmakers were estimated to make a profit north of [£28 billion](https://alphasportsbetting.com/sports-betting-tactics/how-much-money-is-bet-on-the-fifa-world-cup). There is a lot of money in the industry which has paved the way for London based companies [Starlizard](https://www.starlizard.com/) and [Mustard Systems](https://www.mustardsystems.com/) who utilise the fact that odds often reflect the market rather then the true probabilities. This leaves enough wiggle room for both the bookies and betting experts to make a profit to the expense of 28 year old Dave who highly overestimates his teams ability and aimlessly places a £50 bet after 6 pints in the pub. The nature of this kind of market is particularly clear with Bet365 which acts like an exchange. They happily take a small cut out of every transaction whilst the users fight for the rest. The key is to accurately predict the actual probabilities in each fixture, in the light of this we find the games were sufficiently many Daves have placed a bet for us to make a profit. That sounds easy enough, but how do we do it?**

## The Poission Distribution
Firstly I should clarify the connection to Fantasy Premier League. In my [last post](https://philipwinchester.github.io/making-the-middle-count/) we arbitrarily placed a difficulty value on each fixture to discuss which "middle men" were the most attractive to own ahead of the next few gameweeks. The aim in this post will be to put some reasoning behind that difficulty value. In summary, if we can produce a model which tells us that Brighton have a 95% probability of loosing against Man City at the Etihad, this gives us incentive to stock up on City players and lose Lewis Dunk.

The [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution) is a [discrete probability](https://en.wikipedia.org/wiki/Probability_distribution#Discrete_probability_distribution) distribution which takes positive integer values (0, 1, 2...) with some probability depending on some parameter $$\lambda$$. Here is the same thing written in the language of mathematics:

$$P(X = x) = \frac{e^{-\lambda} \lambda ^x }{x!} \quad x = 0,1,2... \quad  \lambda > 0$$

The distribution is often used to model events which:

1. Has x as the number of event which occur in some time interval.
2. Occur independently.  That is, the occurrence of one event does not affect the probability that a second event will occur.
3. The average rate it which event occur is constant. (It is actually equal to λ).

The above assumptions seem to fit quite nicely to the goals scored by any team in a game of football. Hence, it is a Poisson distribution we will use in this post. The key is to find λ, which is the average number of goals we expect a team to score in a particular game. If the Poisson distribution is new to you, an easy example how it may be applied to football modelling is [here](https://help.smarkets.com/hc/en-gb/articles/115001457989-How-to-calculate-Poisson-distribution-for-football-betting), a good taster of what is to come. To get a feel of what probabilities the distribution gives, I have plotted the probabilities for different λ and x ranging from 0 to 20.

![image-center](/images/Whats the score/poisson3.gif){: .align-center .width-half}

Granted a team is unlikely to be expected to score 9 goals in any game, but I hope the trend above is clear: The probability to score more goals increases as $$\lambda$$ increases. If we expect a team to score 1 goal in a game, it is pretty unlikely the team will score more than 3 goals. Whilst if we expect a team to score 3 goals, they may well score 5 or 6. The simplest way to find lambda is simply to take:

$$ \begin{align*}  
  P(\text{Chelsea score 0 goals}) &= \frac{1.73^{0}e^{-1.73}}{0!} = 17.7\% \\
  P(\text{Chelsea score 1 goal}) &= \frac{1.73^{1}e^{-1.73}}{1!} = 30.7\% \\
  P(\text{Chelsea score 2 goals}) &= \frac{1.73^{2}e^{-1.73}}{2!} = 26.5\% \\
  P(\text{Chelsea score 3 goals or more}) &= 1 - \text{Sum of above}=25.1\%
\end{align*}$$


**Work in progress**
