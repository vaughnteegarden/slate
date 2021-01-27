# Changelog

All notable changes to the project will be documented in this log.

## Android 3.1.0 - Jan 27, 2021

- Added AdditionalAttribute list to Order
- Added GeozoneNotificationCallback and stop using BroadcastReceiver to pass to app Display action of notification.
- Added Cloneable to CheckoutProduct
- Added Cloneable to ConfigurableOption and CustomConfigurableOption
- Added engagementId to ScannedData
- Added EventReportInterface
- Added PartnerSettings to EventReportManager
- Added engagementId and engagementName into SspObject
- Added common elements into SspObject.entityToJson implementation
- Added getMerchantId method to SspObject
- Added form builder models.
- Added CurrencyInformation to Merchant
- Added currencyInformation fields getters
- Added user fields (name, location, phone, email) to SspActSubmission
- Added ACCESS_BACKGROUND_LOCATION permission to ssp-google-geofence-detector module
- Added serializable implementation to CheckoutProduct and its fields
- Added check for empty first or last name to CustomerProfileManager.update call
- Added custom payload to OrderDetails
- Added new constructor for (Custom?|ConfigurableOption)
- Added Resolvable.isExclusive()
- Added new enum Resolvable.LOCAL_EXCLUSIVE and Resolvable.SERVER_EXCLUSIVE. In exclusive mode, only the first matching resolver will process the payload.
- Added a ProGuard configuration in the SDK to ensure that app modules that depend on the SDK do not have to manually update their ProGuard files to use our library.
- Added ENTITY_DOESNT_EXIST error type to RezolveError class
- Change: AuthParams class for SSP authentication now requires two extra params: engagement endpoint and act endpoint
- Change: SspActManager now requires SspHttpClient instead of HttpClient.
- Change: LocationProviderFused now only require single argument (context).
- Change: renamed ScanHelper.isBarcodeScan to ScanHelper.is1DBarcodeScan to differentiate from QR scan.
- Change: Allow creating SspActQuestions for PageBuildingBlocks.
- Change: change name of EventReportInterface to SendEventReportCallback
- Change: Set EventReport date when the event is created.
- Fixed crash when an opaque URI is passed to ScannedData.parse() method
- Removed setPartnerId and setDateTime, setOS from EventReport.Builder
- Removed Product references from Order
- Removed geozone notification if detection is handled by any of the callbacks

## Android 3.0.0 - Sept 2, 2020

- Changed compatible Android Scan module version to 3.0.0

## Android 3.0.0 - July 1, 2020

- Added StoreDetails object to OrderDetails.
- Added SSP module.
- Added guest-login module.
- Added Geozone detection
- RezolveSDK divided into separate modules: core, payment and scan.
- Changed AudioDetectService, now sends merchant_id and is_act flag for product response.
- Changed to improve visibility for javadocs and variables after importing the sdk.
- Changed ScanManagerInterface, no longer extends ErrorInterface. All scan errors will be returned in onScanError callback that now includes ScannedData.
- Changed AudioDetectService intent_extra_merchant_name, changed to intent_extra_merchant_id.
- Changed onProductResult from ScanManagerInterface and TriggerInterface to add categoryId to returned data
- Changed StoreDetails 'pickupStore' field name to 'pickupStoreId'.
- Changed permission requirements: Only one of "android.permission.CAMERA" and "android.permission.RECORD_AUDIO" is now required to initialize ScanManager.
- Changed ScanManager, now requires BarcodeMode enum instead of boolean to set the how the manager should deal with barcodes.


## IOS 2.0.4.1 - May 15, 2020

- Added Geofence support
- Added SSP Manager integration
- Added tests for SSP module
- Added support for SSP products
- Complete refactor of the SDK utilizing new Codable protocol and Unit Test implementation
- Updated to `XCode 11.4`
- Updated with merged Baidu Geofence framework with internal Geofence algorithm
- Updated unit tests for Geofencing methods


## IOS 1.11.31 - January 28, 2020

- Update to invoke `TriggerManager` automatically on 1D Barcode detection.

## IOS 1.11.30 - January 23, 2020

- Supressed consecutive `Background Listening` notifications every time the SDK is sent to the background.
- Migration to `Xcode 11.3.1`.
- Added `EAN13` Symbology for improved 1D Barcode detection.


