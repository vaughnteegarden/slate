# Module Reference Docs

## Module: AuthenticationManager
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







## Module: ProfileManager

The ProfileManager is an aggregate of Session.

This module handles maintaining the consumer's contact information and device info.

### Method: customerProfileManager.get

``` objective_c

```
```java
// get using CustomerProfileInterface
public class Profile extends AppCompatActivity implements CustomerProfileInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CustomerProfileManager myProfileManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getCustomerProfileManager();

		List<DeviceProfile> devices = new ArrayList<DeviceProfile>();
        
        // get customer profile
        myProfileManager.get(this);
    }

    @Override
    public void onGetSuccess(CustomerProfile customerProfile) {
        // access profile info
        String dateCreated = customerProfile.getDateCreated();
        String dateUpdated = customerProfile.getDateUpdated();
        List devices = customerProfile.getDevices();
        String email = customerProfile.getEmail();
        String entityId = customerProfile.getEntityId();
        String firstName = customerProfile.getFirstName();
        String lastName = customerProfile.getLastName();
        String locale = customerProfile.getLocale();
        String name = customerProfile.getName();
        String timezone = customerProfile.getTimezone();
        String title = customerProfile.getTitle();

        // access device profile info
        String deviceId = customerProfile.getDevices().get(0).getDeviceId();
        String make = customerProfile.getDevices().get(0).getMake();
        String osType = customerProfile.getDevices().get(0).getOsType();
        Integer osVersion = customerProfile.getDevices().get(0).getOsVersion();
    }
}


```

Method signature: `session.customerProfileManager.get( [callback or interface] )`

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
|devices|array of deviceProfile objects| |

#### deviceProfile Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|device_id|string|bd17391f9561|
|make|string|apple|
|os_type|string|iOS|
|os_version|int|10.3|
|locale|locale as a combination of ISO 639-1 language code and ISO 3166-1 country code|en_GB|

### Method: customerProfileManager.update

``` objective_c

```
```java
// update using CustomerProfileInterface
public class Profile extends AppCompatActivity implements CustomerProfileInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CustomerProfileManager myProfileManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getCustomerProfileManager();


        //update customer profile
        String API_KEY = "1234567890";
        String deviceId = "62c7c7042511c086";
        String deviceManufacturer = "HTC";
        String locale = "en-US";
        DeviceProfile deviceProfile = new DeviceProfile(deviceId, deviceManufacturer, locale);
        List<DeviceProfile> devices = new ArrayList<DeviceProfile>();
        devices.add(deviceProfile);

        CustomerProfile profile = new CustomerProfile();
        profile.setDevices(devices);
        profile.setEmail("johndoe@gmail.com");
        profile.setEntityId("123");
        profile.setFirstName("John");
        profile.setLastName("Doe");
        profile.setLocale("en-US");
        profile.setName("John Doe");
        profile.setTimezone("America/New_York");
        profile.setTitle("Mr.");

        myProfileManager.update(profile, this);
    }

    @Override
    public void onUpdateSuccess(CustomerProfile customerProfile) {
        // show success message
    }

}

```

Method signature: `session.customerProfileManager.update( customerProfile, [callback or interface] )`

The profile update method works only for the updating of consumer personal data. Device profiles and date stamps are not editable.

A full `customerProfile` object must be supplied.

The modified `customerProfile` object is returned upon success.







## Module: AddressbookManager

The AddressbookManager is an aggregate of Session.

This module provides CRUD functions for consumer addresses. The consumer may add one or more addresses to their account.

### Method: addressbookManager.create

```objective_c

```
```java
// create address using addressBookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getAddressbookManager();

        // create a new address
        Address address = new Address();
        address.setShortName("Home");
        address.setCity("Boston");
        address.setCountry("US");
        address.setLine1("123 Some Street");
        address.setLine2("");
        address.setState("MA");
        address.setZip("02110");
        myAddressManager.create(address,this);

    }

    @Override
    public void onAddressbookCreateSuccess(Address address) {
        // show create success update message
    }
}
```

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

### Method: addressbookManager.update

