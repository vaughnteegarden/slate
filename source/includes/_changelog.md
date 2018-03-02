# Changelog

All notable changes to this project will be documented in this log.

## Android v1.6.0, IOS v1.6.0 - February 28 2018

### Added

### Changed
- Prodct Scan, Instant Buy Flow - updated code samples for CheckoutManager changes
- Prodct Scan, Cart Flow - updated code samples for CheckoutManager changes
- Mall flow - update getMerchants method, and add context to getProducts params
- Instancing the SDK via `RezolveSDK.getInstance` used to take an enum to specify environment. This is now takes a string.  `RezolveSDK.getInstance(String API_KEY, String ENVIRONMENT)`. For the Sandbox, ENVIRONMENT should equal `https://sandbox-api-tw.rzlvtest.co`. Changed in many code samples.
- AuthenticationManager.register and .logout methods have been deprecated.
- Android CheckoutManager - the methods .addProductToCart, .buyProduct, .checkoutProduct, .removeProductFromCart, and .updateProductInCart now require Android context as their first parameter.
- ProductManager - change format of getMerchants to require an implementation of ProductCallback as a parameter. Add "this" context as first parameter to .getCategory, .getProducts, and .getProduct.


### Deleted
- The enum to represent the target Rezolve environment RezolveSDK.Env.EvironmentName is removed

## Android v1.5.37, IOS v1.5.40 - Dec 19 2017

### Added

- PhonebookManager Class

### Changed
- Product Scan, Cart Flow example - updated code samples to reflect changes in CheckoutManager, rezolveLocation object
- Product Scan, Cart Flow example - updated sequence diagram and narrative to better describe role of signOrderUpdates call.
- Product Scan, Instant Buy flow example - updated code samples to reflect chnges in CheckoutManager, rezolveLocation object
- Product Scan, Instant Buy flow example - updated sequence diagram and narrative to better describe role of signOrderUpdates call.
- CheckoutManager.buyCart - added phonebookId to method parameters. Removed "Type" from RezolveLocation object.
- CheckoutManager.buyProduct - added phonebookId to method parameters. Removed "Type" from RezolveLocation object.
- CheckoutManager.signOrderUpdates - remove from response:
    - Transaction.transactionAmount (Order object)
    - Transaction.transactionItems list
    - remove Transaction.Timestamp from response
- CheckoutManager.signOrderUpdates - clarified narrative describing socket listener behavior.
- ProductManager.getProduct - change method name from product.getOptionAvailable() product.getOptionsAvailable()  (plural)
- UserActivityManager.getOrders request - add "from" and "to" date parameters to request parameters
- UserActivityManager.getOrders response - orderDetails.getItems() now returns a list of OrderProduct objects, instead of HistoryItem objects
- UserActivityManager.getOrders response - remove from the OrderDetails object: 
    - Type
    - RezolveLocation.Type
- UserActivityManager.getOrders response - rename object FinalPrice to Price
- UserActivityManager.getOrders response - add to the OrderDetails object:
    - Partner Id
    - Billing Address
    - Shipping Address
    - Email
    - First Name
    - Last Name
    - Phone
    - Merchant Email
    - Merchant Name
    - Merchant Phone



### Deleted

- none

