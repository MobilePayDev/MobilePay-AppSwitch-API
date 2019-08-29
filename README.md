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

### How to handle errors



