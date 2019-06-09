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
3. The average rate it which event occur is constant. (It is actually equal to $$\lambda$$).

The above assumptions seem to fit quite nicely to the goals scored by any team in a game of football. Hence, it is a Poisson distribution we will use in this post. The key is to find λ, which is the average number of goals we expect a team to score in a particular game. If the Poisson distribution is new to you, an easy example how it may be applied to football modelling is [here](https://help.smarkets.com/hc/en-gb/articles/115001457989-How-to-calculate-Poisson-distribution-for-football-betting), a good taster of what is to come. To get a feel of what probabilities the distribution gives, I have plotted the probabilities for different $$\lambda$$ and x ranging from 0 to 20.

![image-center](/images/Whats the score/poisson3.gif){: .align-center .width-half}

Granted a team is unlikely to be expected to score 9 goals in any game, but I hope the trend above is clear: The probability to score more goals increases as $$\lambda$$ increases. If we expect a team to score 1 goal in a game, it is pretty unlikely the team will score more than 3 goals. Whilst if we expect a team to score 3 goals, they may well score 5 or 6. The simplest way to find lambda is simply to take:

$$ \frac{\text{Number of goals scored in season so far}}{\text{Number of games played}}$$

As I write this on the 10th of April, Chelsea have scored 57 goals in 33 games. Using the formula above, this equates to an average scoring rate of 1.73 goals per game. Taking this value for $$\lambda$$ gives the following probability values:

$$ \begin{align*}  
  P(\text{Chelsea score 0 goals}) &= \frac{1.73^{0}e^{-1.73}}{0!} = 17.7\% \\
  P(\text{Chelsea score 1 goal}) &= \frac{1.73^{1}e^{-1.73}}{1!} = 30.7\% \\
  P(\text{Chelsea score 2 goals}) &= \frac{1.73^{2}e^{-1.73}}{2!} = 26.5\% \\
  P(\text{Chelsea score 3 goals or more}) &= 1 - \text{Sum of above}=25.1\%
\end{align*}$$

However, given that Chelsea face Liverpool away at Anfield in their next fixture, it is probably not reasonable to assume that there is only a 17.7% chance that they will leave the game goalless. A 25.1% change that they will score 3 or more seems a little optimistic. Clearly there are some parameters we are missing. Here are three of them:

1. In football there is something called **Home field advantage**, discussed [here](https://jogall.github.io/2017-05-12-home-away-pref/). Playing on you home pitch is generally seen as an advantage which is also what data from this year Premier League season suggests. To date, the average goal scoring rate is 1.58 for home teams and 1.25 for away teams. This is a significant difference and should be taking into account in our model.
2. **The opposition** is also important. With out example for Chelsea above, not only are they playing away from home, the fact that they are playing Liverpool makes the numbers derived from our simplistic model look a little off. The goal scoring rate clearly depends on if you're playing Brighton or Liverpool.
3. It is probably reasonable to postulate that a teams **current form** has some effect on their scoring rate in upcoming fixtures. This will be taken into account by only considering games played in the last 3 months.

## Building the Model
We want to predict future results, so a good place to start is to look at previous results. There are a number of sources to extract this information, I get it from [football-data.co.uk](http://www.football-data.co.uk/englandm.php). Lets see what it looks like in R.

```r
library(dplyr)

# Picking the columns we are interested in and only the games played in the last 3 months
Last_Fixture <- as.Date(gsub("/", ".", DF_Original$Date[nrow(DF)]), "%d.%m.%Y")
DF_Recent <- DF_Original[c("HomeTeam", "AwayTeam", "FTHG", "FTAG", "Date")] %>%
  mutate(Date = as.Date(gsub("/", ".", Date), "%d.%m.%Y")) %>%
  filter(Date > (Last_Fixture - 90))

# Creating the dataframe which we will use to build our model
ModelDF_Recent <- data.frame(Team = c(DF_Recent$HomeTeam, DF_Recent$AwayTeam),
  Opposition =  c(DF_Recent$AwayTeam, DF_Recent$HomeTeam),
  HA = c(rep("H",nrow(DF_Recent)),rep("A",nrow(DF_Recent))),
  Goals= c(DF_Recent$FTHG, DF_Recent$FTAG))
```

"DF_Original" is the dataset taken from football-data.co.uk and it contains 62 columns, most of which we are not interested in. The first few lines of code above extract the columns we want, buts the Date column in the format we want and removes games which were played more than 90 days ago.

Our aim is to build a model which takes a team, an opposition and returns a value for $$\lambda$$ depending on if the team is playing at home or away. To train such a model, we need a dataframe with the following columns "Team", "Opposition", "HA" and "Goals scored by Team", where "HA" indicates if the Team is playing at home or away. Such a dataframe is built in the last few lines of code above in "ModelDF_Recent" and is displayed below:

<div>
<table>
  <caption>ModelDF_Recent</caption>
  <thead>
    <tr style="text-align: right;">
      <th>Row Num</th>
      <th>Team</th>
      <th>Opposition</th>
      <th>HA</th>
      <th>Goals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <th>Brighton</th>
      <th>Liverpool</th>
      <th>H</th>
      <th>0</th>
    </tr>
    <tr>
      <th>2</th>
      <th>Burnley</th>
      <th>Fulham</th>
      <th>H</th>
      <th>2</th>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <th>...</th>
      <th>...</th>
      <th>...</th>
    </tr>
    <tr>
      <th>116</th>
      <th>Liverpool</th>
      <th>Brighton</th>
      <th>A</th>
      <th>1</th>
    </tr>
    <tr>
      <th>117</th>
      <th>Fulham</th>
      <th>Burnley</th>
      <th>A</th>
      <th>1</th>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <th>...</th>
      <th>...</th>
      <th>...</th>
    </tr>
  </tbody>
</table>
</div>

The game between Brighton and Liverpool on the 12 Jan (86 days before the most recent fixture) appears as our first entry above. Note that the first entry relates to Brighton scoring 0 goals against Liverpool at home. The conjugate entry in line 116 says that Liverpool scored 1 goal agains Brighton away. Each fixture has two conjugate entries.

Next we use this dataframe to train the model.

```r
# Creating the Model for recent fixtures
attach(ModelDF_Recent)
Model_Recent <- glm(Goals ~ Team + HA + Opposition, family=poisson(link=log))
summary(Model_Recent) # Printing the summary
detach(ModelDF_Recent)
```
<div>
<table>
  <caption>Some output from summary(Model_Recent)</caption>
  <thead>
    <tr style="text-align: right;">
      <th>Coefficients</th>
      <th>Estimate</th>
      <th>Std. Error</th>
      <th>z value</th>
      <th>Pr(>|z|)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(Intercept)</th>
      <th>-0.132610</th>
      <th>0.423617</th>
      <th>-0.313</th>
      <th>0.754249</th>
    </tr>
    <tr>
      <th>TeamCrystal Palace</th>
      <th>-0.110139</th>
      <th>0.331803</th>
      <th>-0.332</th>
      <th>0.739934</th>
    </tr>
    <tr>
      <th>TeamMan City</th>
      <th>0.264142</th>
      <th>0.306663</th>
      <th>0.861</th>
      <th>0.389048</th>
    </tr>
    <tr>
      <th>OppositionCrystal Palace</th>
      <th>0.472469</th>
      <th>0.422454</th>
      <th>1.118</th>
      <th>0.263400</th>
    </tr>
    <tr>
      <th>OppositionMan City</th>
      <th>-0.743706</th>
      <th>0.606242</th>
      <th>-1.227</th>
      <th>0.219918</th>
    </tr>
  </tbody>
</table>
</div>

The [glm](https://en.wikipedia.org/wiki/General_linear_model) (general linear model) takes the observations in out datarame to estimate the parameters ($$\alpha$$, $$\beta$$'s,  $$\gamma$$'s and  $$\kappa$$) in the following expression:

$$ \begin{align*}
  \lambda &= e^{\alpha+\text{Team}+\text{Opposition}+\text{HA}} \\
  \text{Team} &= \begin{cases}
    \beta^1& \quad \text{if Team}=\text{Bournemouth} \\
    \beta^2& \quad \text{if Team}=\text{Brighton} \\
    ... \\
    \beta^{20}& \quad \text{if Team}=\text{Wolves }
  \end{cases} \\
  \text{Opposition} &= \begin{cases}
    \gamma^1& \quad \text{if Opposition}=\text{Bournemouth} \\
    \gamma^2& \quad \text{if Opposition}=\text{Brighton} \\
    ... \\
    \gamma^{20}& \quad \text{if Opposition}=\text{Wolves }
  \end{cases} \\
  \text{HA} &= \begin{cases}
    \kappa \quad \text{if HA}=\text{H} \\
    0 \quad \text{if HA}=\text{A}
  \end{cases}
\end{align*}$$




**Work in progress**

Need to explain what Rows_Original, Rows_Recent and Teams are. Maybe not Row_Recent as it has explcilty been put in above. could actually move positono for teams. After model stuff add the bit about form
