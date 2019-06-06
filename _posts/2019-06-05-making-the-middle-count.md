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
<table>
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

The figures have simply been derived from looking at what betting companies think along with personal judgment. Analysing fixture difficulty is a topic in itself and will be covered in a later post. For the purpose of this post, the numbers above will suffice.

When picking your middle men, it is desirable always to have the option to field four with decent fixtures over the next few gameweek. The player with the hardest fixture each gameweek doesn't matter to much, as he will sit on the bench. Hence, how strong your middle men are is a function of the four easiest fixtures over next few gameweeks. lets apply this reasoning to my middle men.

The table on the below displays the difficulty of the next five fixtures played by my middle men. The fixture highlighted in red, is the toughest each gameweek and if no transfers are made over the period, the player with the red fixture will be put on the bench. The sum of the the non coloured cells represent how strong my middle men are, in this case it's 1061.3. The lower this value is the better.  

<div>
<table border="1" class="dataframe" style="width:43%">
  <caption>Table 1. The fixture difficulty of my middle men.</caption>
  <thead>
    <tr style="text-align: right;">
      <th>Player</th>
      <th>GW16</th>
      <th>GW17</th>
      <th>GW18</th>
      <th>GW19</th>
      <th>GW20</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Wilson</th>
      <th>73.0</th>
      <th>71.5</th>
      <th>42.0</th>
      <th bgcolor="#FF0000">91.0</th>
      <th bgcolor="#FF0000">80.6.0</th>
    </tr>
    <tr>
      <th>Richarlison</th>
      <th>44.0</th>
      <th bgcolor="#FF0000">104.0</th>
      <th bgcolor="#FF0000">70.0</th>
      <th>54.6</th>
      <th>54.6</th>
    </tr>
    <tr>
      <th>Schurle</th>
      <th>80.6</th>
      <th>50.0</th>
      <th>52.0</th>
      <th>55.0</th>
      <th>34.0</th>
    </tr>
    <tr>
      <th>Mooy</th>
      <th bgcolor="#FF0000">81.9</th>
      <th>40.0</th>
      <th>38.0</th>
      <th>80.6</th>
      <th>50.7</th>
    </tr>
    <tr>
      <th>Felipe Anderson</th>
      <th>42.0</th>
      <th>50.7</th>
      <th>44.0</th>
      <th>49.4</th>
      <th>54.6</th>
    </tr>
  </tbody>
</table>
</div>

## The code
**It would be useful to calculate the above score for all possible combinations of five middle men.** This will be the the goal of this section.* It is of course not necessary to consider all midfielders and forwards in £5.5m-£7.5m range, but perhaps a subset of players who are in good form and are likely to play well going forward. I have decided to look at players who have at least 48 total points or 5 in the FPL form rating. The bellow table displays which teams have players under this condition. In total, there are 30 players we wish to consider.

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ARS</th>
      <th>BOU</th>
      <th>BHA</th>
      <th>BUR</th>
      <th>CAR</th>
      <th>CHE</th>
      <th>CRY</th>
      <th>EVE</th>
      <th>FUL</th>
      <th>HUD</th>
      <th>LEI</th>
      <th>LIV</th>
      <th>MCI</th>
      <th>MNU</th>
      <th>NEW</th>
      <th>SOU</th>
      <th>TOT</th>
      <th>WAT</th>
      <th>WHU</th>
      <th>WOL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Players</th>
      <th>0</th>
      <th>3</th>
      <th>2</th>
      <th>1</th>
      <th>1</th>
      <th>3</th>
      <th>2</th>
      <th>3</th>
      <th>2</th>
      <th>1</th>
      <th>1</th>
      <th>2</th>
      <th>1</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>1</th>
      <th>1</th>
      <th>2</th>
      <th>1</th>
    </tr>
  </tbody>
</table>
</div>

I have made a program in R with the above details and highlighted goal in mind. Lets break this program down and comment on some of the results.

```r
#Adding number of interesting players
FPL[["Players"]] <- c(0,3,2,1,1,3,2,3,2,1,1,2,1,0,1,2,1,1,2,1)

#Removing teams with no interesting players
FPL <- FPL[!(FPL$Players==0),]
#Rearranging DF
FPL <- FPL[,c(1,40,2:39)]

#Making vector containing all the player teams
TeamsVector <-c()
for(i in 1:length(FPL$Team)){
  j <- cumsum(FPL$Players)[i]-FPL$Players[i]
  for(k in 1:FPL$Players[i]){
    TeamsVector[j+k] <- FPL$Team[i]
    }
}

#Making matrix containing all five player combinations
PlayerMatrix <- t(combn(TeamsVector,5))

GwStart = 16 #First gameweek we consider
GwEnd = 20 #Last gameweek we consider
n = GwEnd - GwStart + 1 #Number of games we are looking at

#Making DF only including gameweeks we consider
FPL2 <- FPL[c(1,(GwStart+2):(GwEnd+2))]
```