## Android 2.3.1, IOS 1.11.28 - November 21, 2019

Cumulative release of several minor version updates.

### Android Changes

- Implement logic in Android SDK to Support 1D Barcode Scanning
- Fix QR code scanning currently recognizes QR code as 1d barcode
- Add a ProGuard configuration in the SDK to ensure that app modules that depend on the SDK do not have to manually update their ProGuard files to use Rezolve library.

### IOS Changes

- Updated `AuthenticationManager` public initializer.
- Migration to `Xcode 11.2` and Swift `Swift 5.1.2`.


## Android 2.2.0 - October 10, 2019

This release is Android only.

### Android Changes

- StoreDetails class added to OrderDetails. 
- Added isVirtual field to PriceOption
- Added equals, hashCode implementation to PriceOption
- Added doxygen comments for RezolveLocation, StoreAddress and StoreTime.
- isDefault field added to ShippingMethod. Added doxygen comments. 
- Constructors of ShippingMethod and ExtensionAttribute are now private. Added missing toString(), equals(Object o) and hashCode() methods for these classes.
- Add logic to support override settings of resolver based on app flavour 
- Implement logic to support the new status for order "pre-canceled" which sits between "pending" and "cancelled"/"complete"
- On failed scan, return ScannedData in onScanError callback, if available.
- Added new RezolveError.
- Added flag to CustomerProfile
- Change to avoid reusing ScanManager when requested manager has a different params.
- Fix extra's name for AudioDetectService. 
- Add merchant_id to product response.
- Added documentation of Merchant class
- Added documentation of MerchantManager class
- Added documentation of Order class
- Added documentation of OrderHistoryObject
- onProductResult from ScanManagerInterface and TriggerInterface now return categoryId
- Allow ScanManager initialization when only one of required permissions is given
- Pass information if scanned product is an ACT or not.



## IOS 1.11.26 - September 24, 2019

This release is IOS only.

- Compiled SDK with latest Xcode 11 & Swift 5.1.
- Removed the need for having the new Mac Catalyst feature enabled

### IOS Changes

- Compiled SDK with latest Xcode 11 & Swift 5.1
- Removed the need for having the new Mac Catalyst feature enabled

## Android v2.1.0, IOS v1.11.25 - June 26, 2019

### Documentation Changes

- Added tutorial for Search
- New install instructions for IOS SDK (CocoaPods)
- Updated module reference docs for both platforms

### IOS Changes

- Added new method in Product Manager, `getAdditionalAttributes` 
- Added new method in Product Manager, allowing searching for products based on various criteria.
- Added new method in Merchant Manager, allowing searching for merchants based on various criteria.
- Added new, optional parameter fullName to Address object.
- Added distance property to Merchant's model.
- Added new property PriceOption to Product object.
- Added option to suspend and resume the process of audio/video capturing during scan.
- Added explicit handling of Act products with new is_act parameter on Product model.
- Added new full_name parameter on Address model to dinstinguish from address_2.
- Added phone_book_id parameter on Address model to allow for uniue Phone Number on each Address model.
- Added getRootCategory endpoint, gets the root category for the selected merchant
- Added getCartProduct added for fetching product information when there is no category
- Added getMerchants endpoint, added optional parameter to fetch only active Merchants, represented as Bool<br/><br/>
- Updated getProducts
- Updated PageResult's sorting direction now uses enum instead of string.
- Updated getCategories endpoint 
- Misc bug fixes & small optimizations

### Android Changes