```objective_c

```
```java
// update address using addressBookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getAddressbookManager();

        // update an address
        Address address = new Address();
        address.setShortName("Home");
        address.setCity("Boston");
        address.setCountry("US");
        address.setLine1("123 Some Street");
        address.setLine2("");
        address.setState("MA");
        address.setZip("02110");
        myAddressManager.create(address,this);
    }

    @Override
    public void onAddressbookUpdateSuccess(Address address) {
        // show update success message
    }
}
```

Method signature: `session.addressbookManager.update( address, [callback or interface] )`

You must pass in a valid `address` object with `id` populated.

The method returns the updated `address` object upon success.

### Method: addressbookManager.delete

```objective_c

```
```java
// delete address using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getAddressbookManager();

        // delete an address
        Address address = new Address();
        address.setShortName("Home");
        address.setCity("Boston");
        address.setCountry("US");
        address.setLine1("123 Some Street");
        address.setLine2("");
        address.setState("MA");
        address.setZip("02110");
        myAddressManager.delete(address,this);
    }

    @Override
    public void onAddressbookDeleteSuccess(HttpResponse httpResponse) {
        // show delete update message
    }
}
```

Method signature: `session.addressbookManager.delete( id, [callback or interface] )`

You must pass in the `address` object to delete.

### Method: addressbookManager.get

```objective_c

```
```java
// get address using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getAddressbookManager();

        // get a single address
        String address_id = "123";
        myAddressManager.get(address_id,this);
    }

    @Override
    public void onAddressbookGetSuccess(Address address) {
        // get address info
        String id = address.getId();
        String shortName = address.getShortName();
        String city = address.getCity();
        String country = address.getCountry();
        String line1 = address.getLine1();
        String line2 = address.getLine2();
        String state = address.getState();
        String zip = address.getZip();
    }
}
```

Method signature: `session.addressbookManager.get( id, [callback or interface] )`

You must pass in the `id` of the `address` to get.

The method returns an `address` object.

### Method: addressbookManager.getAll
```objective_c

```
```java
// get all addresses using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getAddressbookManager();

        // list all addresses
        myAddressManager.getAll(this);
    }

    @Override
    public void onAddressbookGetAllSuccess(List<Address> list) {
        // get address list info
        for(Address address : list) {
            String id = address.getId();
            String shortName = address.getShortName();
            String city = address.getCity();
            String country = address.getCountry();
            String line1 = address.getLine1();
            String line2 = address.getLine2();
            String state = address.getState();
            String zip = address.getZip();
        }
    }
}
```

Method signature: `session.addressbookManager.getAll( [callback or interface] )`

The method returns an array of `address` objects.







## Module: FavoriteManager

The FavoriteManager is an aggregate of Session.

This module provides CRUD functions for Favorites. The term "Favorite" refers to the target of a top up operation, in which money is added to a remote account associated with a device or service. The consumer may add one or more favorites to their account.

### Method: favouriteManager.create

```objective_c

```
```java
// create favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getFavouriteManager();

        // create a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.create(favourite,this);
    }

    @Override
    public void onFavouriteCreateSuccess(Favourite favourite) {
        // show favourite created message
    }
}
```

Method signature: `session.favouriteManager.create( [callback or interface] )`

You must pass in a `favourite` object with the `id` field blank.

The method returns the created `favourite` object with `id` upon success.

#### favourite Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|new_id_123|
|value|string|549912341234|
|type|string|phone|
|provider|string|AT&T|

### Method: favouriteManager.update

```objective_c

```
```java
// update favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getFavouriteManager();

        // update a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.update(favourite,this);
    }

    @Override
    public void onFavouriteUpdateSuccess(Favourite favourite) {
        // show favourite updated message
    }
}
```

Method signature: `session.favouriteManager.update( favourite, [callback or interface] )`

You must pass in a valid `favourite` object with `id` populated.

The method returns the updated `favourite` object upon success.

### Method: favouriteManager.delete

```objective_c

```
```java
// delete favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getFavouriteManager();

        // delete a favourite
        Favourite favourite = new Favourite();
        favourite.setProvider("AT&T");
        favourite.setType("phone");
        favourite.setValue("549912341234");
        myFavouriteManager.delete(favourite,this);
    }

    @Override
    public void onFavouriteDeleteSuccess(HttpResponse httpResponse) {
        // show favourite deleted message
    }
}
```

Method signature: `session.favouriteManager.delete( id, [callback or interface] )`

You must pass in the `favourite` object to delete.

### Method: favouriteManager.get