After the above code, "FPL" is a dataframe which only includes teams with players we wish to consider, how many interesting players there are in each team and the difficulty for these teams. See Figure 1 of the Appendix. "FPL2" later becomes the equivalent dataframe only including the gameweeks we wish to consider.  

The vector "TeamsVector" contains all the players we wish to consider. So far, we don't distinguish between players in the same team. So even though Wilson and King are interesting players, they both appear as "Bournemouth" in TeamsVector.

The rows in the matrix "PlayerMatrix" add up to all five player combinations taken from the set of our 30 interesting players. The matrix has $${30 \choose 5} = 142506$$ rows and the first few are displayed in Figure 2 of the Appendix. Note that rows 5 and 6 are the same. This is because we are considering two players from Chelsea.

Below we introduce some functions which will be useful for our analysis. I will first present the code and then explain their structure.

```r
SumMinusMax <- function(x){
  #Takes numeric vector and returns the cumulative value minus the largest value
  return(sum(x)-max(x))
}

MatrixMaker  <- function(L,DataFrame,n=5){
  #takes a list of teams and a date frame and produces a matrix with their game difficulties. n is the number of gameweeks we are looking at.
  v1 <- as.numeric(DataFrame[match(L[1],DataFrame$Team),c(2:(n+1))])
  v2 <- as.numeric(DataFrame[match(L[2],DataFrame$Team),c(2:(n+1))])
  mat <- matrix(c(v1,v2),nrow=length(v1))
  for(i in 3:length(L)){
    vi <- as.numeric(DataFrame[match(L[i],DataFrame$Team),c(2:(n+1))])
    mat <- cbind(mat,vi)
  }
  return(t(mat))
}

Score <- function(M) {
  #Takes a matrix and return the sum of the SumMinusMax values of the columns
  Ans <- 0
  for(i in 1:ncol(M)){
    Ans <- Ans + SumMinusMax(M[,i])
  }
  return(Ans)
}
```
"SumMinusMax" is very simple. It takes any numeric vector, finds its cumulative value and subtracts its largest value.

"MatrixMaker" produces a matrix similar to the one in Table 1 above. Given a list of five teams, it produces a matrix with the teams fixture difficulty over the next n games. The variable "DataFrame" needs to be in the form of FPL2.

"Score" takes a matrix as the one produced by MatrixMaker and returns the score of that set of middle me. Our goals is to apply MatrixMaker to all our rows in PlayerMatrix, followed by the Score function. The code below does this and stores the results in the vector "Scores".

```r
#Filling the vector Scores with the scores for the the set of players in the PlayerMatrix
Scores <- c()
for(i in 1:nrow(PlayerMatrix)){
  Scores[i] <- Score(MatrixMaker(PlayerMatrix[i,],FPL2))

}

#Add the scores to the PlayerMatrix and a couple of interesting stats
Ans <- cbind(PlayerMatrix, Scores, rank(Scores),rank(Scores)/length(Scores))
```

In Ans, we combine "Scores", a couple of useful stats and PlayerMatrix. The result is seen in Figure 3 of the Appendix.

## Results
The table below shows row 20939 of Ans which happens to be the combination of middle men I currently possess and we can hence conclude that my boys are within the top 26% of all combinations.Luckily the score of 1061.3 is consistent with what we had before.

<div>
<table>
  <thead>
    <tr style="text-align: right;">
      <th>Player 1</th>
      <th>Player 2</th>
      <th>Player 3</th>
      <th>Player 4</th>
      <th>Player 5</th>
      <th>Scores</th>
      <th>Rank</th>
      <th>Within (%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bournemouth</th>
      <th>Everton</th>
      <th>Fulham</th>
      <th>Huddersfield</th>
      <th>West Ham</th>
      <th>1061.3</th>
      <th>36218</th>
      <th>25.42</th>
    </tr>
  </tbody>
</table>
</div>

## Appendix

{% include figure image_path="/images/Making the middle count/Figure 1.jpg" alt="this is a placeholder image" caption="Figure 1. Data frame after delating teams with no interesting teams and some rearranging." %}

{% include figure image_path="/images/Making the middle count/Figure 2.jpg" alt="this is a placeholder image" caption="Figure 2. The first 18 rows of PlayerMatrix." %}

{% include figure image_path="/images/Making the middle count/Figure 3.jpg" alt="this is a placeholder image" caption="Figure 3. The first 18 rows of Ans" %}
