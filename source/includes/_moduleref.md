# Module Reference Docs

## Module: Authentication
Authentication services are an aggregate of SDK.

This module handles consumer user creation and authentication.

### Method: Register
Method signature: `sdk.register( SignUpRequest )`

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
Method signature: `sdk.createSession( entityId, partnerId, DeviceProfile )`

`entity_id` is a string, returned from `sdk.register`

`partner_id` is a string, returned from `sdk.register`

The method returns a `rezolveSession` object.

#### deviceProfile Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|device_id|string|bd17391f9561|
|make|string|apple|
|os_type|string|iOS|
|os_version|string|10.3|
|locale|locale as a combination of ISO 639-1 language code and ISO 3166-1 country code|en_GB|

#### rezolveSession Object

|field|format|example|
|---|---|---|
|partner_id|string|rezolve|
|entity_id|string|9310c880695c|
|deviceProfile|object| |

### Method: Logout

Method signature: `sdk.logout( entityId )`

`entity_id` is a string, returned from `sdk.register`.







## Module: ProfileService

The ProfileService is an aggregate of Session.

This module handles maintaining the consumer's contact information and device info.

### Method: get
Method signature: `session.customerProfileService.get()`

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
|date_created|unix timestamp integer|1492805827|
|date_updated|unix timestamp integer|1492805827|
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

Method signature: `session.customerProfileService.update( customerProfile )`

The profile update method works only for the updating of consumer personal data. Device profiles and date stamps are not editable.

A full `customerProfile` object must be supplied.

The modified `customerProfile` object is returned upon success.







## Module: AddressbookService

The AddressbookService is an aggregate of Session.

This module provides CRUD functions for consumer addresses. The consumer may add one or more addresses to their account.

### Method: create

Method signature: `session.addressbookService.create( address )`

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

Method signature: `session.addressbookService.update( address )`

You must pass in a valid `address` object with `id` populated.

The method returns the updated `address` object upon success.

### Method: delete

Method signature: `session.addressbookService.delete( id )`

You must pass in the `id` of the `address` to delete.

### Method: get

Method signature: `session.addressbookService.get( id )`

You must pass in the `id` of the `address` to get.

The method returns an `address` object.

### Method: getAll

Method signature: `session.addressbookService.getAll()`

The method returns an array of `address` objects.







## Module: FavoriteService

The FavoriteService is an aggregate of Session.

This module provides CRUD functions for Favorites. The term "Favorite" refers to the target of a top up operation, in which money is added to a remote account associated with a device or service. The consumer may add one or more favorites to their account.

### Method: create

Method signature: `session.favouriteService.create()`

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

Method signature: `session.favouriteService.update( favourite )`

You must pass in a valid `favourite` object with `id` populated.

The method returns the updated `favourite` object upon success.

### Method: delete

Method signature: `session.favouriteService.delete( id )`

You must pass in the `id` of the `favourite` to delete.

### Method: get

Method signature: `session.favouriteService.get( id )`

You must pass in the `id` of the `favourite` to get.

The method returns an `favourite` object.

### Method: getAll

Method signature: `session.favouriteService.getAll()`

The method returns an array of `favourite` objects.







## Module: WalletService
The WalletService is an aggregate of Session.

This module provides CRUD functions for payment cards.  The consumer may add one or more payment cards to their account.

### Method: create

Method signature: `session.walletService.create()`

You must pass in a `paymentCard` object with the `id` field blank.

TODO describe how to encrypt PAN

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

Method signature: `session.walletService.update( paymentCard )`

You must pass in a valid `paymentCard` object with `id` populated.

The method returns the updated `paymentCard` object upon success.

### Method: delete

Method signature: `session.walletService.delete( id )`

You must pass in the `id` of the `paymentCard` to delete.

### Method: get

Method signature: `session.walletService.get( id )`

You must pass in the `id` of the `paymentCard` to get.

The method returns a `paymentCard` object.

### Method: getAll

Method signature: `session.walletService.getAll()`

The method returns an array of `paymentCard` objects.






## Module: Shop

Shop is an aggregate of Session.

The Shop module offers methods specific to e-commerce; getting catalogs, products, and creating orders.

### Method: getMerchants
### Method: getCatalogs
### Method: getCatalog
### Method: getProducts
### Method: getProduct

## Module: Activity
### Method: getOrders

## Module: Checkout
### Method: checkoutOrder
### Method: buyOrder
### Method: signOrderUpdates

## Module: API Check
### Method: checkVersion
### Method: getStatus
### Method: checkUpgrade