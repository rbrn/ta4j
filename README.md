# ta4j [![Build Status](https://travis-ci.org/mdeverdelhan/ta4j.png?branch=master)](https://travis-ci.org/mdeverdelhan/ta4j)

***Technical Analysis For Java***

![Ta4 main chart](https://raw.githubusercontent.com/wiki/mdeverdelhan/ta4j/img/ta4j_main_chart.png)

Ta4j is an open source Java library for [technical analysis](http://en.wikipedia.org/wiki/Technical_analysis). It provides the basic components for creation, evaluation and execution of trading strategies.

## Features

 * [x] 100% Pure Java - works on any Java Platform version 6 or later
 * [x] More than 40 technical indicators (Aroon, ATR, moving averages, parabolic SAR, RSI, etc.)
 * [x] A powerful engine for building custom trading strategies
 * [x] Utilities to run and compare strategies
 * [x] Minimal 3rd party dependencies
 * [x] Simple integration
 * [x] One more thing: it's MIT licensed

## Quick overview

At the beginning we just need a time series.

```java
// Getting a time series (from any provider: CSV, web service, etc.)
TimeSeries series = createTimeSeries();
```

#### Using indicators

```java
// Getting the close price of the ticks
double firstClosePrice = series.getTick(0).getClosePrice();
System.out.println("First close price: " + firstClosePrice.toDouble());
// Or within an indicator:
ClosePriceIndicator closePrice = new ClosePriceIndicator(series);
// Here is the same close price:
System.out.println(firstClosePrice.isEqual(closePrice.getValue(0))); // equal to firstClosePrice

// Getting the simple moving average (SMA) of the close price over the last 5 ticks
SMAIndicator shortSma = new SMAIndicator(closePrice, 5);
// Here is the 5-ticks-SMA value at the 42nd index
System.out.println("5-ticks-SMA value at the 42nd index: " + shortSma.getValue(42).toDouble());

// Getting a longer SMA (e.g. over the 30 last ticks)
SMAIndicator longSma = new SMAIndicator(closePrice, 30);
```

Ta4j includes [more than 40 technical indicators](http://github.com/mdeverdelhan/ta4j/tree/master/ta4j/src/main/java/eu/verdelhan/ta4j/indicators).

#### Building a trading strategy

```java
// Buying rules
// We want to buy:
//  - if the 5-ticks SMA crosses over 30-ticks SMA
//  - or if the price goes below a defined price (e.g $800.00)
Rule buyingRule = new CrossedUpIndicatorRule(shortSma, longSma)
        .or(new CrossedDownIndicatorRule(closePrice, Decimal.valueOf("800")));

// Selling rules
// We want to sell:
//  - if the 5-ticks SMA crosses under 30-ticks SMA
//  - or if if the price looses more than 3%
//  - or if the price earns more than 2%
Rule sellingRule = new CrossedDownIndicatorRule(shortSma, longSma)
        .or(new StopLossRule(closePrice, Decimal.valueOf("3")))
        .or(new StopGainRule(closePrice, Decimal.valueOf("2")));
```

See also:  [Algorithmic trading strategies](http://en.wikipedia.org/wiki/Algorithmic_trading#Strategies)

#### Running our juicy strategy

```java
// Running our juicy trading strategy...
TradingRecord tradingRecord = series.run(new Strategy(buyingRule, sellingRule));
System.out.println("Number of trades for our strategy: " + tradingRecord.getTradeCount());
```

#### Analyzing our results

```java
// Getting the cash flow of the resulting trades
CashFlow cashFlow = new CashFlow(series, tradingRecord);

// Getting the profitable trades ratio
AnalysisCriterion profitTradesRatio = new AverageProfitableTradesCriterion();
System.out.println("Profitable trades ratio: " + profitTradesRatio.calculate(series, tradingRecord));
// Getting the reward-risk ratio
AnalysisCriterion rewardRiskRatio = new RewardRiskRatioCriterion();
System.out.println("Reward-risk ratio: " + rewardRiskRatio.calculate(series, tradingRecord));

// Total profit of our strategy
// vs total profit of a buy-and-hold strategy
AnalysisCriterion vsBuyAndHold = new VersusBuyAndHoldCriterion(new TotalProfitCriterion());
System.out.println("Our profit vs buy-and-hold profit: " + vsBuyAndHold.calculate(series, tradingRecord));
```

## Maven configuration

Ta4j is available on [Maven Central](http://search.maven.org/#search|ga|1|a%3A%22ta4j%22). You just have to add the following dependency in your `pom.xml` file.

```xml
<dependency>
    <groupId>eu.verdelhan</groupId>
    <artifactId>ta4j</artifactId>
    <version>0.6</version>
</dependency>
```

For ***snapshots***, add the following repository to your `pom.xml` file.
```xml
<repository>
    <id>sonatype snapshots</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots</url>
</repository>
```
The current snapshot version is `0.7-SNAPSHOT`.


## Getting Help

This README and the [wiki](https://github.com/mdeverdelhan/ta4j/wiki) are the best places to start learning about ta4j.

You can ask anything on [my Twitter account](http://twitter.com/MarcdeVerdelhan).

For more detailed questions, use the [issues tracker](http://github.com/mdeverdelhan/ta4j/issues).


## Contributing to ta4j

Here are some ways for you to contribute to ta4j:

  * [Create tickets for bugs and new features](http://github.com/mdeverdelhan/ta4j/issues) and comment on the ones that you are interested in.
  * [Fork this repository](http://help.github.com/forking/) and submit pull requests.
  * Consider donating for new feature development. Bitcoin address: 13BMqpqbzJ62LjMWcPGWrTrdocvGqifdJ3 
