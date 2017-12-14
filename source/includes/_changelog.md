# Changelog

All notable changes to this project will be documented in this log.

## December 13, 2017 

### Added

- none

### Changed
- Product Scan, Cart Flow example - updated code samples to reflect chnges in CheckoutManager, rezolveLocation object
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