```objective_c

```
```java
// get a favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getFavouriteManager();

        // get a single favourite
        String favourite_id = "123";
        myFavouriteManager.get(favourite_id,this);
    }

    @Override
    public void onFavouriteGetSuccess(Favourite favourite) {
        // get favourite info
        String id = favourite.getId();
        String provider = favourite.getProvider();
        String type= favourite.getType();
        String value = favourite.getValue();
    }
}
```

Method signature: `session.favouriteManager.get( id, [callback or interface] )`

You must pass in the `id` of the `favourite` to get.

The method returns an `favourite` object.

### Method: favouriteManager.getAll

```objective_c

```
```java
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getFavouriteManager();

        // list all favourites
        myFavouriteManager.getAll(this);
    }

    @Override
    public void onFavouriteGetAllSuccess(List<Favourite> list) {
        // get favourite list info
        for(Favourite favourite : list) {
            String id = favourite.getId();
            String provider = favourite.getProvider();
            String type= favourite.getType();
            String value = favourite.getValue();
        }
    }
}
```

Method signature: `session.favouriteManager.getAll( [callback or interface] )`

The method returns an array of `favourite` objects.







## Module: WalletManager
The WalletManager is an aggregate of Session.

This module provides CRUD functions for payment cards.  The consumer may add one or more payment cards to their account.

### Method: walletManager.create

```objective_c

```
```java
// create payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getWalletManager();

        // create a new payment card
        PaymentCard myCard = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.create(myCard, this);
    }

    @Override
    public void onWalletCreateSuccess(PaymentCard paymentCard) {
        // display success message
    }
}
```

Method signature: `session.walletManager.create( [callback or interface] )`

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

### Method: walletManager.update

```objective_c

```
```java
// update payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getWalletManager();

        // update a payment card
        PaymentCard myCard2 = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.update(myCard, this);
    }

    @Override
    public void onWalletUpdateSuccess(PaymentCard paymentCard) {
        // display success message
    }
}
```

Method signature: `session.walletManager.update( paymentCard, [callback or interface] )`

You must pass in a valid `paymentCard` object with `id` populated.

The method returns the updated `paymentCard` object upon success.

### Method: walletManager.delete

```objective_c

```
```java
// delete payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getWalletManager();

        // delete a payment card
        PaymentCard myCard3 = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.delete(myCard, this);
    }

    @Override
    public void onWalletDeleteSuccess(HttpResponse httpResponse) {
        // display success message
    }
}
```

Method signature: `session.walletManager.delete( id, [callback or interface] )`

You must pass in the `paymentCard` object to delete.

### Method: walletManager.get

```objective_c

```
```java
// get a payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getWalletManager();

        // list a single payment card
        String card_id = "123";
        myWalletManager.get(card_id,this);
    }

    @Override
    public void onWalletGetSuccess(PaymentCard paymentCard) {
        // show Card info
        String cardId = paymentCard.getId();
        String addressId = paymentCard.getAddressId();
        String brand = paymentCard.getBrand();
        String expiresOn = paymentCard.getExpiresOn();
        String nameOnCard = paymentCard.getNameOnCard();
        String pan4 = paymentCard.getPan4();
        String pan6 = paymentCard.getPan6();
        String shortName = paymentCard.getShortName();
        String type = paymentCard.getType();
        String validFrom = paymentCard.getValidFrom();
    }
}
```

Method signature: `session.walletManager.get( id, [callback or interface] )`

You must pass in the `id` of the `paymentCard` to get.

The method returns a `paymentCard` object.

### Method: walletManager.getAll

```objective_c

```
```java
// get all payment cards using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getWalletManager();

        // list all payment cards
        myWalletManager.getAll(this);
    }

    @Override
    public void onWalletGetAllSuccess(List<PaymentCard> list) {
        // handle getAll response here
        for(PaymentCard paymentCard : list) {
            String cardId = paymentCard.getId();
            String addressId = paymentCard.getAddressId();
            String brand = paymentCard.getBrand();
            String expiresOn = paymentCard.getExpiresOn();
            String nameOnCard = paymentCard.getNameOnCard();
            String pan4 = paymentCard.getPan4();
            String pan6 = paymentCard.getPan6();
            String shortName = paymentCard.getShortName();
            String type = paymentCard.getType();
            String validFrom = paymentCard.getValidFrom();
        }
    }
}
```

