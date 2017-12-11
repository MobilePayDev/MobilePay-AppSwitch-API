# Get Reservations Interface Description
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Get Reservations Interface Description](#get-reservations-interface-description)
	- [Interface Identity](#interface-identity)
	- [Resources](#resources)
		- [Reservation- and Capture Types](#reservation-and-capture-types)
	- [Data Types and Constants](#data-types-and-constants)
		- [Input Fields](#input-fields)
		- [Responses](#responses)
			- [Examples](#examples)
	- [Error Handling](#error-handling)
	- [Variability](#variability)
	- [Usage Guide](#usage-guide)

<!-- /TOC -->
## Interface Identity
The __Get Reservations__ REST interface is used to get a list of reservations made by a merchant.
<hr>

## Resources

| Http Verb | Url Template                                                                                    | Description                                                                                     |
|:----------|:------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------|
| GET       | /api/v1/reservations/merchants/{merchantId}/{datetimeFrom}/{datetimeTo}?customerId={customerId} | Get a list of reservations made by a specific merchant, and optionally for a specific customer. |

### Reservation and Capture Types

*   Partial
    *   A partial reservation means that the amount is reserved, but a later capture can be the exact amount reserved __or less__.
    *   If the reservation type is __partial__ then the capture type must also be __partial__.
*   Full
    *   A full reservation means that the amount reserved must also be the amount captured. __It is not possible to capture less than the reserved amount__.
    *   If the reservation type if __full__, then the capture type must be __full__.
<hr>

## Data Types and Constants

### Input Fields

| Field        | Description                                                                                                                   |
|:-------------|:------------------------------------------------------------------------------------------------------------------------------|
| merchantId   | Alphanumeric value consisting the string "APP", a country code, and 10 digits. For example "APPDK0123456789".                 |
| customerId   | Optional: Identification of customer in MobilePay systems (phone number with prefix, e.g. +45).                               |
| datetimeFrom | Start of specific interval within which merchant wants to investigate reservations. ISO8601 (UTC time): 'YYYY-MM-DDTHH_MM'. |
| datetimeTo   | End of time interval within which merchant wants to investigate reservations. ISO8601 (UTC time): 'YYYY-MM-DDTHH_MM'.       |

### Responses
An array of transaction data, where each entry has the following fields:

| Field         | Description                                                                                                                                                                             |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TimeStamp     | ISO8601 (UTC time) YYYY-MM-DDThh:mm:ss.sss (Ex. 2017-11-01T22:41:00.000).                                                                                                             |
| OrderId       | Merchant's OrderID.                                                                                                                                                                     |
| TransactionId | The id of the transaction.                                                                                                                                                              |
| Amount        | The amount that has been reserved.                                                                                                                                                      |
| CaptureType   | The capture type can be one of the following values:<br><br><ul><li>Full</li><li>Partial</li></ul><br>See [Reservation- and Capture Types](#reservation-and-capture-types) for details. |

#### Examples
```

[
  {
    "TimeStamp": "2016-04-08T07:45:00.000",
    "OrderId": "DB TESTING 2015060908",
    "TransactionId": "61872634691623746",
    "Amount": 100.25,
    "CaptureType": "Full"
  },
  {
    "TimeStamp": "2016-04-09T07:45:00.000",
    "OrderId": "DB TESTING 2015060908",
    "TransactionId": "61872634691623799"
    "Amount": 100.25,
    "CaptureType": "Partial"
  }
]
```
<hr>

## Error Handling
If an error occurs then one of the following http status codes will be returned:

| Status Code               | Reasons                                                                                                                                                                                    | Example response content                                                                                                                                                                     |
|:--------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 Bad Request           | <ul><li>InvalidCustomerIdId</li><li>InvalidMerchantId</li><li>InvalidDate</li><li>InvalidTestModeHeader</li><ul><li>If Test-mode is set to anything else but true or false.</li></ul></ul> | `{"Reason":"InvalidMerchantId"}`                                                                                                                                                         |
| 404 Not Found             | <ul><li>MerchantNotFound</li><li>CustomerNotFound</li><li>OrderNotFound</li><ul><li>If there are no reservations within the requested timeframe.</li></ul></ul>                                                                                                                                | `{"Reason":"OrderNotFound"}`                                                                                                                                                          |
| 408 Request Timeout       | One or more of the underlying services did not respond in time. This should be investigated by MobilePay team.                                                                                | <empty>                                                                                                                                                                                      |
| 500 Internal Server Error | An error happened and should be investigated by MobilePay team.                                                                                                                               | <code>{<br>&nbsp;&nbsp;"CorrelationId":"a658ab24-70ab-4d05-a792-e5995f237c10",<br>&nbsp;&nbsp;"Errortype":"ServerError",<br>&nbsp;&nbsp;"Message":"Attempted to divide by zero."<br>}</code> |
<hr>

## Variability
It is possible to invoke the service in test-mode in production. This will not affect any real production data.
Add the header

    Test-mode: true

to the request to invoke the service in test-mode.
<hr>

## Usage Guide

<ul>
<li>Request reservations for merchant 'APPDK0074110008' in the date/time interval ['2016-02-01T12_00'..'2016-02-01T13_30']</li>
<ul>
<li>http://localhost:62554/api/v1/reservations/merchants/APPDK0074110008/2016-02-01T12_00/2016-02-01T13_30</li>
</ul>

<li>Request reservations for merchant 'APPDK0074110008' in the date/time interval ['2016-02-01T12_00'..'2016-02-01T13_30'] for customer id '+4512345678'</li>
<ul>
<li>http://localhost:62554/api/v1/reservations/merchants/APPDK0074110008/2016-02-01T12_00/2016-02-01T13_30?customerId=%2B4512345678</li>
</ul>
</ul>
