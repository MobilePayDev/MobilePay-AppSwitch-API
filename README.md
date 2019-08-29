# MobilePay-AppSwitch API
Technical documentation on how to interact with the MobilePay AppSwitch API.

_Please note that the SOAP API is not available for new merchants. Example code regarding PKI factory and SOAP library can be obtained by contacting [developer@mobilepay.dk](mailto://developer@mobilepay.dk)._

## REST API

### Recommendations

- Check out MobilePay AppSwitch documentation on github.
- Make sure you generate unique order ID’s, otherwise calling Refund endpoint can cause trouble.
- Make sure you do polling on payment status on your backend and do not rely on getting the response from MobilePay through the SDK.
If your backend get a status ‘Reserved’ – the user has swiped and the order can be completed and payment can be captured though the MobilePay AppSwitch API.
- Use Get Reservations endpoint to identify unhandled reservations and cancel the reservations by calling the Cancel endpoint.
- Make sure you do backend validation of the reservation details from the app, e.g. the amount.
- Testing must be done in production with real money, however you can use a test-mode of the MobilePay endpoints to verify the connection and your code.
- In the merchant implementation there are two options:
  1. Capture the amount immediately before the product is delivered.
  1. Deliver the product immediately before the product is delivered. 

### Sunshine payment flow

1. Customer wants to make an order and chooses to pay with MobilePay in the merchant app.
1. The merchant backend receives information about the order and generates an unique order id.
1. The backend starts polling status of the order from the MobilePay AppSwitch API by calling Get Status endpoint.
1. The merchant app calls the AppSwitch SDK in order to process the payment (reservation).
1. The customer logs in to MobilePay and swipes the reservation.
1. The merchant backend will receive a "Reserved" status.
1. The merchant backend completes the order and captures the amount and sends order completed event to the merchant app.

### How to handle errors

- The merchant app can crash or be closed by the customer and when it is restarted, the pending order should be fetched with current status from the MobilePay backend. The customer should be able to finish the order when the merchant app is restarted. Alternatively the reservation should be cancelled.
- The payment flow can be interrupted either forced by the customer using the back or home button or MobilePay can be closed after swipe.
If the customer sees the receipt confirmation screen in MobilePay, the user might believe that the order is completed, but that will not be the case if interrupted payment flows are not handled in the merchant app and backend.
- All the endpoints from the MobilePay API can in rare cases return Http response 500 even through the transaction has completed successfully. Best practice is to call get status endpoint after cancel, refund or capture in order to check if it was successfull. If e.g. the Capture endpoint is called again it will reply with Http response 409 and reason code 'Already captured'.
- A retry mechanism should be implemented to handle the case, where the endpoint replies with Http response 500.
- Http response code 409 and reason code 'Already captured', 'Already cancelled' or 'Already refunded' should be considered as equal to 200 OK.
- If the payment should be cancelled after a 500 Http response have been received on the capture, the following should be done (because it is not certain what has happened with the payment):
   1. Call Get Status endpoint.
   1. If the status is 'Captured', call Refund endpoint to transfer money back to the customer.
   1. If the status is 'Reserved', call Cancel endpoint to cancel the reservation.