Method signature: `session.walletManager.getAll( [callback or interface] )`

The method returns an array of `paymentCard` objects.






## Module: ProductManager

Shop is an aggregate of Session.

The Shop module offers methods specific to e-commerce; getting catalogs, products, and creating orders.

### Method: getMerchants

```objective_c

```
```java
// get merchants using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

        // get merchants
        myProductManager.getMerchants(this);
    }


    @Override
    public void onGetMerchantsSuccess(List<Merchant> list) {
        // list merchants
        for(Merchant merchant : list) {
            String merchant_id = merchant.getId();
            String name = merchant.getName();
            String tagline = merchant.getTagline();
            String banner = merchant.getBanner();
            List<String> bannerThumb = merchant.getBannerThumbs();
            List<String> logoThumbs = merchant.getLogoThumbs();
        }
    }
}
```

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

```objective_c

```
```java
// get catalogs using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

		// get catalogs
        String merchantId = "123";
        myProductManager.getCatalogs(merchantId, this);
    }

    @Override
    public void onGetCatalogsSuccess(List<Catalog> list) {
        for (Catalog catalog : list) {
            String catalog_id = catalog.getId();
            String parentId = catalog.getParentId();
            String name = catalog.getName();
            Boolean hasCatalog = catalog.hasCatalog();
            Boolean hasProduct = catalog.hasProduct();
            String image = catalog.getImage();
            List<String> imageThumbs = catalog.getImageThumbs();
        }
    }
}
```

Method signature: `session.getCatalogs( merchant_id, [callback or interface] )`

You must pass in the `id` of the `merchant` to whose catalogs you wish to get.

The method returns an array of `catalog` objects owned by a specific merchant.

#### catalog Object

|field|format|example|
|---|---|---|
|id|string|2|
|parent_id|string|1|
|name|String|Fitness Wear|
|has_products|boolean|true|
|has_catalogs|boolean|false|
|image|string|http://domain.com/path/image.jpg|
|imageThumbs|array of url strings|&nbsp;|
|catlogs|array of catalog objects|&nbsp;|

Notes:

If `has_catalogs` is `false`, the `catalogs` array should be empty.

If `has_products` is `true`, call `getProducts` with the category `id` to get a product list.

### Method: getCatalog

```objective_c

```
```java
// get a single catalog using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

        // get single catalog
        String catalogId = "123";
        String merchantId = "123";
        myProductManager.getCatalog(merchantId, catalogId, this);
    }

    @Override
    public void onGetCatalogSuccess(List<Catalog> list) {
        for (Catalog catalog : list) {
            String catalog_id = catalog.getId();
            String parentId = catalog.getParentId();
            String name = catalog.getName();
            Boolean hasCatalog = catalog.hasCatalog();
            Boolean hasProduct = catalog.hasProduct();
            String image = catalog.getImage();
            List<String> imageThumbs = catalog.getImageThumbs();
        }
    }
}
```

Method signature: `session.getCatalog( merchant_id, catalog_id, [callback or interface] )`

The method returns a `catalog` object.

### Method: getProducts

```objective_c

```
```java
// get products using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

        // get products
        Catalog catalog = new Catalog();
        Integer count = 16;
        Integer page = 1;
        String sort_by_field = "title";
        String sort_direction = "ASC";
        String merchantId3 = "123";
        String catalogId2 = "123";
        myProductManager.getProducts(merchantId3, catalogId2, count, page, 
        sort_by_field, sort_direction, this);
    }

    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
        Integer count = pageResult.getCount();
        Integer total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> embed = pageResult.getEmbedded();

        for (Link link: links){
            Integer linkcount = link.getCount();
            Integer page = link.getPage();
            String sort = link.getSort();
            String sortBy = link.getSortBy();
        }

        for (DisplayProduct displayProduct : embed){
            String id = displayProduct.getId();
            String imageUrl = displayProduct.getImageUrl();
            float price = displayProduct.getPrice();
            String title = displayProduct.getTitle();
        }
    }
}
```

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
|options|object|&nbsp;|
|optionAvailable|array of Variant objects|&nbsp;|

#### option Object

An option represents a choice. For example, Color or Size on a clothing item.

