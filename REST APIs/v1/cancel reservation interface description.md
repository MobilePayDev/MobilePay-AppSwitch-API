# Cancel Reservation Interface Description
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Cancel Reservation Interface Description](#cancel-reservation-interface-description)
	- [Interface Identity](#interface-identity)
	- [Resources](#resources)
	- [Data Types and Constants](#data-types-and-constants)
		- [Input Fields](#input-fields)
		- [Responses](#responses)
			- [Examples](#examples)
	- [Error Handling](#error-handling)
	- [Variability](#variability)
	- [Usage Guide](#usage-guide)

<!-- /TOC -->

## Interface Identity
The **Cancel Reservation** REST interface is used to cancel reservations previously made by a merchant.
<hr>

## Resources

| Http Verb | Url Template                                                   | Description                           |
|:----------|:---------------------------------------------------------------|:--------------------------------------|
| DELETE    | `/api/v1/reservations/merchants/{merchantId}/orders/{orderId}` | Cancels previously made reservations. |
<hr>

## Data Types and Constants

### Input Fields
The following fields are part of the request URL

| Field      | Description                                                                                                   |
|:-----------|:--------------------------------------------------------------------------------------------------------------|
| merchantId | Alphanumeric value consisting the string "APP", a country code, and 10 digits. For example "APPDK0123456789". |
| orderId    | Alphanumeric value consisting of 4 to 50 characters.                                                          |

### Responses

| Field         | Description                                           |
|:--------------|:------------------------------------------------------|
| TransactionId | Transaction id of the original reservation / payment. |

#### Examples

    {
        "TransactionId" : "61872634691623746"
    }

<hr>

## Error Handling
If an error occurs then one of the following http status codes will be returned:

| Status Code               | Reasons                                                                                                     | Example response content                                                                                                                                 |
|:--------------------------|:------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request           | - InvalidMerchantId<br>- InvalidOrderId                                                                     | `{ "Reason" : "InvalidMerchantId" }`                                                                                                                     |
| 404 Not Found             | - MerchantNotFound<br>- OrderNotFound<br>- ReservationNotFound                                              | `{ "Reason" : "MerchantNotFound" }`                                                                                                                      |
| 409 Conflict              | - TransactionAlreadyCaptured<br>- TransactionAlreadyCancelled,                                              | `{ "Reason" : "TransactionAlreadyCaptured" }`                                                                                                            |
| 408 Request Timeout       | One or more of the underlying services did not respond in time. This should be investigated by MobilePay team. | `<empty>`                                                                                                                                                |
| 500 Internal Server Error | An error happened and should be investigated by MobilePay team.                                                | <code>{<br>"CorrelationId":"a658ab24-70ab-4d05-a792-e5995f237c10",<br>"Errortype":"ServerError",<br>"Message":"Attempted to divide by zero."<br>}</code> |
<hr>

## Variability
It is possible to invoke the service in test-mode in production. This will not affect any real production data.
Add the header

    Test-mode: true

to the request to invoke the service in test-mode.
<hr>

## Usage Guide
The following example cancels a reservation previously made by the merchant.

[cUrl](https://curl.haxx.se/) is a tool to make http requests from the command line.

    curl -X DELETE --header "Accept: application/json" "http://localhost:62554/api/v1/reservations/APPDK0074110008/orders/DB%20TESTING%202015060908"
