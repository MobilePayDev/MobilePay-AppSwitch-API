# Payment Status Interface Description
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Payment Status Interface Description](#payment-status-interface-description)
	- [Interface Identity](#interface-identity)
	- [Resources](#resources)
	- [Data Types and Constants](#data-types-and-constants)
		- [Input Fields](#input-fields)
		- [Responses](#responses)
			- [Get Payment Status](#get-payment-status)
				- [Examples](#examples)
			- [Get Transactions](#get-transactions)
				- [Examples](#examples)
		- [Payment Status Codes](#payment-status-codes)
	- [Error Handling](#error-handling)
	- [Variability](#variability)
	- [Usage Guide](#usage-guide)

<!-- /TOC -->

## Interface Identity
The __Payment Status__ REST interface is used to get the status of a payment, as well as the transactions related to that payment.
<hr>

## Resources

| Http Verb | Url Template                                                   | Description                                                                    |
|:----------|:---------------------------------------------------------------|:-------------------------------------------------------------------------------|
| GET       | `/api/v1/merchants/{merchantId}/orders/{orderId}`              | Get the status for a specific payment, identified by merchant id and order id. |
| GET       | `/api/v1/merchants/{merchantId}/orders/{orderId}/transactions` | Get the transactions associated with a specific payment.                       |

<hr>

## Data Types and Constants

### Input Fields

| Field      | Description                                                                                                   |
|:-----------|:--------------------------------------------------------------------------------------------------------------|
| merchantId | Alphanumeric value consisting the string "APP", a country code, and 10 digits. For example "APPDK0123456789". |
| orderId    | Alphanumeric value consisting of 4 to 50 characters.                                                          |


### Responses

#### Get Payment Status

| Field               | Description                                                                                                                                                                 |
|:--------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LatestPaymentStatus | See [Payment Status Codes](#payment-status-codes).                                                                                                                          |
| TransactionId       | The id of the original transaction.                                                                                                                                         |
| OriginalAmount      | If latest payment status is reserved it is the amount of the reservation. If it is captured it is the captured amount. These amounts can be different for partial captures. |

##### Examples
```
{
  "LatestPaymentStatus": "Captured",
  "TransactionId": "61872634691623746",
  "OriginalAmount": 123.45
}
```

#### Get Transactions

| Field         | Description                                       |
|:--------------|:--------------------------------------------------|
| TimeStamp     | ISO8601 (UTC time) YYYY-MM-DDThh:mm:ss.fffZ       |
| PaymentStatus | See [Payment Status Codes](#payment-status-codes) |
| TransactionId | The id of the specific transaction.               |
| Amount        | Amount in the specific transaction.               |


##### Examples

```
[
  {
    "TimeStamp": "2016-04-08T07:45:36.533Z",
    "PaymentStatus": "Captured",
    "TransactionId": "61872634691623746",
    "Amount": 11.5
  },
  {
    "TimeStamp": "2016-04-09T07:45:36.672Z",
    "PaymentStatus": "Cancelled",
    "TransactionId": "61872634691623799",
    "Amount": 18.9
  }
]
```

### Payment Status Codes

| Value         | Meaning                                                       |
|:--------------|:--------------------------------------------------------------|
| Reserved      | Payment is reserved.                                          |
| Cancelled     | Reservation is cancelled by merchant.                         |
| Captured      | Payment is captured by merchant.                              |
| TotalRefund   | Total payment is refunded by merchant.                        |
| PartialRefund | Payment is partially refunded by merchant.                    |
| Rejected      | The reservation, capture, refund or cancellation is rejected. |
<hr>

## Error Handling
If an error occurs then one of the following http status codes will be returned:

| Status Code               | Reasons                                                                                                                                                           | Example response content                                                                                                                                                               |
|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request           | <ul><li>InvalidOrderId</li><li>InvalidMerchantId</li><li>InvalidTestModeHeader</li><ul><li>If Test-mode is set to anything else but true or false.</li></ul></ul> | `{ "Reason" : "InvalidOrderId" }`                                                                                                                                                      |
| 404 Not Found             | <ul><li>MerchantNotFound</li><li>OrderNotFound</li></ul>                                                                                                          | `{ "Reason" : "MerchantNotFound" }`                                                                                                                                                    |
| 408 Request Timeout       | One or more of the underlying services did not respond in time. This should be investigated by MobilePay team.                                                       | <empty>                                                                                                                                                                                |
| 429 Too Many Request | The limit of maximum requests per second is 50 request |  |
| 500 Internal Server Error | An error happened and should be investigated by MobilePay team.                                                                                                      | <code>{<br>&nbsp;"CorrelationId":"a658ab24-70ab-4d05-a792-e5995f237c10",<br>&nbsp;&nbsp;"Errortype":"ServerError",<br>&nbsp;&nbsp;"Message":"Attempted to divide by zero."<br>}</code> |
<hr>

## Variability
It is possible to invoke the service in test-mode in production. This will not affect any real production data.
Add the header

    Test-mode: true

to the request to invoke the service in test-mode.
<hr>

## Usage Guide
<ul>
<li>Request status for an order
<ul>
<li>http://localhost:62554/api/v1/merchants/APPDK0074110008/orders/DB%20TESTING%202015060908
</ul>
<li>Request all transactions for an order
<ul>
<li>http://localhost:62554/api/v1/merchants/APPDK0074110008/orders/DB%20TESTING%202015060908/transactions
</ul>
</ul>