|field|format|example|
|---|---|---|
|label|string|&nbsp;|
|extraInfo|string displayed to the user, provides additional info about the option|Color may differ from shown.|
|values|array of optionValue objects|&nbsp;|

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

```objective_c

```
```java
// get a single product using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

        // get single product
        Product product2 = new Product();
        String productId = "123abc";
        String merchantId4 = "123";
        String catalogId3 = "123";
        myProductManager.getProduct(merchantId4, catalogId3, productId, this);
    }

    @Override
    public void onGetProductSuccess(com.rezolve.sdk.model.shop.Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        String description = product.getDescription();
        List<String> images = product.getImages();
        String merchant_id = product.getMerchantId();
        float price = product.getPrice();
        HashMap<String, Option> options = product.getOptions();
        List<Variant> optionsAvail = product.getOptionAvailable();

        // iterate to get values of options hashmap
        for (String key : options.keySet()) {
            String optionName = key;
            Option optionValue = options.get(key);

            String extraInfo = optionValue.getExtraInfo();
            String label = optionValue.getLabel();
            List<OptionValue> values = optionValue.getValues();

            for(OptionValue opVal: values){
                String opValLabel = opVal.getLabel();
                String opValValue = opVal.getValue();
            }
        }

        // iterate to get values of variant array
        for (Variant variant : optionsAvail) {
            HashMap<String, String> composition = variant.getCompositions();
            // iterate to get values of composition hashmap
            for (String key : composition.keySet() ){
                String compLabel = key;
                String compValue = composition.get(key);
            }
        }
    }
}
```

Method signature: `session.getProduct( merchant_id, catalog_id, product_id, [callback or interface] )`

You must pass a `merchant_id`, `catalog_id`, and a `product_id`.

The method returns a <a href="#product-object">`product object`</a> .






## Module: UserActivityManager

Activity is an aggregate of Session.

The activity module handles order history and other historical data.

### Method: getOrders

```objective_c

```
```java
// get transactions using UserActivityManager
public class Activities extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getUserActivityManager();

        // get transactions from User Activity Manager
        myActivityManager.getOrders(this);
    }

    @Override
    public void onGetOrdersSuccess(List<Transaction> list) {
        for(Transaction transaction : list){
            Map<String, String> deliverySettings = transaction.getDeliverySettings();
            float finalPrice = transaction.getFinalPrice();
            Map<String, Double> geoLoc = transaction.getGeoLoc();
            List<TransactionItem> items = transaction.getItems();
            Map<String, String> loyaltySettings = transaction.getLoyaltySettings();
            String merchant_id = transaction.getMerchantId();
            String pan4 = transaction.getPan4();
            String payWith = transaction.getPayWith();
            String status = transaction.getStatus();
            String timestamp = transaction.getTimestamp();
            String type = transaction.getType();

            for (String key : geoLoc.keySet()) {
                String label = key;
                Double value = geoLoc.get(key);
            }

            for (TransactionItem transactionItem : items) {
                transactionItem.getId();
                transactionItem.getImageUrl();
                transactionItem.getPrice();
                transactionItem.getQty();
                transactionItem.getTitle();
            }

            for (String key : loyaltySettings.keySet()) {
                String label = key;
                String value = loyaltySettings.get(key);
            }
        }
    }
}
```

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
|geoLoc|object|&nbsp;|
|items|array of transactionItem objects|&nbsp;|

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








## Module: CheckoutManager

Checkout is an aggregate of Session.

The checkout module creates orders and completes orders with payment.

### Method: checkoutOrder

