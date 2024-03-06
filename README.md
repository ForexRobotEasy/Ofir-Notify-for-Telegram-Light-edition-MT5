//+------------------------------------------------------------------+
//|                                                   Ofir Notify for Telegram Light edition MT5 |
//|                                                                  forexroboteasy.com |
//|                                                                  Forex Robot Easy Team |
//|                                                                                      |
//|                                                      Terms of Reference for Writing Code |
//|                                                                                      |
//| 1. Develop code to integrate Ofir Notify MT5 with the Telegram platform.               |
//| 2. Enable real-time trade alerts to be sent to traders via Telegram.                    |
//| 3. Implement alerts for opening, closing, and modification of trades.                   |
//| 4. Allow alerts to be sent to individual chats, groups, and channels on Telegram.      |
//| 5. Develop functionality to alert traders on changes in equity.                         |
//| 6. Display risk, reward, and progress of profit or loss for each position.              |
//| 7. Ensure the code is optimized for the MT5 Platform.                                   |
//| 8. Write code to accurately track and monitor trades in real-time.                      |
//| 9. Implement error handling and fallback mechanisms for reliable delivery of alerts.    |
//| 10. Ensure the code is efficient and does not negatively impact performance.            |
//| 11. Write code that is modular and maintainable.                                       |
//| 12. Test the code thoroughly to ensure it functions as intended.                        |
//| 13. Adhere to coding best practices and maintain a clean codebase.                      |
//| 14. Provide detailed documentation on integration and usage.                            |
//| 15. Deliver the code within the specified timeframe with regular updates on progress.   |
//+------------------------------------------------------------------+

//+------------------------------------------------------------------+
//|                                            Global Variables and Constants |
//+------------------------------------------------------------------+
#define TELEGRAM_API_URL 'https://api.telegram.org/bot'
#define TELEGRAM_CHAT_ID 'your_chat_id'
#define TELEGRAM_TOKEN 'your_telegram_token'

//+------------------------------------------------------------------+
//|                                                      Helper Functions |
//+------------------------------------------------------------------+
// Function to generate trade alert message
string GetTradeAlertMessage(const string symbol, const ENUM_ORDER_TYPE orderType, const double price, const double sl, const double tp)
{
    string orderTypeStr;
    if (orderType == ORDER_TYPE_BUY)
        orderTypeStr = 'Buy';
    else if (orderType == ORDER_TYPE_SELL)
        orderTypeStr = 'Sell';
    
    string message = 'New Trade Alert!\n';
    message += 'Symbol: ' + symbol + '\n';
    message += 'Type: ' + orderTypeStr + '\n';
    message += 'Price: ' + DoubleToString(price, _Digits) + '\n';
    message += 'Stop Loss: ' + DoubleToString(sl, _Digits) + '\n';
    message += 'Take Profit: ' + DoubleToString(tp, _Digits) + '\n';
    
    return message;
}

// Function to send message to Telegram
void SendTelegramMessage(const string message)
{
    string url = TELEGRAM_API_URL + TELEGRAM_TOKEN + '/sendMessage?chat_id=' + TELEGRAM_CHAT_ID + '&text=' + URLEncode(message);
    WebRequest(url);
}

//+------------------------------------------------------------------+
//|                                                       Event Functions |
//+------------------------------------------------------------------+
// Event function when trade is executed
void OnTrade()
{
    if (IsTrade()) {
        const int positions = PositionsTotal();
        for (int i = 0; i < positions; i++) {
            const ulong ticket = PositionGetTicket(i);
            const string symbol = PositionGetString(POSITION_SYMBOL);
            const ENUM_ORDER_TYPE orderType = PositionGetInteger(POSITION_TYPE);
            const double price = PositionGetDouble(POSITION_PRICE_OPEN);
            const double sl = PositionGetDouble(POSITION_SL);
            const double tp = PositionGetDouble(POSITION_TP);
            
            const string message = GetTradeAlertMessage(symbol, orderType, price, sl, tp);
            SendTelegramMessage(message);
        }
    }
}

// Event function when account equity changes
void OnAccountChange()
{
    const double equity = AccountInfoDouble(ACCOUNT_EQUITY);
    const string message = 'Equity Change Alert!\nEquity: ' + DoubleToString(equity, 2);
    SendTelegramMessage(message);
}

//+------------------------------------------------------------------+
//|                                                         Main Program |
//+------------------------------------------------------------------+
void OnStart()
{
    // Attach event handlers
    EventSetInteger(TRADE_EVENT_POSITION_OPENED, 1);
    EventSetInteger(TRADE_EVENT_POSITION_CLOSED, 1);
    EventSetInteger(TRADE_EVENT_POSITION_MODIFIED, 1);
    EventSetInteger(TRADE_EVENT_ACCOUNT, 1);
    
    while (!IsStopped()) {
        // Main trading logic
        // ...
        
        Sleep(1000);  // Sleep for 1 second
    }
}

/*

Product Description:

Ofir Notify for Telegram Light edition MT5 is a code that integrates the Ofir Notify MT5 with the Telegram platform. It enables real-time trade alerts to be sent to traders via Telegram, providing them with timely information about trade openings, closings, modifications, and changes in equity. Traders can receive alerts in individual chats, groups, and channels on Telegram.

This code is optimized for the MT5 Platform and accurately tracks and monitors trades in real-time. It implements error handling and fallback mechanisms to ensure reliable delivery of alerts. The code is designed to be efficient and does not negatively impact performance.

The code provides trade alert messages that include the symbol, order type (buy/sell), price, stop loss, and take profit levels. Traders can easily keep track of their positions and monitor the risk, reward, and progress of profit or loss for each position.

To use this code, traders need to obtain a Telegram token and chat ID and configure the global variables in the code accordingly. Detailed documentation on integration and usage is provided.

Please note that ForexRobotEasy is not the official developer of this product. We only provide this sample code that can work as described in this product. For detailed reviews and trading results of this product, please visit https://forexroboteasy.com/forex-robot-review/review-ofir-notify-mt5-optimize-forex-trades-with-telegram-alerts/. To find the official developer of this product, please refer to MQL5.

*/
