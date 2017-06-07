# Module Reference Docs

## Module: Authentication
Authentication services are an aggregate of SDK.

This module handles consumer user creation and authentication.

### Method: Register

```objective_c
let request: AuthenticationRequest = authenticationRequest()
// TODO populate authenticationRequest object

let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: .Development)

sdk.registerUser(authenticationRequest: request) {  (entityId, partnerId) in 
	// persist the entity_id and partner_id values here
}
```
```java
DeviceProfile deviceProfile = new DeviceProfile(deviceId, deviceManufacturer, locale);
SignUpRequest signUpRequest = new SignUpRequest.Builder()
    .email("john.doe@domain.com")
    .firstName("John")
    .lastName("Doe")
    .name("JDoe")
    .device(deviceProfile)
    .build();

RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION).registerUser(signUpRequest, 
new RezolveInterface() {
    @Override
    public void onInitializationSuccess(RezolveSession rezolveSession, String partnerId, 
    String entityId) {
    	// persist the entity_id and partner_id values here
        ...

        // set session for convenience
        mySession = rezolveSession;
    }

    @Override
    public void onInitializationFailure() {
    	// handle failure here
    }
});

```

Method signature: `sdk.register( SignUpRequest, [callback or interface] )`

You must pass in a valid `signUpRequest` object.

The method returns a `signUpResponse` object.

#### signUpRequest Object

|field|format|example|
|---|---|---|
|email|string|user@domain.com|
|first_name|string|John|
|last_name|string|Doe|
|deviceProfile|object|&nbsp;|

#### deviceProfile Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|device_id|string|bd17391f9561|
|make|string|apple|
|os_type|string|iOS|
|os_version|string|10.3|
|locale|locale as a combination of ISO 639-1 language code and ISO 3166-1 country code|en_GB|

#### signUpResponse Object
<aside class="warning">
Note, the values in the SignUpResponse should be persisted at least for the life of the install. Persisting on the partner server is recommended, as this allows for Rezolve account reassociation after an uninstall/reinstall.
</aside>

|field|format|example|
|---|---|---|
|partner_id|string|rezolve|
|entity_id|string|9310c880695c|

### Method: Create Session

``` objective_c
let request: AuthenticationRequest = authenticationRequest()
// TODO populate authenticationRequest object

let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: .Development)

sdk.createSession(authenticationRequest: request) { (session: RezolveSession) in
	// use created session to access managers
    // example: session.CustomerProfileService.get
}
```
```java
RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION).createSession(entityId, 
partnerId, deviceProfile, new RezolveInterface() {
    @Override
    public void onInitializationSuccess(RezolveSession rezolveSession, String s, 
    String s1) {
        mySession = rezolveSession;
        // use created session to access managers
    }

    @Override
    public void onInitializationFailure() {
        // handle failed initialization
    }
});
```

Method signature: `sdk.createSession( entityId, partnerId, DeviceProfile, [callback or interface] )`

`entity_id` is a string, returned from `sdk.register`

`partner_id` is a string, returned from `sdk.register`

`deviceProfile` is an object (see definition above)

The method returns a `rezolveSession` object.

#### rezolveSession Object

|field|format|example|
|---|---|---|
|partner_id|string|rezolve|
|entity_id|string|9310c880695c|
|deviceProfile|object| |

### Method: Logout

``` objective_c
// When session ends you should inform the sdk by calling
session.authenticationService.logout();

```
```java
// When session ends you should inform the sdk by calling
rezolveSession.getAuthenticationManager().logout(entityId);
```

Method signature: `sdk.logout( entityId )`

`entity_id` is a string, returned from `sdk.register`.







## Module: ProfileService

The ProfileService is an aggregate of Session.

This module handles maintaining the consumer's contact information and device info.

### Method: get
Method signature: `session.customerProfileService.get( [callback or interface] )`

No parameters are required to be passed in. It will always fetch the profile for the authenticated consumer.

The method returns a `customerProfile` object.

#### customerProfile Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|entity_id|string|9310c880695c|
|title|string|Mr.|
|first_name|string|John|
|last_name|string|Doe|
|email|string|user@domain.com|
|timezone|string|Europe/London|
|date_created|long (unix timestamp)|1492805827|
|date_updated|long (unix timestamp)|1492805827|
|devices|array of deviceProfile objects||

#### deviceProfile Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|device_id|string|bd17391f9561|
|make|string|apple|
|os_type|string|iOS|
|os_version|string|10.3|
|locale|locale as a combination of ISO 639-1 language code and ISO 3166-1 country code|en_GB|

