# 99Tech DevOps Challenge
My submission for the 99Tech DevOps Challenge.

Link to my CV:

## Problem 1
### Answer:
```bash
grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt | jq -r '.order_id' | xargs -I {} curl -s "https://example.com/api/:{}" >> ./output.txt
```
### Explanation for my answer:
- use ```grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt``` to filter lines that contain orders that are selling ```TSLA```
- pipe (```|```) to ```jq -r '.order_id``` to extract value of key 'order-id' from the result of ```grep```
- pipe to ```xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt``` to call a HTTP request to https://example.com/api/order_id to with each value of ```order_id``` extracted (```xargs -I {}``` replace ```{}``` in the API endpoint with each ```order_id```) and write output to ```output.txt``` 
- if you replace ```curl -s``` with ```echo```, the command would be :
  ```
  grep '"symbol": "TSLA", "quantity": [0-9]*, "price": [0-9.]*, "side": "sell"' ./transaction-log.txt | jq .order_id | xargs -I {} echo "https://example.com/api/:{}" >> ./endpoints.txt
  ```
  and each line of the content of ```endpoints.txt``` will be an API endpoint respective to each ```order_id``` that would be called if you used ```curl -s```