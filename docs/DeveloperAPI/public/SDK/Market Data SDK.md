# Market Data SDK

The Market Data SDK provides seamless integration with the Blitz Gateway Market Data APIs, enabling developers to access real-time market information such as Last Traded Price (LTP), Option Chain, and Quotes.

It offers robust WebSocket support for subscribing to and receiving live market updates, ensuring low-latency data delivery.

The SDK includes built-in functionality for secure authentication, market data retrieval, subscription management, and efficient handling of WebSocket connections, making it an ideal choice for building high-performance trading and analytics applications.

---

## Features
- **Authentication**: Secure login  to obtain an **API key**. With the **API key** and **user ID** Login via 
[AppLogin](../MarketDataSrevr/AppLogin.md)
 get an **Acess-Token**, we can authenticate and access the SDK.
- **Market Data APIs**:
  - Get LTP (Last Traded Price) for given instruments.
  - Get Option Chain data.
  - Get real-time quotes for instruments.
  - Get historial data.
- **WebSocket Streaming**: 
  - Subscribe to live market data for specific instruments.
  - Handle real-time market depth and index updates.
- **Error Handling**: Built-in error logging and retries for WebSocket reconnections.

---

## Installation

### 1. Clone the repository:
```bash
git clone https://github.com/yourusername/market-data-sdk.git
cd market-data-sdk
```
OR

Run the command
```bash
pip install blitz-sdk
```

### 2. Install dependencies:
This SDK requires Python 3.x and the `requests` and `websocket-client` libraries.

```bash
pip install -r requirements.txt
```

---


## Setup & Configuration

To start using the SDK, need the following:
- **API Key**: With  API key to acess *SDK* and  authenticat via [AppLogin](../MarketDataSrevr/AppLogin.md)
.
- **User ID**:  unique user identifier.

### Environment Variables (for sensitive data)
Store sensitive information, such as the API key and user ID, in environment variables for security.

```bash
export API_KEY="api-key"
export USER_ID="user-id"
```

Alternatively, can pass these directly to the client when initializing.

---

## Usage

### 1. Initialize the Client

```python
from marketdata.market_data import MarketDataClient
from marketdata.websocket_stream_handler import MarketDataWebSocketClient

client = MarketDataClient(
    api_key="api-key",
    user_id="user-id"
)
```

### 2. Authentication

*Market Data SDK* automatcally handles authentication by retrieving an access token upon initialization. The `MarketDataClient` internally calls the authentication process when the access token is needed.

### 3. Fetching Market Data

### LTP (Last Traded Price)

Retrieve the Last Traded Price (LTP) for one or more instruments using the `get_ltp()` method.

```python
instrument_ids = [1010010002000001, "NSECM|RELIANCE"]
ltp = client.get_ltp(instrument_ids)
print("LTP Response:", ltp)
```
Users can provide either the Instrument ID or the Instrument Name (in the format `ExchangeSegment|Symbol`) to fetch the LTP.

---

### Option Chain

Fetch the Option Chain for a specific symbol and expiry date using the `get_option_chain()` method.

```python
symbol = "NIFTY"
expiry_date = "20250424"
option_chain = client.get_option_chain(symbol, expiry_date)
print("Option Chain Response:", option_chain)
```

This method retrieves all available call and put options for the given symbol and expiry date.

---

### Quotes

Retrieves detailed market quotes for one or more instruments.
This method provides complete quote information — including last traded price, volume, and other key market data fields.

```python
instrument_ids = ["NSECM|RELIANCE"]
quote_data = client.get_quote(instrument_ids)
print("Quote Response:", quote_data)
```

---

### Historcal Data

Fetch historical market data for a given instrument using the get_historical_data() method. This allows you to analyze past price movements and trends over a specified time range.

```python
hist_data = client.get_historical_data("IRFC", "2024-01-11", "2025-11-11")
print("Historical Data Response:", hist_data)
```

---

### 4. WebSocket for Live Market Data

Use the WebSocket interface to subscribe to and receive real-time market data updates such as ticks, quotes, and depth information.

```python

import time

# Define your instrument(s)
instruments = [1010010002000001]        # Using numeric Instrument ID
# instruments = ["NSECM|RELIANCE"]     # Or use symbol format

# Define callback function to handle incoming data
def my_callback_function(data):
    print("New tick data received:", data)

# Set callback
client.on_message = my_callback_function

# Establish WebSocket connection
client.connect_ws()
print("WebSocket connection established.")

# Subscribe to live market data
client.subscribe_market_data(instruments)

# Unsubscribe (if needed)
# client.unsubscribe_market_data(instruments)

# Keep the script running to continue receiving ticks
while True:
    time.sleep(1)

```

### WebSocket Methods:
- **connect_ws()**: Starts the WebSocket connection.
- **stop_websocket()**: Stops the WebSocket connection.
- **subscribe_market_data(instrument_ids)**: Subsbscribes to live market data for given instruments.
- **unsubscribe_market_data(instrument_ids)**: Unsubscribes from the market data.


---

## Advanced Usage

### Reconnection Logic:
The MarketDataWebSocketClienthas automatic reconnection logic, which attempts to reconnect when the connection is lost.

### Callbacks:
The SDK allows developers to define custom callback functions for handling various WebSocket events. These callbacks enable you to control how real-time data, errors, and connection closures are managed within your application.

- **on_message**: Triggered whenever a new WebSocket message (e.g., tick or market update) is received.
Developers can assign a custom function to process or display incoming data.


- **on_error**: Invoked when an error occurs during the WebSocket connection or message processing.
This callback can be used for logging, reconnection logic, or error notifications.

- **on_close**: Called when the WebSocket connection is closed, either intentionally or due to a network issue.
Use this callback to perform cleanup tasks or attempt reconnection.

For more Info visit [callbackimplementation](callbackimplementation)

---


## Error Handling

The SDK includes error handling mechanisms:
- **Network errors**: Retries on WebSocket disconnections.
- **API errors**: Logs HTTP status codes and error messages.
- **Invalid data**: Logs detailed error messages when parsing issues occur.

---

## Troubleshooting

- **Connection Issues**: Ensure  API key is valid and that connected to the internet.
- **No Data Received**: Check if subscribing to the correct instrument IDs and verify the WebSocket connection is active.

---
