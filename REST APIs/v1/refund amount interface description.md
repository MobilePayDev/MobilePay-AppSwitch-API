# Refund Amount Interface Description
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Refund Amount Interface Description](#refund-amount-interface-description)
	- [Interface Identity](#interface-identity)
	- [Resources](#resources)
	- [Data Types and Constants](#data-types-and-constants)
		- [Input Fields](#input-fields)
			- [Examples of Request Content](#examples-of-request-content)
		- [Responses](#responses)
			- [Examples](#examples)
	- [Error Handling](#error-handling)
	- [Variability](#variability)
	- [Usage Guide](#usage-guide)

<!-- /TOC -->

## Interface Identity
The __Refund Amount__ REST interface is used to refund money, either in part or the whole amount.
<hr>

## Resources

| Http Verb | Url Template                                      | Description                                                                            |
|:----------|:--------------------------------------------------|:---------------------------------------------------------------------------------------|
| PUT       | `/api/v1/merchants/{merchantId}/orders/{orderId}` | Refunds the transaction amount, either the entire amount or just a part of the amount. |
<hr>

## Data Types and Constants

### Input Fields

| Field      | Description                                                                                                   |
|:-----------|:--------------------------------------------------------------------------------------------------------------|
| merchantId | Alphanumeric value consisting the string "APP", a country code, and 10 digits. For example "APPDK0123456789". |
| orderId    | Alphanumeric value consisting of 4 to 50 characters.                                                          |

Additionally the request requires content with the following fields:

| Field   | Mandatory | Description                                                                      |
|:--------|:----------|:---------------------------------------------------------------------------------|
| Amount  | No        | The amount to refund. If not specified, then the entire amount will be refunded. |
| BulkRef | No        | A reference for bulking payments on the merchants account statement.             |

#### Examples of Request Content

```
{
    "Amount" : 100.00,
    "BulkRef" : "123456789"
}
```
```
{
    "Amount" : 100.00
}
```
```
{
}
```

### Responses

| Field                 | Description                                                                        |
|:----------------------|:-----------------------------------------------------------------------------------|
| TransactionId         | The transaction id of the new refund payment.                                      |
| OriginalTransactionId | The id of the transaction which is now being (partially) refunded.                 |
| Remainder             | The remaining amount. If the transaction is completely refunded then this is 0.00. |

#### Examples

```
{
    "TransactionId" : "61872634691623757",
    "OriginalTransactionId" : "61872634691623746",
    "Remainder" : 20.00
}
```
<hr>

## Error Handling
If an error occurs then one of the following http status codes will be returned:

| Status Code               | Reasons                                                                                                                                                                                                                                                                                                                                                                                                                                                              	 | Example response content                                                                                                                                                               |
|:--------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request           | <ul><li>InvalidMerchantId</li><li>InvalidOrderId</li><li>InvalidAmount</li><ul><li>Most often the error is due to insufficient funds on the AppSwitch account.</li></ul></ul>                                                                                                                                                                                                                                                                                                                                                                                      | `{ "Reason" : "InvalidMerchantId" }`                                                                                                                                                   |
| 404 Not Found             | <ul><li>MerchantNotFound</li><li>OrderNotFound (If payment doesn't exist or is older than 90 days)</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                              | `{ "Reason" : "MerchantNotFound" }`                                                                                                                                                    |
| 409 Conflict              | <ul><li>TransactionAlreadyRefunded</li><li>Failed</li><ul><li>The call has failed due to reasons that can not be disclosed to the merchant, e.g. the daily limit has been reached, or the credit card has been revoked.</li></ul><li>AmountNotAvailable</li><ul><li>The specified amount cannot be refunded.</li></ul><li>RefundNotPossible</li><ul><li>It is not possible to refund the amount, as no bank account is registered on MobilePay user.</li></ul></ul>   | `{ "Reason" : "Failed" }`                                                                                                                                                              |
| 408 Request Timeout       | One or more of the underlying services did not respond in time. This should be investigated by MobilePay team.                                                                                                                                                                                                                                                                                                                                                           | <empty>                                                                                                                                                                                |
| 500 Internal Server Error | An error happened and should be investigated by MobilePay team.                                                                                                                                                                                                                                                                                                                                                                                                          | <code>{<br>&nbsp;"CorrelationId":"a658ab24-70ab-4d05-a792-e5995f237c10",<br>&nbsp;&nbsp;"Errortype":"ServerError",<br>&nbsp;&nbsp;"Message":"Attempted to divide by zero."<br>}</code> |
<hr>

## Variability
It is possible to invoke the service in test-mode in production. This will not affect any real production data.
Add the header

    Test-mode: true

to the request to invoke the service in test-mode.
<hr>

## Usage Guide
The following example refunds 20 DKK to a customer.

[cUrl](https://curl.haxx.se/) is a tool to make http requests from the command line.
```
curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" -d "{
  \"Amount\": 20.00
}" "http://localhost:62554/api/v1/merchants/APPDK0074110008/orders/DB%20TESTING%202015060908"
```