### Method: update

Method signature: `session.customerProfileService.update( customerProfile, [callback or interface] )`

The profile update method works only for the updating of consumer personal data. Device profiles and date stamps are not editable.

A full `customerProfile` object must be supplied.

The modified `customerProfile` object is returned upon success.







## Module: AddressbookService

The AddressbookService is an aggregate of Session.

This module provides CRUD functions for consumer addresses. The consumer may add one or more addresses to their account.

### Method: create

Method signature: `session.addressbookService.create( address, [callback or interface] )`

You must pass in a valid `address` object with the `id` field blank.

The method returns the created `address` object with `id` upon success.

#### address Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|short_name|string|My house|
|line1|string|Theodore Lowe|
|line2|string|Ap #867-859 Sit Rd.|
|city|string|New York|
|state|string|NY|
|zip|string|39531|
|country|string|USA|

### Method: update

Method signature: `session.addressbookService.update( address, [callback or interface] )`

You must pass in a valid `address` object with `id` populated.

The method returns the updated `address` object upon success.

### Method: delete

Method signature: `session.addressbookService.delete( id, [callback or interface] )`

You must pass in the `id` of the `address` to delete.

### Method: get

Method signature: `session.addressbookService.get( id, [callback or interface] )`

You must pass in the `id` of the `address` to get.

The method returns an `address` object.

### Method: getAll

Method signature: `session.addressbookService.getAll( [callback or interface] )`

The method returns an array of `address` objects.







## Module: FavoriteService

The FavoriteService is an aggregate of Session.

This module provides CRUD functions for Favorites. The term "Favorite" refers to the target of a top up operation, in which money is added to a remote account associated with a device or service. The consumer may add one or more favorites to their account.

### Method: create

Method signature: `session.favouriteService.create( [callback or interface] )`

You must pass in a `favourite` object with the `id` field blank.

The method returns the created `favourite` object with `id` upon success.

#### favourite Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|new_id_123|
|value|string|549912341234|
|type|string|phone|
|provider|string|AT&T|

### Method: update

Method signature: `session.favouriteService.update( favourite, [callback or interface] )`

You must pass in a valid `favourite` object with `id` populated.

The method returns the updated `favourite` object upon success.

### Method: delete

Method signature: `session.favouriteService.delete( id, [callback or interface] )`

You must pass in the `id` of the `favourite` to delete.

### Method: get

Method signature: `session.favouriteService.get( id, [callback or interface] )`

You must pass in the `id` of the `favourite` to get.

The method returns an `favourite` object.

### Method: getAll

Method signature: `session.favouriteService.getAll( [callback or interface] )`

The method returns an array of `favourite` objects.







## Module: WalletService
The WalletService is an aggregate of Session.

This module provides CRUD functions for payment cards.  The consumer may add one or more payment cards to their account.

### Method: create

Method signature: `session.walletService.create( [callback or interface] )`

You must pass in a `paymentCard` object with the `id` field blank.

The SDK will encrypt the PAN (payment account number) using the session public key before transmission.

The method returns the created `paymentCard` object with `id` upon success.

#### paymentCard Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123|
|short_name|string|black amex|
|pan|string|gWkxi0kiMJnEagRpm5dXmggWOrblj9+wsasm8Ysjp|
|pan6|string|123456|
|pan4|string|1234|
|brand|string|visa|
|type|string|debit|
|name_on_card|string|John Curtis|
|expires_on|string|08/17|
|valid_from|string|08/14|
|address_id|string|123|

### Method: update

Method signature: `session.walletService.update( paymentCard, [callback or interface] )`

You must pass in a valid `paymentCard` object with `id` populated.

The method returns the updated `paymentCard` object upon success.

### Method: delete

Method signature: `session.walletService.delete( id, [callback or interface] )`

You must pass in the `id` of the `paymentCard` to delete.

### Method: get

Method signature: `session.walletService.get( id, [callback or interface] )`

You must pass in the `id` of the `paymentCard` to get.

The method returns a `paymentCard` object.

### Method: getAll

Method signature: `session.walletService.getAll( [callback or interface] )`

The method returns an array of `paymentCard` objects.






## Module: Shop

Shop is an aggregate of Session.

The Shop module offers methods specific to e-commerce; getting catalogs, products, and creating orders.

### Method: getMerchants

Method signature: `session.getMerchants( [callback or interface] )`

The method returns an array of `merchant` objects.

#### merchant Object

