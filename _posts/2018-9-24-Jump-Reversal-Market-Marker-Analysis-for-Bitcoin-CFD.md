## Summary
The trading idea presented in this document could make a profit of 74% capital gain over the period from Jan 2017 to Feb 2018 for trading XBT CFD contract in BitMEX exchange. I have written a program to automate my trading strategy. The program went live on 05 Jun 2018. I also wrote another program to backtest my strategy.

Section 1 shows my real trading results from 05 Jun 2018. Section 2 presents the backtesting results. Section 3 outlines the trading idea. Section 4 talks about how I build my trading program. Section 5 is about the simulation used for backtesting. Section 6 shows how I optimize the strategy parameters by Monte Carlo method.

Note: The trading strategy is an adaptation of market making strategy which means we always place buy limit orders and sell limit orders. It also does not have a clear exit point, whether the position size increases and decreases will depend on the price movement. So for the results in section 1 and section 2, we will draw PnL along with the position size (positive means long position and negative means short position). And the position size can be managed to stay round zero.

## Change Log

14/06/2018: Update version 1.01. Update real trade results in section I. Update new parameter optimization for the period 10/06 – 14/06.

21/09/2018: Update version 1.02. Added wallet balance graph.

## Real Trade Results

Period: 05 Jun 2018 – 07 Jun 2018

Number of trades: 44

Max position size: 120 USD

In the period 11 Jun to 14 Jun, the trading algorithms experienced a significant loss of -0.001 XBT. Most of the loss occurred during the first 24 hours of strong bearish move where XBT price drop from 7600 to 6600 without retracement. Note that our trading algorithm makes profits when there is fluctuation within a certain range. If the price strictly and strongly moving up or down, the loss is expected. This loss is normal as we will see even bigger loss in the backtesting results in section 2. However, I feel like it is a good time to fine tuning our parameters taking into account the current level of volatility. The parameter optimization will be presented in section 6. 

![PnL and position size for real trading account](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p1-PnL.png)

![Wallet Balance](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p2-WalletBalance.png)

## Backtesting Results

Period: Jan 2017 – Feb 2018

Number of trades: 31400

Estimated capital (zero leverage): 600 USD

Accumulate profits: 0.0634 XBT

Capital growth for 1 year (assume 1 XBT = 7000 USD):  74%

![Backtesting](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p3-Backtesting.png)

## Trading Idea

Definition: Spike is a big price movement over a short amount of time (less than a minute).

Big spikes are quite common in Bitcoin. After the spike, Bitcoin price is likely to move back to where it was. We would like to take advance of those spikes to buy lower and sell higher than the current average price. To do that, we simply set limit orders to always buy lower and sell higher than the current price. Those limit order price is adjusted periodically so without the spikes when the price movement is relatively slow the limit orders will never be filled. In short, we only buy low and sell high when the spikes occur.

Let’s look at some scenario where the limit orders may be filled.

Potential losing scenario. The price keeps moving down with downward spikes and retrace slowly. There is no upward spike so we would not have any sell order executed.

![Losing](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p4-Losing.png)

Potential winning scenario. There are both upward spikes and downward spikes.

![Winning](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p5-Winning.png)

### Trade pseudo code
```
while true:
  update current market price
	place a buy limit order at (current market price - spread constant)
	place a sell limit order at (current market price + spread constant)
	if position size exceed a threshold:
	adjust spread constant in order to reduce position size
	sleep for sometime
```

## Automation Implementation

The trading automation program is written in Python. The program is run inside an Ubuntu cloud server. In every short time interval (a few seconds), the program will connect to BitMEX server, examine the current market conditions, then adjust orders if needed.

![Infrastructure](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p6-Infrastructure.png)


Automate trading in action. It shows bid and ask offers are automatically adjusted base on the current price
![Action](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p7-Action.png)

## Market Replay Simulation

Ninja trader is used to feed historical data from BitMEX server and Matlab is used to replay the price movement.

For backtesting our strategy, we need to recreate the spikes which happened in the past. Unfortunately, the historical data that available freely only has information about the candle sticks which is the opening price, closing price, minimum price, and maximum price. So the precise information about the spike is lost. We only can estimate the past spikes. The figure below shows how the spikes are recreated using historical candle data.

![Spike](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p8-Spike.png)

## Monte Carlo Simulation - Parameters Optimization

### Period 01/05/2018 - 20/05/2018

The same simulation program used for backtesting was run repeatedly for different values of gap between the current price and the limit order price for the recent historial data from 01/05 to 20/05.

![GapOptimization](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p9-GapOptimization.png)

![GapOptimization](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p10-GapOptimization.png)

![GapOptimization](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p11-GapOptimization.png)





















