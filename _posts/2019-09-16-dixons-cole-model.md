---
title: "Dixon Coles Model"
date: 2019-09-16
tags: [FPL, R]
excerpt: "The Poisson distribution is great and all, but we can do better! In this post we discuss two popular improvements to the standard Poisson model for football predictions"
mathjax: true
header:
  overlay_image: "/images/Whats the score/Header.jpg"
  overlay_filter: 0.4
  cta_label: "Poisson stuff"
  cta_url: "https://philipwinchester.github.io/whats-the-score/"
classes: wide
---

**In 1995 Mark Dixon and Stuart Coles were fed up with football results being predicted by Poisson models. Because lets be honest, they probably can't. So [here it is](http://web.math.ku.dk/~rolf/teaching/thesis/DixonColes.pdf), the new and improved Dixon Coles model suitable named after the two authors. If not only for amusing quotes such as "A recently introduced type of betting, fixed odds betting, is also growing in popularity. Bookmakers offer odds on the various outcomes of a match" which certainly reveals how ancient the paper is, it is certainly worth a read. The paper suggest two improvements to the standard Poisson model which will be discussed here. As a refresher, please do head up to the header for a link to my post on Poisson models.**

## Low Scoring Games
The heart of soul of the Poisson model is that the number of goals scored by each team can be modeled by two independent, you guessed it, Poisson distributions. At the start of the paper, Dixon and Coles discuss the suitability of this independence assumption and argue that it is not suitable for low scoring games. There seems to be some evidence for this theory in practice. Below is heat map, curtesy of David Sheehan author of an excellent [blog](https://dashee87.github.io/), which displays the average difference between actual and Poisson model predicted scorelines for the 2005/06 season all the way up to the 2017/18 season. Green cells imply the model underestimated those scorelines, while red cells suggest overestimation - the colour strength indicates the level of disagreement.

![image-center](/images/Dixon Coles Model/actual_model_diff.png){: .align-center .width-half}

Indeed, there are issues around low scoring draws but there is little to say about 1-0 and 0-1. In the light of this, Dixon and Coles introduce the dependence factor, $$\tau$$, which takes the following form in the models probability distribution:

$$\begin{align*}  
  P(X_{i,j}=x,Y_{i,j}=y) &= \tau_{\lambda, \mu}(x,y)\frac{e^{-\lambda} \lambda ^x }{x!}\frac{e^{-\mu} \mu^y }{y!}  \\
  \lambda &= \alpha_{i}\beta_{j}\gamma \\
  \mu &= \alpha_{j}\beta_{i} \\
  \tau_{\lambda, \mu}(x,y) &= \begin{cases}
    1-\lambda\mu\rho& \quad \text{if }x=y=0 \\
    1+\lambda\rho& \quad \text{if }x=0,y=1 \\
    1+\mu\rho& \quad \text{if }x=1,y=0 \\
    1-\rho& \quad \text{if }x=y=1 \\
    1& \quad \text{otherwise.}\\
  \end{cases}
\end{align*}$$

Here $$X$$ and $$Y$$ are the home and away goals scored, $$\alpha_{i}$$ and $$\beta_{i}$$ is the home team attacking and defensive strength and equivalently for the away team $$j$$. $$\gamma$$ is the home team advantage factor and $$\rho$$ is the newly introduced dependance factor. Setting $$\rho = 0$$ corresponds to independence. In our work going forward, we will aim to estimate a non-zero value for $$\rho$$ which deals with the issues we have for low scoring games. In practice, $$\tau$$ multiplies our low scorlines with a non-unit value. The model remains Poisson for high scoring games. Given that $$\rho$$ is expected to be small, this is really a very minor correction.  

## Time Decay
Manchester City loosing 3-2 against Norwich yesterday is more interesting to us than City beating Stoke 7-0 two years ago with regards to estimating the current attack and defensive strength of City. This is not taken into account at the moment. In fancy words: The current model is static - the attack and defense parameters of each teams are regarded as constant throughout time. Lets fix that!

Lets start by ignoring the above statement. As it stands, we have $$2n + 2$$ parameters to estimate, where $$n$$ is the number of teams in our dataset, 20 for the Premier League. $$n$$ $$\alpha$$'s, $$n$$ $$\beta$$'s, $$\gamma$$ and $$\rho$$. When estimating parameters we often refer to the likelihood of our observations and try to maximize this through varying our parameters. In simple terms: A bunch of games have been played, what values do we give our parameters so that the outcome of the games played were actually very likely to happen in the first place? In even simpler terms: John scored 5 goals in every single game ha played in last season. We want to model his scoring rate with a Poisson distribution. Johns Poisson parameters is probably about 5, not 1. Likelihoods are a key concept in probability theory and in this post going forward. Read up about it [here](https://en.wikipedia.org/wiki/Likelihood_function).

In our case, the likelihood function takes the following form:

$$\begin{align*}  
  L(\alpha_i, \beta, i, \gamma, \rho, i=1,...,n) &= \prod_{k=1}^{N} P(X_{i(k),j(k)}=x_k,Y_{i(k),j(k)}=y_k) \\
  &= \prod_{k=1}^{N} \tau_{\lambda_k, \mu_k}(x_k,y_k)\frac{e^{-\lambda_k} {\lambda_{k}}^{x_k} }{x_k!}\frac{e^{-\mu_k} {\mu_{k}}^{y_k} }{y_k!} \\
  &\propto \prod_{k=1}^{N} \tau_{\lambda_k, \mu_k}(x_k,y_k)e^{-\lambda_k} {\lambda_{k}}^{x_k} e^{-\mu_k} {\mu_{k}}^{y_k}
\end{align*}$$

In the likelihood function, $$k$$ runs through all N matched in our dataset and multiply these together given our parameters where $$\lambda_k = \alpha_{i(k)}\beta_{j(k)} \gamma$$ and $$\mu_k = \alpha_{j(k)}\beta_{i(k)}$$ are match dependent for home team $$i$$ and away team $$j$$. In the final step above we have simply got rid of the factorial factors as these just combine to one very very big number which becomes irrelevant when we're trying to maximize the likelihood with respect to the parameters.

To maximize, we find our partial derivatives of $$L$$ with respect to the parameters (gradient if you will). This looks incredibly scary and a common trick in this case is to consider $$\log_e(L)$$ instead. This works since the $$\log$$ function is a monotonically increasing function (ie, it increases as its argument increases), hence the maximums of $$L$$ are the same as those for $$\log_e(L)$$. The gain here is that logs of products become sums, which are much easier to deal with. the log likelihood is:

$$\begin{align*}  
  LL(\alpha_i, \beta, i, \gamma, \rho, i=1,...,n) &= \sum_{k=1}^{N} \log(\tau_{\lambda_k, \mu_k}(x_k,y_k))-\lambda_k+x_k\log(\lambda_k)-\mu_k+y_k\log(\mu_k)
\end{align*}$$

Going back to the first paragraph, our goal was to impose some sort of time dependance where more recent games are given more weighting. A good idea which achieves this is to multiply each term in the log likelihood by a monotonically decreasing function of time (ie a function that decreases with time). Here, $$t$$ for game $$k$$ is the time in days since the game was played. $$\phi(t) = -t$$ is monotonically decreasing, but for things to look pretty, we want a function that is 1 at $$t=0$$ and strictly positive... lets throw $$\phi(t) = e^{-\epsilon t}$$ for some positive constant $$\epsilon$$ in there. The size of $$\epsilon$$ dictates how quickly the importance of older games deteriorates. This is illustrated below.

![image-center](/images/Dixon Coles Model/Epsilon.jpeg){: .align-center .width-half}

It is a little problematic finding an optimal value for $$\epsilon$$. Dixon and Coles choose $$\epsilon$$ to optimize outcome predictions (win, loss, draw) rather than actual scorelines, which simplifies things slightly. In their paper, they use half week units for t and arrive at a value of 0.0065 for $$\epsilon$$. For us, that translates to $$\frac{0.0065}{3.5} = 0.0019$$. In the interest of keeping this post within a 30 min read, we are going to take this value as gospel. Adding the time decaying weighting function, or lok likelihood becomes:

$$\begin{align*}  
  LL(\alpha_i, \beta, i, \gamma, \rho, i=1,...,n) &= \sum_{k=1}^{N} e^{-\epsilon t}[\log(\tau_{\lambda_k, \mu_k}(x_k,y_k))-\lambda_k+x_k\log(\lambda_k)-\mu_k+y_k\log(\mu_k)]
\end{align*}$$

Below is some code which encapsulates the above. `MatchLL` find the contribution to the total log likelihood from any one game. This is then used in `LL` which finds the total log likelihood of our dataset given some vector of parameters.

$$\begin{align*}
  \text{Parameters} &= \begin{bmatrix}
    \alpha_{1}, ..., \alpha_{n}, \beta_{1}, ..., \beta_{n}, \gamma, \rho
    \end{bmatrix}
\end{align*}$$

The parameters are in alphabetical order which means that for the Premier League $$\alpha_{1}$$ corresponds to the attacking strength of Arsenal. Match_Data is a dataframe will all Premier League Fixtures since tha start of the 17/18 season. As usual, this data is taken from [football-data.co.uk](http://www.football-data.co.uk/englandm.php).

```r
tau <- Vectorize(function(x, y, lambda, mu, rho){
  #Defining the tau function
  if (x == 0 & y == 0){return(1 - (lambda*mu*rho))
  } else if (x == 0 & y == 1){return(1 + (lambda*rho))
  } else if (x == 1 & y == 0){return(1 + (mu*rho))
  } else if (x == 1 & y == 1){return(1 - rho)
  } else {return(1)}
})

phi <- function(t, epsilon = 0.0019){
  # Define the weight function
  return(exp(-epsilon*t))
}

MatchLL <- function(x,y,ai, aj, bi, bj, gamma, rho, t){
  # A function which calcualtes the log likelihood of some game
  lambda <- ai*bj*gamma
  mu <- aj*bi
  return(phi(t)*sum(log(tau(x, y, lambda, mu, rho)) - lambda + x*log(lambda) - mu + y*log(mu)))
}


LL <- function(Match_Data, Parameters){
  # Function which calclulates the LL for all the games
  LL <- 0

  Teams <- sort(unique(Match_Data$HomeTeam))

  # Fixing gamma and rho, as these are constant for all games
  gamma <- Parameters[2*length(Teams)+1]
  rho <- Parameters[2*length(Teams)+2]

  for(k in 1:nrow(Match_Data)){
    # Finding index for the home and away team
    IndexHome <- match(Match_Data$HomeTeam[k],Teams)
    IndexAway <- match(Match_Data$AwayTeam[k],Teams)

    # Finding relevant Parameters and other variables
    ai <- Parameters[IndexHome]
    aj <- Parameters[IndexAway]
    bi <- Parameters[IndexHome + length(Teams)]
    bj <- Parameters[IndexAway + length(Teams)]
    t <- Match_Data$t[k]
    x <- Match_Data$FTHG[k]
    y <- Match_Data$FTAG[k]

    #Adding the LL from game k to the total
    LL <- LL + MatchLL(x,y,ai, aj, bi, bj, gamma, rho, t)
  }

  return(LL)
}
```

## Maximizing the Log Likelihood

Next we need to find the parameters which maximize the log likelihood. In an ideal world we would do this analytically by finding the gradient and setting this to zero, but due to the high dimensionality of the problem, we are restricted to numerical maximization. This will be done by gradient ascent, a cousin to the better known gradient descent. The two algorithms are really very similar as there is only a subtle sign change between the two. If you are unfamiliar with the concepts, read about gradient descent [here](https://en.wikipedia.org/wiki/Gradient_descent) and the difference between the two [here](https://stats.stackexchange.com/questions/258721/gradient-ascent-vs-gradient-descent-in-logistic-regression).

In their paper, Dixon and Coles imposed the following condition on the attack parameters:

$$\begin{align*}
  n^{-1}\sum^{n}_{i=1} \alpha_{i} = 1.
\end{align*}$$

Ie, the average attack parameter is 1. This condition is not strictly necessary, but ensures that we arrive at the same order of maximizing perimeters whenever we run the gradient ascent.

The first step in gradient ascent is to find the gradient of the log likelihood. To save you having to look at a sea of greek symbols, I have put these derivatives in the Appendix. Below is a rough sketch of the algorithm. The actual code can be find on my [GitHub page](https://github.com/PhilipWinchester).

<div class="well">
  <p>1: Optimise <- function(Match_Data){<br>
  2:&ensp;&ensp;Creating <i>Parameters</i>, <i>StartParameters</i> and <i>Gradient</i> vectors of length 2n+2<br>
  3:&ensp;&ensp;Setting all entries in <i>Parameters</i> to 1, apart from γ and ρ which are set to 1.3 and <br> &ensp;&ensp;&ensp;-0.05 respectively<br>
  4:&ensp;&ensp;while(|<i>Parameters</i> - <i>StartParameters</i>| > Some small number) <br>
  5:&ensp;&ensp;&ensp;&ensp;Setting <i>StartParameters</i> = <i>Parameters</i> <br>
  6:&ensp;&ensp;&ensp;&ensp;Calculating the gradient of the log likelihood using Match_Data and<br> &ensp;&ensp;&ensp;&ensp;&ensp;<i>Parameters</i> using the partial derivatives in the Appendix and setting<br> &ensp;&ensp;&ensp;&ensp;&ensp;equal to <i>Gradient</i> <br>
  7:&ensp;&ensp;&ensp;&ensp;Imposing the condition that the average attack parameter is 1. This is done by<br> &ensp;&ensp;&ensp;&ensp;&ensp;altering the gradient in the following way:<br>
  &ensp;&ensp;&ensp;&ensp;&ensp;<i>Gradient</i> = <i>Gradient</i> - <i>Condition</i>, where <i>Condition</i> is<br> &ensp;&ensp;&ensp;&ensp;&ensp;a 2n+2 vector. Entries 1 to n are equal to the average of the first n entries<br> &ensp;&ensp;&ensp;&ensp;&ensp;in <i>Gradient</i>. remaining entries are 0.<br>
  8:&ensp;&ensp;&ensp;&ensp;Setting <i>PresentPoint</i> = <i>Parameters</i> and<br>
  &ensp;&ensp;&ensp;&ensp;&ensp;<i>StepPoint</i> = <i>Parameters</i> + <i>Gradient</i><br>
  9:&ensp;&ensp;&ensp;&ensp;while(LL(Match_Data, <i>StepPoint</i>) > LL(Match_Data, <i>PresentPoint</i>)) <br>
  10:&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;<i>PresentPoint</i> = <i>StepPoint</i> <br>
  11:&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;<i>StepPoint</i> = <i>StepPoint</i> + <i>Gradient</i><br>
  12:&ensp;&ensp;&ensp;&ensp;<i>Parameters</i> = <i>PresentPoint</i><br>
  13:&ensp;&ensp;return(<i>Parameters</i>)<br>
  14: }</p> </div>

Before adding the _Gradient_ to _StepPoint_ in lines 8 and 11, it is usual to reduce the size of _Gradient_ so that the steps we take to locate the maximum aren't too big, ie, we don't want to miss it. This is done by multiply _Gradient_ by some constant smaller than 1, say $$\theta$$. $$\theta$$ is referred to as the step size and is allowed to change at every iteration. As we get closer to the maximum, usually we want $$\theta$$ to decrease.

It is somewhat arbitrary what we set $$\theta$$ as. For the purpose of what we're doing, setting it equal to say throughout the algorithm is fine. Although there are ways of setting a variable $$\theta$$ to aid the speed of the algorithm. On my [GitHub page](https://github.com/PhilipWinchester), you will see that I have defined the function `NMod` which is used to decrease the size of _Gradient_ as we approach the maximum.

As it stands, my algorithm takes about 10 seconds to optimize parameters to the closest 0.01. This time is of course dependent on the number of teams and and games in our dataset, but for comparison, there are functions in the [alabama](https://cran.r-project.org/web/packages/alabama/index.html) package such as auglag which I understand take just under 1 minute to run on a similarly sized dataset.

## Results
As at 19 September 2019
<div>
<table>
  <caption>Parameters</caption>
  <thead>
    <tr style="text-align: right;">
      <th>Team</th>
      <th>α</th>
      <th>β</th>
      <th>Team</th>
      <th>α</th>
      <th>β</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Arsenal</th>
      <th>1.44</th>
      <th>1.15</th>
      <th>Man City</th>
      <th>1.93</th>
      <th>0.60</th>
    </tr>
    <tr>
      <th>Aston Villa</th>
      <th>0.56</th>
      <th>0.96</th>
      <th>Man United</th>
      <th>1.26</th>
      <th>0.97</th>
    </tr>
    <tr>
      <th>Bournemouth</th>
      <th>1.11</th>
      <th>1.51</th>
      <th>Newcastle</th>
      <th>0.82</th>
      <th>1.00</th>  
    </tr>
    <tr>
      <th>Brighton</th>
      <th>0.68</th>
      <th>1.26</th>
      <th>Norwich</th>
      <th>1.69</th>
      <th>1.58</th>
    </tr>
    <tr>
      <th>Burnley</th>
      <th>0.87</th>
      <th>1.24</th>
      <th>Sheffield United</th>
      <th>0.69</th>
      <th>0.99</th>
    </tr>
    <tr>
      <th>Cardiff</th>
      <th>0.69</th>
      <th>1.45</th>
      <th>Southampton</th>
      <th>0.87</th>
      <th>1.32</th>
    </tr>
    <th>Chelsea</th>
    <th>1.28</th>
    <th>0.98</th>
    <th>Stoke</th>
    <th>0.71</th>
    <th>1.46</th>
  </tr>
  <tr>
    <th>Crystal Palace</th>
    <th>0.99</th>
    <th>1.16</th>
    <th>Swansea</th>
    <th>0.57</th>
    <th>1.23</th>
  </tr>
  <tr>
    <th>Everton</th>
    <th>0.98</th>
    <th>1.09</th>
    <th>Tottenham</th>
    <th>1.38</th>
    <th>0.85</th>
  </tr>
  <tr>
    <th>Fulham</th>
    <th>0.68</th>
    <th>1.70</th>
    <th>Watford</th>
    <th>0.92</th>
    <th>1.39</th>
  </tr>
  <tr>
    <th>Huddersfield</th>
    <th>0.48</th>
    <th>1.50</th>
    <th>West Brom</th>
    <th>0.64</th>
    <th>1.22</th>
  </tr>
  <tr>
    <th>Leicester</th>
    <th>1.04</th>
    <th>1.08</th>
    <th>West Ham</th>
    <th>1.02</th>
    <th>1,25</th>
  </tr>
  <tr>
    <th>Liverpool</th>
    <th>1.75</th>
    <th>0.63</th>
    <th>Wolves</th>
    <th>0.94</th>
    <th>1.12</th>
  </tr>
  </tbody>
</table>
</div>

$$\gamma$$ = 1.28 and $$\rho$$ = -0.06.


## Conclusions
Firstly, lets have a few notes on the results:

- West Brom, Stoke, Swansea etc aren't in the Premier League? Why do they appear in the results? That's because they have been since the 17/18 season! Recall, that our dataset goes two seasons back.  
- Are Norwich really the third biggest attacking threat in the league? Probably not, but Norwich have only played 5 games in the Premier League over the last few years and putting three goals in against Man City (who according to the results have the strongest defense in the league) in the weekend will have boosted their attacking threat massively. I expect the attacking strength for Norwich to go down as the season progresses.
- Note about gamma

## Appendix
