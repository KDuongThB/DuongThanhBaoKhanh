# Problem 1

## Task

Task
Given the following file, write a CLI command to complete the objective:

Objective: submit a HTTP GET request to ```https://example.com/api/:order_id``` with Order IDs that are selling ```TSLA```, writing to output to ```./output.txt```

File: ```./transaction-log.txt```

```json
{"order_id": "12345", "symbol": "AAPL", "quantity": 100, "price": 142.50, "side": "buy", "timestamp": "2025-02-18T09:15:30Z"}
{"order_id": "12346", "symbol": "TSLA", "quantity": 50, "price": 890.15, "side": "sell", "timestamp": "2025-02-18T09:16:10Z"}
{"order_id": "12347", "symbol": "GOOG", "quantity": 10, "price": 2850.60, "side": "buy", "timestamp": "2025-02-18T09:17:20Z"}
{"order_id": "12348", "symbol": "AMZN", "quantity": 200, "price": 3201.45, "side": "sell", "timestamp": "2025-02-18T09:18:05Z"}
{"order_id": "12349", "symbol": "SPY", "quantity": 300, "price": 411.20, "side": "buy", "timestamp": "2025-02-18T09:19:15Z"}
{"order_id": "12350", "symbol": "MSFT", "quantity": 150, "price": 305.75, "side": "sell", "timestamp": "2025-02-18T09:20:00Z"}
{"order_id": "12351", "symbol": "NVDA", "quantity": 60, "price": 215.40, "side": "buy", "timestamp": "2025-02-18T09:20:45Z"}
{"order_id": "12352", "symbol": "NFLX", "quantity": 25, "price": 650.30, "side": "sell", "timestamp": "2025-02-18T09:21:25Z"}
{"order_id": "12353", "symbol": "BTCUSD", "quantity": 0.5, "price": 27000.00, "side": "buy", "timestamp": "2025-02-18T09:22:10Z"}
{"order_id": "12354", "symbol": "ETHUSD", "quantity": 2, "price": 1900.30, "side": "sell", "timestamp": "2025-02-18T09:23:00Z"}
{"order_id": "12355", "symbol": "XRPUSD", "quantity": 1000, "price": 0.85, "side": "buy", "timestamp": "2025-02-18T09:23:40Z"}
{"order_id": "12356", "symbol": "BABA", "quantity": 120, "price": 145.75, "side": "sell", "timestamp": "2025-02-18T09:24:25Z"}
{"order_id": "12357", "symbol": "SPY", "quantity": 50, "price": 413.50, "side": "buy", "timestamp": "2025-02-18T09:25:15Z"}
{"order_id": "12358", "symbol": "AAPL", "quantity": 30, "price": 141.10, "side": "sell", "timestamp": "2025-02-18T09:26:00Z"}
{"order_id": "12359", "symbol": "GOOG", "quantity": 75, "price": 2840.40, "side": "buy", "timestamp": "2025-02-18T09:26:45Z"}
{"order_id": "12360", "symbol": "AMZN", "quantity": 50, "price": 3215.60, "side": "sell", "timestamp": "2025-02-18T09:27:30Z"}
{"order_id": "12361", "symbol": "MSFT", "quantity": 180, "price": 307.10, "side": "buy", "timestamp": "2025-02-18T09:28:05Z"}
{"order_id": "12362", "symbol": "TSLA", "quantity": 60, "price": 885.10, "side": "sell", "timestamp": "2025-02-18T09:28:50Z"}
{"order_id": "12363", "symbol": "NVDA", "quantity": 120, "price": 220.80, "side": "buy", "timestamp": "2025-02-18T09:29:30Z"}
{"order_id": "12364", "symbol": "NFLX", "quantity": 40, "price": 648.50, "side": "sell", "timestamp": "2025-02-18T09:30:00Z"}
```

## Answer

- if we have ```jq``` and ```curl``` installed:

  ```bash
  grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt | jq -r '.order_id' | xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt
  ```

## Explanation for my answer

- use ```grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt``` to filter lines that contain orders that are selling ```TSLA```
- pipe to ```jq .order_id``` to extract values of key 'order-id' from the result of ```grep```
- pipe to ```xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt``` to call a HTTP request to <https://example.com/api/:order_id> to with each value of ```order_id``` extracted (```xargs -I {}``` replace ```{}``` in the API endpoint with each ```order_id```) and write output to ```output.txt```
- if you replace ```curl -s``` with ```echo```, the command would be:

  ```bash
  grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt | jq .order_id | xargs -I {} echo "https://example.com/api/{}" >> ./urls.txt
  ```

  and each line of the content of ```urls.txt``` will be an API endpoint respective to each ```order_id``` that would be called if you used ```curl -s```

- Since ```https://example.com``` doesn't have that endpoint, it will return an error code 404 (Not found).

## Alternatives

- since ```jq``` and ```curl``` are not preinstalled on Ubuntu 24.04

  - we can replace ```jq .order_id``` with ```awk -F'"' '{for(i=1;i<=NF;i++) if($i=="order_id") print $(i+2)}'```
  - replace ```curl -s``` with ```sh -c 'printf "GET /api/{} HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n" | nc example.com 80' >> ./output.txt``` to call the same API with ```netcat```, which results in the following command:

    ```bash
    grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt | awk -F'"' '{for(i=1;i<=NF;i++) if($i=="order_id") print $(i+2)}' | xargs -I {} sh -c 'printf "GET /api/{} HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n" | nc example.com 80' >> ./output.txt
    ```