|field|format|example|
|---|---|---|
|id|string|brookstone|
|title|string|Brookstone|
|logo_url|string|http://domain.com/path/logo.jpg|
|image_url|string|http://domain.com/path/image.jpg|

Recommended use of images: the image_url is used as a header background, and the logo is overlayed on top.

### Method: getCatalogs

Method signature: `session.getCatalogs( merchant_id, [callback or interface] )`

You must pass in the `id` of the `merchant` to whose catalogs you wish to get.

The method returns an array of `catalog` objects owned by a specific merchant.

#### catalog Object

|field|format|example|
|---|---|---|
|id|string|2|
|parent_id|integer|1|
|title||Fitness Wear|
|has_products|boolean|true|
|has_catalogs|boolean|false|
|image_url|string|http://domain.com/path/image.jpg|
|catlogs|array of catalog objects| |

Notes:

If `has_catalogs` is `false`, the `catalogs` array should be empty.

If `has_products` is `true`, call `getProducts` with the category `id` to get a product list.

### Method: getCatalog

Method signature: `session.getCatalog( merchant_id, catalog_id, [callback or interface] )`

The method returns a `catalog` object.

### Method: getProducts

Method signature: `session.getProducts( merchant_id, catalog_id, pageNavigation, [callback or interface] )`

You must pass a `merchant_id`, `catalog_id`, and a `pageNavivation` object. 

The method returns a `pageResult` object.

#### pageNavigation Object

The pageNavigation object controls pagignation and sorting of paginated results. 

|field|format|example|explanation|
|---|---|---|---|
|count|integer|10|number of items per page|
|pageIndex|integer|1|what page of results you are requesting|
|sortBy|string|productName|must be a field of the pageResult > SDKEntity object array (see below)|
|sort|enum|one of: ASC, DESC|

#### pageResult Object

pageResult is a generic object used for combining pagination info with an array of returned objects. In the case of getProducts, this is an array of products.

|field|format|example|explanation|
|---|---|---|---|
|links|object| |object containing First, Next, Previous, Last links|
|count|integer|10|items per page, as requested in pageNavigation.count|
|total|integer|50|total items in SDKEntity array|
|SDKEntity|array| |array of Product objects|

#### links Object

The links object is a series of 5 pageNavigation objects (see above), each representing a particular pagination navigation goal:

|field|format|explanation|
|---|---|---|
|current|pageNavigation object|The current page of results|
|first|pageNavigation object|The first page of results|
|last|pageNavigation object|The last page of results|
|prev|pageNavigation object|The previous page of results. If you are on the first page, this should be blank|
|next|pageNavigation object|The next page of results. If you are on the last page, this should be blank.|

#### product Object

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|merchant_id|string|brookstone|
|title|string|Fitbit Charge 2|
|subtitle|string|Heart Rate and Fitness Wristband|
|price|decimal|129.00|
|images|array of image urls|http://domain.com/path/image.jpg|
|options|object||
|optionAvailable|array of Variant objects||

#### option Object

An option represents a choice. For example, Color or Size on a clothing item.

|field|format|example|
|---|---|---|
|label|string||
|extraInfo|string displayed to the user, provides additional info about the option|Color may differ from shown.|
|values|array of optionValue objects||

#### optionValue Object
OptionValue objects are key/value pairs, each describing one choice within an option. For example, if the option is Color, you might have three optionValue objects, one for the red option, one for blue, and one for green.

|field|format|example|
|---|---|---|
|label|string|Red|
|value|string|C001|

#### variant Object

> Example data for a product with three sizes, two colors, two patterns:

```json
"option_available": [
	{ "size": "SI01", "color": "C001", "pattern": "P001" },
	{ "size": "SI02", "color": "C001", "pattern": "P001" },
	{ "size": "SI03", "color": "C001", "pattern": "P001" },
	{ "size": "SI01", "color": "C002", "pattern": "P001" },
	{ "size": "SI02", "color": "C002", "pattern": "P001" },
	{ "size": "SI03", "color": "C002", "pattern": "P001" },
	{ "size": "SI01", "color": "C001", "pattern": "P002" },
	{ "size": "SI02", "color": "C001", "pattern": "P002" },
	{ "size": "SI03", "color": "C001", "pattern": "P002" },
	{ "size": "SI01", "color": "C002", "pattern": "P002" },
	{ "size": "SI02", "color": "C002", "pattern": "P002" },
	{ "size": "SI03", "color": "C002", "pattern": "P002" },
]
```

A variant represents a unique combination of  option values for a product. For example, if the product is a shirt, one variant might be Small, Red, Striped. There would be additional variants for each size/color/pattern combo.

