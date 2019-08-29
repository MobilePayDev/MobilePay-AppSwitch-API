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

### Best practice payment flow

1 - Customer wants to make an order and chooses to pay with MobilePay.

4 - Merchant App calls AppSwitch SDK (reservation).


### How to handle errors

- The merchant app can crash or be closed by the customer and when it is restarted, the pending order should be fetched with current status from the MobilePay backend. The customer should be able to finish the order when the merchant app is restarted. Alternatively the reservation should be cancelled.
- The payment flow can be interrupted either forced by the customer using the back or home button or MobilePay can be closed after swipe.
If the customer sees the receipt confirmation screen in MobilePay, the user might believe that the order is completed, but that will not be the case if interrupted payment flows are not handled in the merchant app and backend.




