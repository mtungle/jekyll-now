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