- Added hashCode, equals implementation for CustomOption, OptionValue
- Added toString implementation to CustomOption, OptionValue models
- Added HttpClientConfig to RezolveSdk.Builder
- Added missing interface to DisplayProduct.
- Added default page navigation filter.
- Added product and category ids.
- Added a getter for scanned objects.
- Added ScanManagerHelper.
- Added .equal() methods for Placement and CheckoutProduct.
- Added hashCode methods for CheckoutProduct and Placement.
- Added missing flags to DisplayProduct.
- Added equals and hashcode methods added for ConfigurableOption and CustomConfigurableOption.
- Add visibility flag to the /merchant call.
- Added CustomAttribute class.
- Added Multiple Exception types catching in HttpClient.enqueueRequest to reduce code duplication
- Added equals, hashCode and toString methods for PaymentCard.
- Added proguard rule for MerchantFilter
- Added merchants search method.
- Added MerchantSearchResult and DisplayStore models.
- Added required fields to DisplayProduct and Merchant. Use SearchMerchantResult and SearchProductResult as wrappers. 
- Added TermsAndCondtitions.
- Added merchant_id as a param to SearchData.
- Added ProductSearchData and MerchantSearchData.
- Added toString, eqals and hashcode methods to new and modified classes. 
- Added ProductSearchInterface and MerchantSearchInterface.
- Added "total" field to search responses. 
- Added "itemsPerPage" to SearchData.
- Added support for too large quantity error
- Added missing return statement in Links.jsonToEntity
- Added factory method to create PageNavigationFilter from Link object
- Added getOkHttpClient to HttpClient
- Added distance field to Merchant<br/><br/>
- Changed /options endpoint to v4
- Fixed rder details under my Activity shows QR for Delivery orders
- Changed /category endpoint used by ScanManager to v2.
- Changed item limit back to 30.
- Changed product to be parcelable
- Changed torch support methods to reside in another class.
- Changed all interfaces to extend error interface.
- Changed scan logic to move away from ScanManager.
- Changed image reader to reside in ScanImageHelper class.
- Changed scan audio logic moved to ScanAudioHelper
- Changed RezolveScanResult modification to prevent returning to the user if the engagement has expired.
- Changed clear cache thread priority to background. Make Credentials fields final.
- Changed so allowed to add interceptors to HttpClientConfig.
- Changed OrderDetails status to enum.
- Changed; if scanned image is a QR code, check if it belongs to rezolve.
- Change type of returned scan errors.
- Fixed return error type for scan error.
- Changed new 'include_cart_button_on_listing' flag to be handled by Android SDK
- Changed cart buttons flag by moving to Category.
- Fixed query param name.
- Changed CustomAttribute to AdditionalAttribute
- Fixed bug in HttpClient.enqueueRequest method that could crash the app.
- Changed HttpResponse implementation to throw catched exceptions.
- Changed MerchantFilter enum name to MerchantVisibility.
- Changed override onSearchMerchantsSuccess in MerchantCallback
- Changed do not set param to all when product type is null. This option is not supported yet on the - server side.
- Changed SearchOrderBy by replacing with MerchantSearchOrderBy, ProductSearchOrderBy
- Changed search data param include_in_result -> include_in_results
- Fixed hashCode, equals implementation in (Merchant|Product)SearchData
- Fixed bug in DisplayProduct.jsonToEntity
- Changed include_in_results param to have lowercase values (all, products, acts)
- Changed Link[] to Links object with named fields<br/><br/>
- Deprecated old getMerchants method. 
- Deprecate old /category methods.







## Android v2.0.1, IOS v1.11.20 - Nov 21, 2018

### Documentation Changes

- Added tutorial on how to present data for an Act.
- New install instructions for IOS SDK (CocoaPods)
- New install instructions for Android SDK (Nexus/Maven)

### IOS Changes

- `HistoryItemProduct` implementation changed
- Scanmanager internal libraries updated
- `CheckoutBundleV2.deliveryMethod` changed type to `DeliveryMethod?`
- `CheckoutBundleV2.paymentMethod` changed type to `SupportedPaymentMethod?`
- `createProductCheckoutBundleV2` changed parameter type from `deliveryMethod` to `DeliveryMethod`
- `createProductCheckoutBundleV2` changed parameter type from `paymentMethod` to `SupportedPaymentMethod?`
- `createCartCheckoutBundleV2` changed parameter type from `deliveryMethod` to `DeliveryMethod`
- `createCartCheckoutBundleV2` changed parameter type from `paymentMethod` to `SupportedPaymentMethod?`

### Android Changes

 - Added abort purchase method to cancel orders without cleaning the cart on QuickPass canceling.
 - Fixed wrong base price with certain options





## Android v2.0.0, IOS v1.11.10 - October 11, 2018

### Major Documentation Changes

