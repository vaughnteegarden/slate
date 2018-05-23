# Changelog

All notable changes to the project will be documented in this log.

## Android v1.7.5, IOS v1.6.5 - May 5, 2018

### Added

- New section on Generic JWT Authentication, with sequence diagram.
- AuthenticationManager.createSession, v2, added.
- `MerchantManager` class added
- `CheckoutBundle` class added (see CheckoutManager class for details)
- `ProductManager.getgetProductsAndCategories` method added
- `ProductManager.getParentCategory` method added (Android Only)
- `ProductManager.getCartProduct` method added (Android Only)

### Changed

- Instancing the SDK via `RezolveSDK.getInstance` used to take an enum to specify environment. This is now takes a string.  `RezolveSDK.getInstance(String API_KEY, String ENVIRONMENT)`. For the Sandbox, ENVIRONMENT should equal `sandbox-api-tw.rzlvtest.co` in IOS, and `https://sandbox-api-tw.rzlvtest.co/api` in Android. Changed in many code samples.  
- Updated all sequence diagrams for v2 authentication, using JWT.
- Added new SDK download process.
- Product Scan, Instant Buy Flow - 
	- Updated code samples for `CheckoutManager` changes.  
	- Revised sequence diagram. 
- Product Scan, Cart Flow
	- Updated code samples for `CheckoutManager` changes. 
	- Updated Category structure. 
	- Revised sequence diagram. 
- Category Scan Flow
	- Simplified calls.
	- Updated Category structure.
	- Revised sequence diagram.
- Mall flow 
	- Updated `getMerchants` method
	- Updated Category structure. Simplified subsequent calls
	- Revised sequence diagram. 
- `AuthenticationManager` class
	- `.register` deprecated
	- v1 `.createSession` deprecated
	- v1 `.logout` deprecated  
	- V2 `.createSession` has been added, using JWT accessToken, and removing deviceProfile. CreateSession change made to all sequence diagrams.
- `CheckoutManager` class
	- Added CheckoutBundle class documentation
	- Revised checkout methods and purchase methods to use CheckoutBundles
- `ProductManager` class  
	- `getCategory` now takes category object as a parameter, instead of category id. 
	- `getProduct` now takes a product object as a parameter, instead of a product id.
	- `onGetProductsSuccess` now uses `getItems` instead of `getProducts` to extract DisplayProducts. 
	- Updated `Category` objects



### Deleted

- The enum to represent the target Rezolve environment `RezolveSDK.Env.EvironmentName` is removed.
- DeviceProfile object removed from createSession. 
- Removed Merchant handling from ProductManager

## Android v1.5.37, IOS v1.5.40 - Dec 19 2017

### Added

- PhonebookManager Class
- MerchantManager Class
- ProductManager.getCartProduct 
- ProductManager.getParentCategory
- ProductManager.getProductsAndCategories



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

