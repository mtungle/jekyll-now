---
layout: post
description: The trading idea presented in this document could make a profit of 74% capital gain over the period from Jan 2017 to Feb 2018 for trading XBT CFD contract in BitMEX exchange.
tags: Machine-Learning Matlab Python Trading Strategy
---

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

### Period 05/06/2018 - 14/06/2018

![GapOptimization](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p12-GapOptimization.png)

## Appendix

### Trade History

![History](https://mtungle.github.io/images/Jump-Reversal-Marketmaker/p13-TradeHistory.png)

### Sample Code

Python code to place orders

```python
    def tracking_orders(self, buy_stop_orders, sell_stop_orders, buy_limit_orders, sell_limit_orders):
        """Converge the orders we currently have in the book with what we want to be in the book.
           This involves amending any open orders and creating new ones if any have filled completely.
           We assume all of the orders is stop market order"""
        #tickLog = self.exchange.get_instrument()['tickLog']
        to_amend = []
        to_create = []
        to_cancel = []
        buy_stop_matched = 0
        sell_stop_matched = 0
        buy_limit_matched = 0
        sell_limit_matched = 0
        #existing_orders = self.exchange.get_orders()

        # Check all existing orders and match them up with what we want to place.
        # If there's an open one, we might be able to amend it to fit what we want.
        for order in self.existing_orders:
            try:
                if order['side'] == 'Buy' and order['ordType']== 'Stop':
                    desired_order = buy_stop_orders[buy_stop_matched]
                    buy_stop_matched += 1
                    if desired_order['stopPx'] != order['stopPx'] or desired_order['orderQty'] != order['orderQty']:
                        to_amend.append({'orderID': order['orderID'], 'orderQty': desired_order['orderQty'],'stopPx': desired_order['stopPx'], 'side': order['side']})

                if order['side'] == 'Sell' and order['ordType']== 'Stop':
                    desired_order = sell_stop_orders[sell_stop_matched]
                    sell_stop_matched += 1
                    if desired_order['stopPx'] != order['stopPx'] or desired_order['orderQty'] != order['orderQty']:
                        to_amend.append({'orderID': order['orderID'], 'orderQty': desired_order['orderQty'],'stopPx': desired_order['stopPx'], 'side': order['side']})

                if order['side'] == 'Buy' and order['ordType']== 'Limit':
                    desired_order = buy_limit_orders[buy_limit_matched]
                    buy_limit_matched += 1
                    if desired_order['price'] != order['price'] or desired_order['orderQty'] != order['orderQty']:
                        to_amend.append({'orderID': order['orderID'], 'orderQty': desired_order['orderQty'],'price': desired_order['price'], 'side': order['side']})

                if order['side'] == 'Sell' and order['ordType']== 'Limit':
                    desired_order = sell_limit_orders[sell_limit_matched]
                    sell_limit_matched += 1
                    if desired_order['price'] != order['price'] or desired_order['orderQty'] != order['orderQty']:
                        to_amend.append({'orderID': order['orderID'], 'orderQty': desired_order['orderQty'],'price': desired_order['price'], 'side': order['side']})

            except IndexError:
                # Will throw if there isn't a desired order to match. In that case, cancel it.
                to_cancel.append(order)

        while buy_stop_matched < len(buy_stop_orders):
            to_create.append(buy_stop_orders[buy_stop_matched])
            buy_stop_matched += 1

        while sell_stop_matched < len(sell_stop_orders):
            to_create.append(sell_stop_orders[sell_stop_matched])
            sell_stop_matched += 1

        while buy_limit_matched < len(buy_limit_orders):
            to_create.append(buy_limit_orders[buy_limit_matched])
            buy_limit_matched += 1

        while sell_limit_matched < len(sell_limit_orders):
            to_create.append(sell_limit_orders[sell_limit_matched])
            sell_limit_matched += 1

        if len(to_amend) > 0:
            # This can fail if an order has closed in the time we were processing.
            # The API will send us `invalid ordStatus`, which means that the order's status (Filled/Canceled)
            # made it not amendable.
            # If that happens, we need to catch it and re-tick.
            try:
                self.exchange.amend_bulk_orders(to_amend)
            except requests.exceptions.HTTPError as e:
                errorObj = e.response.json()
                if errorObj['error']['message'] == 'Invalid ordStatus':
                    logger.warn("Amending failed. Waiting for order data to converge and retrying.")
                    sleep(0.5)
                    return self.place_orders()
                else:
                    logger.error("Unknown error on amend: %s. Exiting" % errorObj)
                    sys.exit(1)

        if len(to_create) > 0:
            logger.info("Creating %d orders:" % (len(to_create)))
            self.exchange.create_bulk_orders(to_create)

        # Could happen if we exceed a delta limit
        if len(to_cancel) > 0:
            self.exchange.cancel_bulk_orders(to_cancel)

```

Matlab code for backtesting

```matlab
%%%%%%%%
%Read data
%%%%%%%%
 
data=csvread('XBTUSD.Last_1-05_20-05.csv');
%1st is opening price
%2nd column is maximum
%3rd column is minimum
%4th is closing price
 
%number of candle stick
len=length(data(:,1));
%len=100;
 
%lets define a jump, a jump starts at the opening price and end at the max
%or minimum
jump_side=(data(:,4)-data(:,1))./abs((data(:,4)-data(:,1))); %plus for pump, minus for dump
jump_side(isnan(jump_side))=0; %zero value jump is a pump
jump_magnitude=zeros(len,1);
jump_start=zeros(len,1);
jump_end=zeros(len,1);
 
for i=1:len
    if jump_side(i)>0
       jump_magnitude(i)=data(i,2)-data(i,1);
       jump_start(i)=data(i,1);
       jump_end(i)=data(i,2);
    else
       jump_magnitude(i)=data(i,1)-data(i,3);
       jump_start(i)=data(i,1);
       jump_end(i)=data(i,3);
    end
end
 
%calculate PnL
position=zeros(len,1);
XBT_position=zeros(len,1);
est_PnL=zeros(len,1);
est_price=zeros(len,1);%average of start and close of candle stick
Qty=20;
%gap=31;
no_trade=0;
rebate_rate=0.0225/100;
 
 
 
for i=2:len %lets not trade at the first candle just to avoid index zero
    if jump_magnitude(i)*jump_side(i)>=gap
        bought_price=jump_start(i)+gap;
        XBT_position(i)=XBT_position(i-1)-Qty/bought_price + rebate_rate * Qty/bought_price;%plus rebate
        %disp('reduced position');
        position(i)=position(i-1)-Qty;
        est_price(i)=(data(i,1)+data(i,4))/2;
        est_PnL(i)=XBT_position(i)-position(i)/est_price(i); 
        no_trade=no_trade+1;
    
    elseif jump_magnitude(i)*jump_side(i)<=-gap
        bought_price=jump_start(i)-gap;
        XBT_position(i)=XBT_position(i-1)+Qty/bought_price + rebate_rate * Qty/bought_price;%plus rebate
        %disp('increased position');
        position(i)=position(i-1)+Qty;
        est_price(i)=(data(i,1)+data(i,4))/2;
        est_PnL(i)=XBT_position(i)-position(i)/est_price(i); 
        no_trade=no_trade+1;
    else        
        XBT_position(i)=XBT_position(i-1);
        position(i)=position(i-1);
        est_price(i)=(data(i,1)+data(i,4))/2;
        est_PnL(i)=XBT_position(i)-position(i)/est_price(i); 
    end
end


```




