A variant has a varying number of key/value string pairs, as determined by the number of options in a product. A product with three options will have three key/value pairs in it's variants.

|field|format|example|
|---|---|---|
|label|string|Size|
|value|string|SI01|

### Method: getProduct

Method signature: `session.getProduct( merchant_id, catalog_id, product_id, [callback or interface] )`

You must pass a `merchant_id`, `catalog_id`, and a `product_id`.

The method returns a <a href="#product-object">`product object`</a> .






## Module: Activity

Activity is an aggregate of Session.

The activity module handles order history and other historical data.

### Method: getOrders

Method signature: `session.getOrders( [callback or interface] )`

The method returns an array of `transaction objects`.

#### transaction Object

|field|format|example|
|---|---|---|
|id|string|80695c|
|payWith|string|123123123|
|type|string|scan|
|status|string|processing|
|timestamp|long (unix timestamp)|1493130773|
|finalPrice|decimal|129.00|
|deliveryAddressId|String|123|
|pan4|string|4111|
|geoLoc|object||
|items|array of transactionItem objects||

#### geoLoc Object

|field|format|example|
|---|---|---|
|lat|double|51.5237737|
|long|double|-0.1585369|

#### transactionItem object

|field|format|example|
|---|---|---|
|id|string|38389992|
|price|decimal|129.00|
|qty|decimal|1|
|title|string|Fitbit Charge 2|
|imageUrl|string|http://domain.com/path/image.jpg|








## Module: Checkout

Checkout is an aggregate of Session.

The checkout module creates orders and completes orders with payment.

### Method: checkoutOrder

CheckoutOrder creates an order id and calculates the cost of the order.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### cart Object

|field|format|example|
|---|---|---|
|merchantId|string||
|loyaltySettings|object||
|deliverySettings|object||
|type|string||
|products|array of checkoutProduct objects||
|geoLoc|Object||

#### loyaltySettings Object

|field|format|example|
|---|---|---|
|loyalty_id|string|123123|

#### deliverySettings Object

|field|format|example|
|---|---|---|
|delivery_address_id|string|14|

#### checkoutProduct Object

|field|format|example|
|---|---|---|
|productId|string|12345c|
|productTitle|string|Fitbit Charge 2|
|quantity|decimal|1|
|finalPrice|decimal|159.00|
|options|map of OptionValue objects||

See <a href="#optionvalue-object">`optionValue object`</a>.

#### geoLoc Object

|field|format|example|
|---|---|---|
|lat|double|51.5237737|
|long|double|-0.1585369|

#### order Object

|field|format|example|
|---|---|---|
|id|string|123|
|finalPrice|decimal|129.00|
|breakdown|array of priceBreakdown objects||

#### priceBreakdown Object

>Example breakdown object:

```json
  "breakdown": [
    { "type": "main", "amount": 129.00  },
    { "type": "shipping", "amount": 10.00  },
    { "type": "tax", "amount": 10.00  },
    { "type": "option_variant", "amount": 10.00  },
  ]
```

|field|format|example|
|---|---|---|
|type|string||
|amount|decimal||

### Method: createPaymentRequest

Creates an encrypted paymentRequest object to be used with buyOrder.

Method signature: `session.createPaymentRequest( paymentCard, cvv, [callback or interface] )`

You must pass in a <a href="#paymentcard-object">`paymentCard object`</a> and a `cvv` string.

#### paymentRequest object

|field|format|example|
|---|---|---|
|paymentCard|object|(encrypted)|
|cvv|string|(encrypted)|

### Method: buyOrder

BuyOrder references an order id and supplies payment for the order.

Method signature: `session.buyOrder( paymentRequest, order, [callback or interface] )`

You must pass in a <a href="#paymentrequest-object">`paymentRequest object`</a>, and the <a href="#order-object">`order object`</a> you want to provide payment for.

The method returns a <a href="#transaction-object">`transaction object`</a>.

### Method: signOrderUpdates

In environments where transactions can take a very long time (minutes to tens of minutes), this method provides a way to subscribe and be notified of transaction success.

Method signature: `session.buyOrder( entity_id, order_id, [callback or interface] )`

This method is reserved for future functionality.







## Module: API Check

API Check is an aggregate of SDK.

The *API Check* module assists the developer with app lifecycle management.

This module is reserved for future functionality.

### Method: checkVersion

Reserved for future functionality.

### Method: getStatus

Reserved for future functionality.

### Method: checkUpgrade

Reserved for future functionality.




