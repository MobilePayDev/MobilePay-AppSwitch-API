# Capture Amount Interface Description
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Capture Amount Interface Description](#capture-amount-interface-description)
	- [Interface Identity](#interface-identity)
	- [Resources](#resources)
		- [Reservation- and Capture Types](#reservation-and-capture-types)
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
The **Capture Amount** REST interface is used to capture previously reserved amounts.
<hr>

## Resources

| Http Verb | Url Template                                                   | Description                                                                                                                                                                                                                                                                                                                                                 |
|:----------|:---------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PUT       | `/api/v1/reservations/merchants/{merchantId}/orders/{orderId}` | Captures the transaction, i.e. carries out the actual payment. <br><br>It is important to know which capture type to use, since it must match the reservation type. See Reservation- and Capture Types for further information. |

### Reservation- and Capture Types

*   Partial
    *   A partial reservation means that the amount is reserved, but a later capture can be the exact amount reserved __or less__.
    *   If the reservation type is __partial__ then the capture type must also be __partial__.
*   Full
    *   A full reservation means that the amount reserved must also be the amount captured. __It is not possible to capture less than the reserved amount__.
    *   If the reservation type if __full__, then the capture type must be __full__.

<hr>

## Data Types and Constants

### Input Fields
The following fields are part of the request URL:

| Field      | Description                                                                                                   |
|:-----------|:--------------------------------------------------------------------------------------------------------------|
| merchantId | Alphanumeric value consisting the string "APP", a country code, and 10 digits. For example "APPDK0123456789". |
| orderId    | Alphanumeric value consisting of 4 to 50 characters.                                                          |

Additionally the request requires content with the following fields:

| Field   | Mandatory | Description                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------|:----------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BulkRef | No        | A reference for bulking payments on the merchants account statement.                                                                                                                                                                                                                                                                                                                                                                        |
| Amount  | No        | Reservation ([Reservation- and Capture Types](#reservation-and-capture-types)):<br><ul><li>Partial</li><ul><li>Amount must be > 0.0 and <= reserved amount</li><li>If Amount is less than the reserved amount, then that amount is captured. The remaining amount is then released, i.e. not reserved any more.</li></ul><li>Full</li><ul><li>If the reservation type is __Full__ then only the full amount can be captured.</li></ul></ul> |

#### Examples of Request Content

```
{
    "BulkRef" : "123456789",
    "Amount" : 700.00
}
```
```
{
    "Amount" : 700.00
}
```
```
{
}
```
### Responses
The response contains the following field:

| Field         | Description                                                                                                              |
|:--------------|:-------------------------------------------------------------------------------------------------------------------------|
| TransactionId | This is the transaction ID of the payment transaction. It is the same transaction id returned when reserving the amount. |

#### Examples
```
{
    "TransactionId" : "61872634691623746"
}
```
<hr>

## Error Handling
If an error occurs then one of the following http status codes will be returned:

| Status Code               | Reasons                                                                                                                                                                                                                                                                                                                                                                               | Example response content                                                                                                                                 |
|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request           | <ul><li>InvalidOrderId</li><li>InvalidMerchantId</li><li>PartialCaptureNotPossible</li><ul><li>When trying to partially capture an amount that has been reserved fully.</li></ul><li>InvalidAmount</li><ul><li>Amount is not specified or has an invalid value.</li></ul><li>InvalidTestModeHeader</li><ul><li>If Test-mode is set to anything else but true or false.</li></ul></ul> | `{ "Reason" : "InvalidMerchantId" }`                                                                                                                     |
| 404 Not Found             | <ul><li>MerchantNotFound</li><li>OrderNotFound</li></ul>                                                                                                                                                                                                                                                                                                                              | `{ "Reason" : "MerchantNotFound" }`                                                                                                                      |
| 408 Request Timeout       | One or more of the underlying services did not respond in time. This should be investigated by MobilePay team.                                                                                                                                                                                                                                                                         | `<empty>`                                                                                                                                                |
| 409 Conflict              | <ul><li>TransactionAlreadyCaptured</li><li>TransactionAlreadyCancelled</li><li>Failed</li><ul><li>The call has failed due to reasons that can not be disclosed to the merchant, e.g. the daily limit has been reached, or the credit card has been revoked.</li></ul></ul>                                                                                                            | `{ "Reason" : "DuplicateOrderId" }`                                                                                                                      |
| 500 Internal Server Error | An error happened and should be investigated by MobilePay team.                                                                                                                                                                                                                                                                                                                          | <code>{<br>"CorrelationId":"a658ab24-70ab-4d05-a792-e5995f237c10",<br>"Errortype":"ServerError",<br>"Message":"Attempted to divide by zero."<br>}</code> |
<hr>

## Variability
It is possible to invoke the service in test-mode in production. This will not affect any real production data.
Add the header

    Test-mode: true

to the request to invoke the service in test-mode.
<hr>

## Usage Guide
The following is a request for a capture of 200.00 DKK.

[cUrl](https://curl.haxx.se/) is a tool to make http requests from the command line.

	curl -X PUT --header "Content-Type: application/json" --header "Accept: application/json" -d "{\"Amount\": 200.00}" "http://localhost:62554/api/v1/reservations/APPDK0074110008/orders/DB%20TESTING%202015060908
