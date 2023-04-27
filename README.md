//+------------------------------------------------------------------+
//|                                                       Projectj50 |
//|                   Copyright 2023, Your Name                        |
//|                             https://www.yourwebsite.com            |
//+------------------------------------------------------------------+
#property copyright "Copyright 2023, Your Name"
#property link      "https://www.yourwebsite.com"
#property version   "1.00"
#property strict

// define input parameters
extern double lotsize = 0.5;
extern int stoploss = 50;
extern int takeprofit = 100;
extern double sar_step = 0.02;
extern double sar_maximum = 0.2;
extern double adx_threshold = 25;

// define variables
int buy_order;
int sell_order;

void OnTick()
{
    double ema8 = iMA(NULL, 0, 8, 0, MODE_EMA, PRICE_CLOSE, 0);
    double ema14 = iMA(NULL, 0, 14, 0, MODE_EMA, PRICE_CLOSE, 0);
    double ema21 = iMA(NULL, 0, 21, 0, MODE_EMA, PRICE_CLOSE, 0);
    double ema50 = iMA(NULL, 0, 50, 0, MODE_EMA, PRICE_CLOSE, 0);
    double ema100 = iMA(NULL, 0, 100, 0, MODE_EMA, PRICE_CLOSE, 0);
    double ema200 = iMA(NULL, 0, 200, 0, MODE_EMA, PRICE_CLOSE, 0);
    double adx = iADX(NULL, 0, 14, PRICE_CLOSE, MODE_MAIN, 0);
    double di_minus = iADX(NULL, 0, 14, PRICE_CLOSE, MODE_MINUSDI, 0);
    double di_plus = iADX(NULL, 0, 14, PRICE_CLOSE, MODE_PLUSDI, 0);
    double sar = iSAR(NULL, 0, sar_step, sar_maximum, 0);

    // check for buy conditions
    if (ema8 > ema14 && ema14 > ema21 && adx > adx_threshold && di_minus < adx_threshold && Bid > sar)
    {
        // check if there is no existing buy order
        if (buy_order == 0)
        {
            // open buy order
            Alert(Symbol());
            buy_order = OrderSend(Symbol(), OP_BUY, lotsize, Ask, 0, 0, 0, "Buy", 0, 0, Blue);
        }
    }
    else
    {
        // check if there is an existing buy order
        if (buy_order != 0)
        {
            // close buy order
            OrderClose(buy_order, lotsize, Bid, 0, Green);
            buy_order = 0;
        }
    }

    // check for sell conditions
    if (ema8 < ema14 && ema14 < ema21 && adx > adx_threshold && di_plus < adx_threshold && Bid < sar)
    {
        // check if there is no existing sell order
        if (sell_order == 0)
        {
            // open sell order
            sell_order = OrderSend(Symbol(), OP_SELL, lotsize, Bid, 0, 0, 0, "Sell", 0, 0, Red);
        }
    }
    else
    {
        // check if there is an existing sell order
        if (sell_order != 0)
        {
            // close buy order
            OrderClose(sell_order, lotsize, Bid, 0, Yellow);
            sell_order = 0;
        }
    }
    
    //Alert(MarketInfo(NULL, MODE_MAXLOT));
    
}
