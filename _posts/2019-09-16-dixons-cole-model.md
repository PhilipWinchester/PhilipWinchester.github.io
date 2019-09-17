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

Here $$X$$ and $$Y$$ are the home and away goals scored, $$\alpha_{i}$$ and $$\beta_{i}$$ is the home team attacking and defensive strength and equivalently for the away team $$j$$. $$\gamma$$ is the home team advantage factor and $$\rho$$ is the newly introduced dependance factor. Setting $$\rho = 0$$ corresponds to independence. In our work going forward, we will aim to estimate a non-zero value for $$\rho$$ which deals with the issues we have for low scoring games.

Could be a lot easier had the independance thing (which is not very strong anyway), had been included. the time dependance is strong though and should be included. All probably needed for betting and even more things as well.
