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

## Low scoring games
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

## Time decay
Manchester City loosing 3-2 against Norwich yesterday is more interesting to us than City beating Stoke 7-0 two years ago with regards to estimating the current attack and defensive strength of City. This is not taken into account at the moment. In fancy words: The current model is static - the attack and defense parameters of each teams are regarded as constant throughout time. Lets fix that!

Lets start by ignoring the above statement. As it stands, we have $$2n + 2$$ parameters to estimate, where $$n$$ is the number of teams in our dataset, 20 for the PL. $$n$$ $$\alpha$$'s, $$n$$ $$\beta$$'s, $$\gamma$$ and $$\rho$$. When estimating parameters we often refer to the likelihood of our observations and try to maximize this through varying our parameters. In simple terms: A bunch of games have been played, what values do we give our parameters so that the outcome of the games played were actually very likely to happen in the first place? In even simpler terms: John scored 5 goals in every single game ha played in last season. We want to model his scoring rate with a Poisson distribution. Johns Poisson parameters is probably about 5, not 1. Likelihoods are a key concept in probability theory and in this post going forward. Read up about it [here](https://en.wikipedia.org/wiki/Likelihood_function).

In our case, the likelihood function takes the following form:

$$\begin{align*}  
  L &= \prod_{k=1}^{N} \tau_{\lambda_k, \mu_k}(x_k,y_k)\frac{e^{-\lambda_k} {\lambda_{k}}^{x_k} }{x_k!}\frac{e^{-\mu_k} {\mu_{k}}^{y_k} }{y_k!}
\end{align*}$$

In the likelihood function, $$k$$ run through all N matched in our dataset and multiply these together given our parameters where $$\lambda_k = \alpha_{i(k)}\beta_{j(k)} \gamma$$ and $$\mu_k = \alpha_{j(k)}\beta_{i(k)}$$ are match dependent. In the final step above we have simply got rid of the factorial factors as these just combine to one very very big number which becomes irrelevant when we're trying to maximize the likelihood with respect to the parameters.

To maximize, we find our partial derivatives of $$L$$ with respect to the parameters (gradient if you will). This looks incredibly scary and a common trick in this case is to consider $$\log_e(L)$$ instead. This works since the $$\log$$ function is a monotonically increasing function (ie, it increases as its argument increases), hence the maximums of $$L$$ are the same as those for $$\log_e(L)$$. The gain here is that logs of products become sums, which are much easier to deal with. the log likelihood is:

$$\begin{align*}  
  LL(\alpha_i, \beta, i, \gamma, \rho, i=1,...,n) &= \sum_{k=1}^{N} \log(\tau_{\lambda_k, \mu_k}(x_k,y_k))-\lambda_k+x_k\log(\lambda_k)-\mu_k+y_k\log(\mu_k)
\end{align*}$$

Going back to the first paragraph

L(\alpha_i, \beta, i, \gamma, \rho, i=1,...,n) &= \prod_{k=1}^{N} P(X_{i(k),j(k)}=x_k,Y_{i(k),j(k)}=y_k) \\

\\
&\propto \prod_{k=1}^{N} \tau_{\lambda_k, \mu_k}(x_k,y_k)e^{-\lambda_k} \lambda_k ^x_k e^{-\mu_k} \mu_k^y_k

## Comments
Could be a lot easier had the independance thing (which is not very strong anyway), had been included. the time dependance is strong though and should be included. All probably needed for betting and even more things as well.

In the interest of keeping this post wihtin a 30 min read, we are going to take the value derived by Dixon and Cole as gospel. Please refer to their paper for an explanation of how this is derived and hit me with your strongest arguments for why team for is more or less variable now than in 1995 and I may consider writing a post on how the epsilon value is maxmeised with a more recent dataset. eps looks pretty small to me actually. would not be sirpsied if we arrive at a bigger value
