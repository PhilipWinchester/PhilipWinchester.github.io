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
      <th></th>
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

[Call:
glm(formula = Goals ~ Team + HA + Opposition, family = poisson(link = log))

Deviance Residuals:
    Min       1Q   Median       3Q      Max  
-2.0608  -0.9136  -0.1114   0.5018   2.5263  

Coefficients:
                          Estimate Std. Error z value Pr(>|z|)    
(Intercept)              -0.132610   0.423617  -0.313 0.754249    
TeamBournemouth          -0.410482   0.367262  -1.118 0.263702    
TeamBrighton             -0.854457   0.431366  -1.981 0.047612 *  
TeamBurnley              -0.112327   0.338071  -0.332 0.739693    
TeamCardiff              -0.751166   0.409125  -1.836 0.066353 .  
TeamChelsea              -0.106657   0.332163  -0.321 0.748137    
TeamCrystal Palace       -0.110139   0.331803  -0.332 0.739934    
TeamEverton              -0.260379   0.350986  -0.742 0.458179    
TeamFulham               -0.522561   0.389683  -1.341 0.179923    
TeamHuddersfield         -1.178886   0.474592  -2.484 0.012992 *  
TeamLeicester            -0.047993   0.328877  -0.146 0.883977    
TeamLiverpool             0.187713   0.315719   0.595 0.552139    
TeamMan City              0.264142   0.306663   0.861 0.389048    
TeamMan United           -0.027127   0.348216  -0.078 0.937906    
TeamNewcastle            -0.241388   0.345886  -0.698 0.485251    
TeamSouthampton          -0.240801   0.359593  -0.670 0.503082    
TeamTottenham            -0.294002   0.365137  -0.805 0.420714    
TeamWatford              -0.034965   0.350195  -0.100 0.920468    
TeamWest Ham             -0.545795   0.375685  -1.453 0.146279    
TeamWolves               -0.111324   0.338343  -0.329 0.742136    
HAH                       0.430247   0.115717   3.718 0.000201 ***
OppositionBournemouth     0.643716   0.405659   1.587 0.112549    
OppositionBrighton        0.586949   0.424993   1.381 0.167255    
OppositionBurnley         0.490371   0.418789   1.171 0.241629    
OppositionCardiff         0.684450   0.407185   1.681 0.092776 .  
OppositionChelsea         0.614190   0.413591   1.485 0.137539    
OppositionCrystal Palace  0.472469   0.422454   1.118 0.263400    
OppositionEverton         0.049252   0.455130   0.108 0.913825    
OppositionFulham          0.908161   0.390599   2.325 0.020070 *  
OppositionHuddersfield    0.920738   0.393328   2.341 0.019238 *  
OppositionLeicester       0.688431   0.410402   1.677 0.093453 .  
OppositionLiverpool      -0.004021   0.466587  -0.009 0.993124    
OppositionMan City       -0.743706   0.606242  -1.227 0.219918    
OppositionMan United      0.038017   0.461929   0.082 0.934407    
OppositionNewcastle       0.287249   0.432340   0.664 0.506431    
OppositionSouthampton     0.423737   0.432959   0.979 0.327728    
OppositionTottenham       0.187541   0.444575   0.422 0.673140    
OppositionWatford         0.382051   0.424754   0.899 0.368406    
OppositionWest Ham        0.493553   0.414817   1.190 0.234122    
OppositionWolves          0.355747   0.430653   0.826 0.408767    
---


(Dispersion parameter for poisson family taken to be 1)

    Null deviance: 288.01  on 229  degrees of freedom
Residual deviance: 204.57  on 190  degrees of freedom
AIC: 698.94

Number of Fisher Scoring iterations: 5]{: .notice--primary}



**Work in progress**

Need to explain what Rows_Original, Rows_Recent and Teams are. Maybe not Row_Recent as it has explcilty been put in above
