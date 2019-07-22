---
title: "I Will Tell You How to Become Rich"
date: 2019-06-06
tags: [Quantitative Finance, Python]
excerpt: "In this post we will take advice from Mr Warren Buffet himself and see if we can find a way to become rich"
mathjax: true
header:
  overlay_image: "/images/I Will Tell You How to Become Rich/Warren.jpg"
  overlay_filter: 0.4
classes: wide
---
**"I will tell you how to become rich. Close the doors. Be fearful when others are greedy. Be greedy when others are fearful." - Warren Buffett.**

Ok, thats sounds easy enough and Warren Buffet arguably (not really) the most successfully investor in the world, so we should probably listen to him. His quote above has been analyzed [here](https://www.investopedia.com/articles/investing/012116/warren-buffett-be-fearful-when-others-are-greedy.asp), [here](http://www.arborinvestmentplanner.com/be-fearful-when-others-are-greedy/) and [here](https://www.gurufocus.com/news/4199/investment-warfare-be-fearful-when-others-are-greedy-and-greedy-when-others-are-fearful--warren-buffett) and holds a lot more meaning than when people tell you to "Buy low and sell high". Of course we all want to buy low, but how do we know when the market is low? Well, according to Mr Buffet, when people are fearful and vice verse for when the market is high. Fear and greed are emotions we can measure through reading The FT and talking to people. Hence, it can act as an investment strategy, "Buy low and sell high" can not. Even though a large proportion of the markets trading volume is driven by algorithmic trading, psychological and emotional factors still come into play. Conventional opinion on markets tend to rely on recent development. When market go up, investors become greedy and prices boil over, and one should be cautious lest they overpay for an asset that subsequently leads to anemic returns. When markets go down, and investors are fearful, it may present a good buying opportunity.

In this post I will take this idea and look to produce strategies which measures the optimism in market by looking at recent pricing and making investment decisions according. I will then post adds on dodgy websites saying "I hear this guy makes £5000 per month without leaving the house"

## A Strategy

The key is for the strategy to measure the level of optimism in the market and invest and disinvest accordingly. Recent performance is the largest driver of market optimism. On the back of a few strong weeks, people tend to have optimistic views on the market and vice versa. The proposed strategy is a simple one and can be broken down into four points:

- We invest all our money into the market at time 0.
- Once the market goes down a `Sell_Number` of time successfully, we sell all our holdings
- Once the market goes up a `Buy_Number` of time successfully, we invest all of our money in the market again
- Each transaction costs and amount equal to `Cost_Per_Transaction`. This is a set number, arguably it should be a percentage and could be easily changed to be just that.

To test the strategy we will run a bunch of pair for `Sell_Number` and `Buy_Number` against the scenario when you invest at time 0 and keep your money invested. The market will be an index. In this post we will look at the FTSE 100, Nikkei 250 and Gold index. Here's a bunch of code:

```python
import pandas as pd
import numpy as np
# So that the whole np array is shown when printed
import sys
np.set_printoptions(threshold=sys.maxsize)

def Investment_Function(Index_List, Start_Money, Cost_Per_Transaction, Sell_Number, Buy_Number):
    """Function which takes an Index List, how much money you have to start with (float), the Cost per transaction (float), how many ups it takes for you to sell (int) and how many downs it takes for you to buy (int).
    Returns a matrix.
    First colum: Index
    Second column: Units held at each time,
    Third column: Value held at each time
    Fourth column: An explenation of what is happening"""

    # Making the units and value lists, filling with 0's. zerolistmaker is a function defined earlier in the module.
    Units = zerolistmaker(len(Index_List))
    Value = zerolistmaker(len(Index_List))
    Exp = zerolistmaker(len(Index_List))

    # Caluclating how many units we start with filling the first entries in the lists
    Start_Units = Start_Money/Index_List[-1]
    Units[-1] = Start_Units
    Value[-1] = Start_Money
    Exp[-1] = "Start"

    # "In" indicates if we are invested our not. "Count" will indicate how many ups/downs we have had in sucession
    In = True
    Count = 0

    # Working our way up the Index_List to work out when to buy and sell
    for i in range(-2,-len(Index_List)-1,-1):
        # We are In, Index goes up and we are below the selling point, so we dont sell
        if In and Index_List[i] > Index_List[i+1] and Count < (Sell_Number-1):
            Count += 1
            Units[i] = Units[i+1]
            Value[i] = Units[i]*Index_List[i]
            Exp[i] = "Up, but hold"
        # We are In, Index goes up and we hit the selling point, so we sell
        elif In and Index_List[i] > Index_List[i+1] and Count == (Sell_Number-1):
            Count = 0
            Units[i] = 0
            Value[i] = Units[i+1]*Index_List[i] - Cost_Per_Transaction
            # Returns 0 if we have run out of money
            if Value[i] < 0:
                Value[i] = 0
                Units[i] = 0
                return(np.column_stack((Index_List, Units, Value, Exp)))
            In = False
            Exp[i] = "Sell"
        # We are In, Index goes down, so we dont sell
        elif In and Index_List[i] < Index_List[i+1]:
            Count = 0
            Units[i] = Units[i+1]
            Value[i] = Units[i]*Index_List[i]
            Exp[i] = "Down, so hold"
        # We are not In, Index goes down and we we are bellow the buying point, so we don't buy
        elif not(In) and Index_List[i] < Index_List[i+1] and Count < (Buy_Number-1):
            Count += 1
            Value[i] = Value[i+1]
            Exp[i] = "Down, but don't buy"
        # We are not In, Index goes down and we hit the buying point, so we buy
        elif not(In) and Index_List[i] < Index_List[i+1] and Count == (Buy_Number-1):
            Count = 0
            Units[i] = (Value[i+1] - Cost_Per_Transaction)/Index_List[i]
            Value[i] = Value[i+1] - Cost_Per_Transaction
            # Returns 0 if we have run out of money
            if Value[i] < 0:
                Value[i] = 0
                Units[i] = 0
                return(np.column_stack((Index_List, Units, Value, Exp)))
            In = True
            Exp[i] = "Buy"
        # We are not In, Index goes up, so we don't buy
        elif not(In) and Index_List[i] > Index_List[i+1]:
            Count = 0
            Value[i] = Value[i+1]
            Exp[i] = "Up, so don't buy"
        # If the Index does not change, just copy what we had before
        elif Index_List[i] == Index_List[i+1]:
            Value[i] = Value[i+1]
            Units[i] = Units[i+1]
            Exp[i] = "Index unchanged"
        else:
            print("Something funny is happening at i =" + str(i))

    # Returning the matrix with Index Value, Units, Value and the explanation
    return(np.column_stack((Index_List, Units, Value, Exp)))
```

In addition to `Sell_Number`, `Buy_Number` and `Cost_Per_Transaction` as discussed above `Investment_Function` takes `Index_List` as a list of index values with the most recent value as its first entry. `Start_Money` is simply how much money we have to invest at time 0. The return as a matrix which illustrates how the strategy is progressing. I have managed to source the daily closing value of the FTSE 100 index between 20 October 1997 to 31 May 2019 from [The London Stock Exchange](https://www.londonstockexchange.com/statistics/ftse/ftse.htm). Lets so what the following input explains a strategy where we have £10,000 to invest, the transaction cost is £1 and the `Sell_Number` and `Buy_Number` are both 2. Lets see what it looks like.

```python
print(Investment_Function(FTSE100_Index, 10000, 1, 2, 2))
```

```python
  ## ['4764.35' '1.94999951033' '9290.48016703' 'Buy']
  ## ['4863.79' '0.0' '9291.48016703' "Down, but don't buy"]
  ## ['4908.32' '0.0' '9291.48016703' "Up, so don't buy"]
  ## ['4897.43' '0.0' '9291.48016703' "Down, but don't buy"]
  ## ['4906.39' '0.0' '9291.48016703' "Up, so don't buy"]
  ## ['4842.33' '0.0' '9291.48016703' 'Sell']
  ## ['4801.92' '1.91901009783' '9214.93296898' 'Up, but hold']
  ## ['4755.39' '1.91901009783' '9125.64142913' 'Down, so hold']
  ## ['4840.72' '1.91901009783' '9289.39056077' 'Down, so hold']
  ## ['4970.2' '1.91901009783' '9537.86398824' 'Down, so hold']
  ## ['4991.47' '1.91901009783' '9578.68133302' 'Down, so hold']
  ## ['5148.76' '1.91901009783' '9880.52243131' 'Down, so hold']
  ## ['5225.91' '1.91901009783' '10028.5740604' 'Up, but hold']
  ## ['5211.02' '1.91901009783' '10000.0' 'Start']]
```

The above table gives the first 14 iterations of our strategy. Our initial £10,000 gives us approximately 1.92 units of the FTSE 100 index. After two successive ups in the index, we sell our holding at the 9th iteration and don't invest again until 5 days later. The `Sell_Number` and `Buy_Number` are quite small for this strategy, so we will end up with quite a few transactions here. The transaction cost is illustrated nicely between the 13th and 14th iteration where our value has decreased by £1. Even though we have lost over £700 in two weeks here (ough), note that we are holding 0.03 units more compared to when we started. The strategy is therefore doing better than simply buying and holding which would results in a constant number of units held. This is because the transaction cost is trumped by the decreasing index between out Sell and Buy dates. Let's look at how the strategy does over the full time horizon.  

<figure class="half">
    <a href="/images/I Will Tell You How to Become Rich/FTSE S1 Full.png"><img src="/images/I Will Tell You How to Become Rich/FTSE S1 Full.png"></a>
    <a href="/images/I Will Tell You How to Become Rich/FTSE S1 Parter.png"><img src="/images/I Will Tell You How to Become Rich/FTSE S1 Parter.png"></a>
</figure>

The left image tells us that the strategy performs significantly better than the index over the full time horizon. Buying and holding takes our £10k to £14k whilst the strategy return £55k. Interestingly the strategy seems to to do particularly well during the 2008 financial crisis. This is a time where the index dropped significantly and we would not expect any strategy to do well. To illustrate this, the right image above plots the strategy had we invested in September 2008 until the end of the year. Since this is a shorter time period, we can now see buy and sell actions. The strategy is invested until the start of October. Subsequently, flat periods of the orange graph illustrate times when we are not invested. Over this period, the index dropped 24% whilst the strategy returned 20%. Interesting!

The next step is to work out which strategy like this is the most successfully. To do so, firstly we need to consider which strategies are sensible to consider. Ie, it is not sensible to consider a strategy which sells after 25 ups, if the maximum number of successive ups is 10. The below code finds the maximum number of successive ups from an Index_List. There is an equivalent function for successive downs.

```python
def Max_Ups(Index_List):
    """Function which takes a list of numbers and return the the maximum numbers of sucessive ups working from right to left"""  
    Ans = 0
    Compar = 0

    # Working our way up the Index_List
    for i in range(-2,-len(Index_List)-1,-1):         
        if Index_List[i] > Index_List[i+1]:
            Compar += 1
        else:
            Ans = max(Compar, Ans)
            Compar = 0

    Ans = max(Compar, Ans)
    return(Ans)
```





## Another One

## Conclusion
