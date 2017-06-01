# Module Reference Docs

## Authentication
Authentication services are an aggregate of SDK. 
### Register
Method signature: `sdk.register(SignUpRequest, SignUpResponse)`

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

### Create Session
Method signature: `sdk.createSession(entityId, partnerId, DeviceProfile, RezolveSession)`

`entity_id` is a string, returned from sdk.register

`partner_id` is a string, returned from sdk.register

#### deviceProfile Object

|field|format|example|
|---|---|---|
|device_id|string|bd17391f9561|
|make|string|apple|
|os_type|string|iOS|
|os_version|string|10.3|
|locale|locale as a combination of ISO 639-1 language code and ISO 3166-1 country code|en_GB|

### logout

Method signature: `sdk.logout(entityId)`

`entity_id` is a string, returned from sdk.register

## ProfileService

Profile services are an aggregate of Session.

### get

### update

## FavoriteService
### create
### update
### delete
### getAll
### get

## AddressbookService
### create
### update
### delete
### getAll
### get

## WalletService
### create
### update
### delete
### getAll
### get

## Shop
### getMerchants
### getCatalogs
### getCatalog
### getProducts
### getProduct

## Activity
### getOrders

## Checkout
### checkoutOrder
### buyOrder
### signOrderUpdates

## API Check
### checkVersion
### getStatus
### checkUpgrade