```objective_c

```
```java
// create an order and calculate cart cost
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getCheckoutManager();

        // create a cart object for demo use
        // in actual use, pass an existing cart object
        HashMap<String, String> options = new HashMap<>();
        options.put("color", "red");

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        checkoutProduct.setId("123");
        checkoutProduct.setPrice(125);
        checkoutProduct.setTitle("Fitbit 2");
        checkoutProduct.setQty(5);
        checkoutProduct.setOptions(options);

        List<CheckoutProduct> items = new ArrayList<>();
        items.add(checkoutProduct);

        HashMap<String, Double> geoLoc = new HashMap<>();
        geoLoc.put("lat", 12.3123);
        geoLoc.put("long", 31.1323);

        HashMap<String, String> deliverySettings = new HashMap<>();
        deliverySettings.put("delivery_address_id", "123123");

        HashMap<String, String> loyaltySettings = new HashMap<>();
        loyaltySettings.put("loyalty_id", "123123");

        String merchantId = "123";

        Cart cart = new Cart.Builder()
                .merchantId(merchantId)
                .type("scan")
                .deliverySettings(deliverySettings)
                .loyaltySettings(loyaltySettings)
                .geoLoc(geoLoc)
                .items(items)
                .build();


        // create order and calculate cost
        myCheckoutManager.checkoutOrder(cart, this);

    }


    @Override
    public void onCheckoutOrderSuccess(Order order) {
        // get order info
        String orderId = order.getOrderId();
        float finalPrice = order.getFinalPrice();
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        //iterate price breakdown array
        for (PriceBreakdown breakdown : breakdowns ) {
            float amount = breakdown.getAmount();
            String type = breakdown.getType();
        }
    }
}
```

CheckoutOrder creates an order id and calculates the cost of the order.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### cart Object

|field|format|example|
|---|---|---|
|merchantId|string|&nbsp;|
|loyaltySettings|object|&nbsp;|
|deliverySettings|object|&nbsp;|
|type|string|&nbsp;|
|products|array of checkoutProduct objects|&nbsp;|
|geoLoc|Object|&nbsp;|

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
|options|map of OptionValue objects|&nbsp;|

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
|breakdown|array of priceBreakdown objects|&nbsp;|

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
|type|string|tax|
|amount|decimal|10.00|

### Method: createPaymentRequest

```objective_c

```
```java
PaymentCard card = new PaymentCard(); // in actual use, pass in an existing card object
String cvv = "123";
PaymentRequest paymentRequest = myCheckoutManager.createPaymentRequest(card, cvv);
```

Creates an encrypted paymentRequest object to be used with buyOrder.

Method signature: `session.createPaymentRequest( paymentCard, cvv, [callback or interface] )`

You must pass in a <a href="#paymentcard-object">`paymentCard object`</a> and a `cvv` string.

#### paymentRequest object

|field|format|example|
|---|---|---|
|paymentCard|object|(encrypted)|
|cvv|string|(encrypted)|

### Method: buyOrder

```objective_c

```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getCheckoutManager();

        // buy Order
        // first create the payment request from card and cvv
        // in actual use, pass in an existing card object
        PaymentCard card = new PaymentCard();
        String cvv = "123";
        PaymentRequest paymentRequest = myCheckoutManager.createPaymentRequest(card, cvv);
        
        // then submit the order
        // in actual use, pass in an existing order object
        Order order = new Order();
        myCheckoutManager.buyOrder(paymentRequest, order, this);
    }

    @Override
    public void onBuyOrderSuccess(Transaction transaction) {
    	// get transaction info
        Map<String, String> deliverySettings = transaction.getDeliverySettings();
        float finalPrice = transaction.getFinalPrice();
        Map<String, Double> geoLoc = transaction.getGeoLoc();
        List<TransactionItem> items = transaction.getItems();
        Map<String, String> loyaltySettings = transaction.getLoyaltySettings();
        String merchantId = transaction.getMerchantId();
        String pan4 = transaction.getPan4();
        String payWith = transaction.getPayWith();
        String status = transaction.getStatus();
        String timestamp = transaction.getTimestamp();
        String type = transaction.getType();

        // iterate to get delivery settings
        for (String key : deliverySettings.keySet()) {
            String label = key;
            String value = deliverySettings.get(key);
        }
        // iterate to get loyalty settings
        for (String key : loyaltySettings.keySet()) {
            String label = key;
            String value = loyaltySettings.get(key);
        }
        // iterate to get transaction items
        for (TransactionItem item : items){
            String itemId = item.getId();
            String itemTitle = item.getTitle();
            String qty = item.getQty();
            String price = item.getPrice();
            String imageUrl = item.getImageUrl();
        }
    }
}
```

BuyOrder references an order id and supplies payment for the order.

Method signature: `session.buyOrder( paymentRequest, order, [callback or interface] )`

You must pass in a <a href="#paymentrequest-object">`paymentRequest object`</a>, and the <a href="#order-object">`order object`</a> you want to provide payment for.

The method returns a <a href="#transaction-object">`transaction object`</a>.

### Method: signOrderUpdates

In environments where transactions can take a very long time (minutes to tens of minutes) to return a status, this method provides a way to subscribe and be notified of transaction status updates.

Method signature: `session.buyOrder( entity_id, order_id, [callback or interface] )`

This method is reserved for future functionality.







## Module: ScanManager

```java
//
// contents of scan_activity.xml
// 

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ScanActivity"
    android:padding="10dp">

	//this is the video scan UI component
    <com.rezolve.sdk.views.RezolveScanView
        android:id="@+id/scan_view"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:layout_centerHorizontal="true"
        android:layout_below="@id/start_video_scan">
    </com.rezolve.sdk.views.RezolveScanView>

	// this is the audio scan UI component
    <com.rezolve.sdk.views.RezolveAudioVizualizationView
        android:id="@+id/visualizer"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_below="@+id/scan_view"
        android:layout_marginTop="1dp"
        android:background="#000000"
        android:layerType="hardware" >
    </com.rezolve.sdk.views.RezolveAudioVizualizationView>
