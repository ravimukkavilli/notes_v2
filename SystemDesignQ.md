
## Stock Broker

### Functional Reqs:
*  User can see the prices of a stock using the stock symbol or name.
*  User should be able to search a the stock by name.
*  User should be able to place an buy or sell order.
*  User should be able to track the order
*  User can place a Market/Limit order

### Non-Functional Reqs:
*  High Availability
*  High Scalability
*  Security
*  Consistency 
*  Low latency
*  Reliable - Once the Order is placed, 

For Resource
*  Total number of users: 5 million (5,000,000).
*  Active daily users: 1 million.
*  No of Writes: xxx/sec
*  No Of reads: xxx/sec

Storage Estimation:
*  
Bandwidth Estimation:


API Design:

Resources:
*  User
*  Protfolio
*  Stock
*  Order
*  Exchange

User Profile:
Get user: api/v1/users/{userId}; Method: GET
Create user: api/v1/users; Method : POST
Update user: api/v1/users/{userId} ; METHOD: PUT

STOCK:
Get Stock: api/v1/stocks/{stockId}
Get Stock Price for 1 Day: api/v1/exchanges/{exchangeId}/stocks/{stockId}/prices
```json
{
    "EXCH": "IDX",
    "START": 1721586601,
    "END": 1722537001,
    "INTERVAL": "60"
}
```