- Added tutorial for Trigger Manager
- Added tutorial for Background Listening
- Added tutorial for Click & Collect
- Removed manually created module reference docs and replaced them with module reference docs generated from source. 

### IOS

#### Added

- Support for virtual products
- `x-rezolve-device-id` for all Core calls
- `authorizationCallback` in DataClient so we can intercept when 401 is thrown by the server
- `PaymentOptionManager`
- `CheckoutManagerV2`
- `CheckoutBundleV2`

#### Changed

- License file
- Updated `deviceId` payload key for login calls
- New `paymentOption` attribute on `CheckoutBundle`
- `CheckoutBundle` helper functions have a new parameter: `paymentOption`
- New attribute `orderId` on Order entity
- `CheckoutManager.checkout(bundle:callback:errorCalback:)` is now supporting `paymentOption`
- `CheckoutManager.buy(bundle:paymentRequest:location:phone:callback:errorCallback:)` is now supporting `paymentOption`
- Updated `CheckoutOrder` object with new properties
- Updated scan resolver
- Fixed deserialisation method from history transaction.
- Fixed `CartManager.removeCartProduct(merchantId:cart:product:callback:errorCallback:)` removal of products cotaining options or ACT
- Fixed `CartManager`: The payment_option key removed from the payload
- Fixed `InStorePickupManager`: The `payment_option` key removed from the payload
- Fixed On createSession 401 weren't dispatching properly error to app handle it.
- Fixed `PaymentOptionManager.getProductOptions(checkoutProduct:merchantId:callback:errorCallback:)` updated JSON sent to server
- Fixed Regression serialisation on `CustomOption.swift`
- `getScanManager` now accept username, password and env as parameter
- Deprecated `ProductManager.getProductsAndCategories`, use `ProductManager.getProductsAndCategoriesV2` instead.
- Deprecagted `ProductManager.getCategories` use `ProductManager.getPaginatedCategories` instead
- Breaking change: `Merchant.contactInformation` type changed from tuple to class
- Breaking change: Order have a new property orderId
- Breaking change: `HistoryTransaction` model updated
- Breaking change: Change type of "value" member on `CustomOption` from `Any?` to `[String]`
- Breaking change: `PaymentOptionManager.getProductOptions(checkoutProduct:merchantId:callback:errorCallback:)` updated JSON sent to server


#### Deleted

- Removed Websocket support from SDK
- Custom Option Array Fix.


### Android

#### Added 

- List of `CustomConfigurableOptions` added to `OrderProduct` class.
- `CustomOption` fields for user input.
- `getOrdersV2` method in `UserActivityManager`
- `NotificationHelper` class.
- `TriggerManager` class.
- `isVirtual` flag for Product.
- `PaymentOptionManager`
- `CheckoutManagerV2`
- `CheckoutBundleV2`
- `DeliveryMethod`
- Rezolve Auto Detect Service to listen for ads in backgorund
- Credit Cards may have the number fully edited by users
- Auto Torch for the scan screen. If the device has a light sensor the scan screen is able to turn on the flash light automatically to help on capture process.
- Rezolve Auto Detect Service to listen for ads in backgorund
- Credit Cards may have the number fully edited by users
- Read custom resolver url from shared preferences.

#### Changed

- `CustomConfigurableOptions` added to `CheckoutProduct`.
- Fixed parsing methods for shipping details.
- `CustomOption` optionId is now an integer.
- Fixed a bug on `CustomOption` validator that prevented the validation of dates.
- Fixed QR codes scan.
- Fixed the location sent on a buy if the user didn't allow location on the app.
- Fixed several improvements and bugfixes on the video and audio scan process.
- New LICENSE.TXT file with updated use terms.
- Fixed a bug where starting the Rezolve Auto Detect Service before login could crash the SDK.
- Fixed an issue that could crash the SDK if a credit card data was updated without changes on the credit card number.
- Fixed `StoreAddress` model.
- Fixed issue when `ScanManager` torch methods were not responsive.









## Android v1.7.5, IOS v1.7.6.1 - July 9, 2018

Note: This update only changes the IOS version. 

### Changed

- Updated IOS version to be compatible with IOS version 9 and onward, instead of 10 and onward
- Updated IOS to be compatible with latest releases of SWIFT (4.x) and XCode (9.x)

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