</RelativeLayout>

```
ScanManager is different than other Managers as it relies on a user interface component.

### Methods: startVideoScan, stopVideoScan

```objective_c

```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface {

    private final static String API_KEY = "your_api_key";

    private final static String TAG = ScanActivity.class.getSimpleName();
    private ScanManager scanManager;
    private RezolveScanView rezolveScanView;
    private RezolveAudioVizualizationView rezolveAudioVizualizationView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
                .getRezolveSession().getScanManager(this, true);

        // use startVideo to begin watching for scannable media
        scanManager.startVideoScan(this, rezolveScanView);
        // when done, call stopVideoScan
        scanManager.stopVideoScan();
    }

    // in response to a scan, the following methods may be called:
    //
    // processingStarted  - fires when a scannable asset has been recognized.
    // onProductResult    - fires if the asset is recognized as a product link
    // onRezolveResult    - fires if the asset is some other type the SDK can handle,
    //                      like a tollway transponder barcode
    // onScanError        - fires if asset is recognized, but cannot be decoded (for
    //                      example, due to network error)
    // processingFinished - fires when asset recognition is finished
    //

    @Override
    public void processingStarted() {
        Log.d(TAG, "processingStarted");
    }

    // if scan media contains a product link, onProductResult fires and
    // returns a Product object
    @Override
    public void onProductResult(Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        // ... etc
    }

    // if scan media is another recognized format, onRezolveResult fires and
    // returns a RezolveScanResult object. Inspect the object to get the
    // payload.
    @Override
    public void onRezolveResult(RezolveScanResult result) {
        Log.d(TAG, "Scan success! "+result.mContent);
        String content = result.mContent;
        String description = result.mDescription;
        Bitmap bitmap = result.mBitmap;
        String payloadId = result.mPayloadId;
        String reportActionToken = result.mReportActionToken;
    }

    @Override
    public void onScanError(String errorTitle, String errorMsg) {
        // display scan error notification
    }

    @Override
    public void processingFinished() {
        Log.d(TAG, "processingFinished");
    }
}
```

Starts/stops watching the camera video stream to look for tagged media, qr codes, and barcodes.

Method signature: `session.scanManager.startVideoScan()`

Method signature: `session.scanManager.stopVideoScan()`

### Methods: startAudioScan, stopAudioScan

```objective_c

```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface {
    // same vars as above

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
                .getRezolveSession().getScanManager(this, true);

        // use startAudioScan to being listening for watermarked media
        scanManager.startAudioScan(this, rezolveAudioVizualizationView);
        // use stopAudioScan to cease listening.
        scanManager.stopAudioScan();
    }
    // the same response methods apply to AudioScan as VideoScan. See above.
}
```

Starts/stops listening to microphone audio stream for tagged media.

Method signature: `session.scanManager.startAudioScan()`

Method signature: `session.scanManager.stopAudioScan()`

### Method: destroy

```objective_c

```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface {
    // same vars as above

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
                .getRezolveSession().getScanManager(this, true);

        // use stopAudioScan to cease listening.
        scanManager.destroy();
    }
    // destroy has no response methods
}
```

The destroy() method should always be called when done with scanning. The method destroys the initialized scan manager, and frees the resources it was consuming.

Method signature: `session.scanManager.destroy()`







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




