# Module Reference Docs

## Module: AuthenticationManager
Authentication services are an aggregate of SDK.

This module handles consumer user creation and authentication.

### Method: Register

```swift
  let deviceProfile: DeviceProfile = DeviceProfile(
      deviceId: "123",
      make: "Apple",
      osType: "iOS",
      osVersion: "10",
      locale: "Europe/London"
  )

  let signUpRequest: SignUpRequest = SignUpRequest(
      email: "johndoe@domain.com",
      firtName: "John",
      lastName: "Doe",
      name: "John Doe",
      device: deviceProfile
  )


  sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
      self.entityId = entityId;
      self.partnerId = partnerId

      sdk.createSession(
          entityId: self.entityId!,
          partnerId: self.partnerId!,
          device: deviceProfile) { (session: RezolveSession) in

              self.mySession = session
      }
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

RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).registerUser(
signUpRequest, new RezolveInterface() {
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

```swift
let deviceProfile: DeviceProfile = DeviceProfile(
  deviceId: "123",
  make: "Apple",
  osType: "iOS",
  osVersion: "10",
  locale: "Europe/London"
)

sdk.createSession(
  entityId: self.entityId!,
  partnerId: self.partnerId!,
  device: deviceProfile) { (session: RezolveSession) in
      self.mySession = session
}

```
```java
RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).createSession(entityId, 
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

```swift
// When session ends you should inform the sdk by calling
session.authenticationManager.logout();
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

```swift
  import UIKit
  import RezolveSDK

  class CustomerProfilerViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.customerProfileManager.get(callback: { 
          (customerProfile:CustomerProfile) in

            let dateCreated: Date?  = customerProfile.dateCreated
            let dateUpdated: Date?  = customerProfile.dateUpdated
            let devices: Array<DeviceProfile>?  = customerProfile.devices
            let email: String  = customerProfile.email
            let entityId: String  = customerProfile.entityId
            let firstName: String?  = customerProfile.firstName
            let lastName: String  = customerProfile.lastName

            let name: String  = customerProfile.name
            let timezone: String  = customerProfile.timezone
            let title: String  = customerProfile.title

        }, errorCallback: { (errors: Array<HttpRequestError>) in
            // handle error
        })
    }
  }
```
```java
// get using CustomerProfileInterface
public class Profile extends AppCompatActivity implements CustomerProfileInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CustomerProfileManager myProfileManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getCustomerProfileManager();

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

No parameters are required to be passed in. The method always fetches the profile for the authenticated consumer.

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

```swift
import UIKit
import RezolveSDK

class CustomerProfilerViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        self.mySession?.customerProfileManager.get(callback: { 
         (customerProfile:CustomerProfile) in

            customerProfile.name = "Mary Doe"

            self.mySession?.customerProfileManager.update( customerProfile: 
             customerProfile, callback: { (remoteCustomerProfile: CustomerProfile) in

                // handle result
            }, errorCallback: { (errors: Array<HttpRequestError>) in

                // handle errors
            })

        }, errorCallback: { (errors: Array<HttpRequestError>) in

            // handle error
        })
    }
}
```
```java
// update using CustomerProfileInterface
public class Profile extends AppCompatActivity implements CustomerProfileInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CustomerProfileManager myProfileManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getCustomerProfileManager();


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

```swift
import UIKit
import RezolveSDK

class AddressBookViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let address: Address = Address()

        address.city = "London"
        address.country = "UK"
        address.line1 = "St..."
        address.line2 = "111"
        address.shortName = "House"
        address.zip = "000999"
        address.state = "State"

        self.mySession?.addressbookManager.create(
            address: address) { (remoteAddress: Address) in

                let addressId: String = address.id
        }


        self.mySession?.addressbookManager.get(id: "123") { (remoteAddress: Address) in

            let addressId: String = remoteAddress.id
        }
    }
}
```
```java
// create address using addressBookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getAddressbookManager();

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

```swift
import UIKit
import RezolveSDK

class AddressBookViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.addressbookManager.get(id: "123") { (remoteAddress: Address) in

           remoteAddress.shortName = "My Company"

           self.mySession?.addressbookManager.update(address: remoteAddress) { 
            (remoteAddress2: Address) in

               let addressId: String = remoteAddress2.id
           }
       }
    }
}
```
```java
// update address using addressBookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getAddressbookManager();

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

```swift
import UIKit
import RezolveSDK

class AddressBookViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.addressbookManager.get(id: "123") { (remoteAddress: Address) in


          self.mySession?.addressbookManager.delete(address: remoteAddress) { () in

              //handle callback
          }
      }
    }
}
```
```java
// delete address using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getAddressbookManager();

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

```swift
import UIKit
import RezolveSDK

class AddressBookViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.addressbookManager.get(id: "123") { (remoteAddress: Address) in

            let addressId: String = remoteAddress.id
        }
    }
}
```
```java
// get address using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getAddressbookManager();

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

```swift
import UIKit
import RezolveSDK

class AddressBookViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.addressbookManager.getAll() { (listOfAddress: Array<Address>) in

           //handle the result
       }
    }
}
```
```java
// get all addresses using AddressbookInterface
public class addressBook extends AppCompatActivity implements AddressbookInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AddressbookManager myAddressManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getAddressbookManager();

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

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let favourite: Favourite = Favourite()

        favourite.provider = "AT&T"
        favourite.type = "phone"
        favourite.value = "555499999999"

        self.mySession?.favouriteManager.create(
            favourite: favourite) { (remoteFavourite: Favourite) in

                let favouriteId: String = remoteFavourite.id
        }
    }
}
```
```java
// create favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

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

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            remoteFavourite.value = "555488888888"

            self.mySession?.favouriteManager.update(favourite: remoteFavourite) {
             (remoteFavourite2: Favourite) in

                //handle result
            }
        }
    }
}
```
```java
// update favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

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

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            self.mySession?.favouriteManager.delete(favourite: remoteFavourite) { () in

                //handle callback
            }
        }
    }
}
```
```java
// delete favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

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

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.get(id: "123") { (remoteFavourite: Favourite) in

            let favouriteId: String = remoteFavourite.id
        }
    }
}
```
```java
// get a favorite using FavouriteInterface
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

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

```swift
class FavouriteViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.favouriteManager.getAll() { (listOfFavourites: Array<Favourite>) in

            //handle the results
        }
    }
}
```
```java
public class Favorites extends AppCompatActivity implements FavouriteInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        FavouriteManager myFavouriteManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getFavouriteManager();

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

```swift
class WalletViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        let paymentCard = PaymentCard()

        paymentCard.brand = "MASTER"
        paymentCard.type = "credit"
        paymentCard.cardHolderName = "John Doe"
        paymentCard.expiresOn = "12/18"
        paymentCard.pan = "9999999999999999"
        paymentCard.addressId = "123" // address.id
        paymentCard.shortName = "My Mastercard"

        self.mySession?.walletManager.create(
            paymentCard: paymentCard) { (remotePaymentCard: PaymentCard) in

                let paymentCardId: String = remotePaymentCard.id
        }
    }
}
```
```java
// create payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getWalletManager();

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

```swift
class WalletViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { 
         (remotePaymentCard: PaymentCard) in

            remotePaymentCard.shortName = "My Visa"

            self.mySession?.walletManager.update(paymentCard: remotePaymentCard) {
             (remotePaymentCard2: PaymentCard) in

                //handle result
            }
        }
    }
}
```
```java
// update payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getWalletManager();

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

```swift
class WalletViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { (remotePaymentCard: PaymentCard) in

            self.mySession?.walletManager.delete(paymentCard: remotePaymentCard) { () in

                //handle callback
            }
        }
    }
}
```
```java
// delete payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getWalletManager();

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

```swift
class WalletViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { (remotePaymentCard: PaymentCard) in

            let paymentCardId: String = remotePaymentCard.id
        }
    }
}
```
```java
// get a payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getWalletManager();

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

```swift
class WalletViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.getAll() { 
         (listOfPaymentCard: Array<PaymentCard>) in

            //handle the results
        }
    }
}
```
```java
// get all payment cards using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getWalletManager();

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

```swift
import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getMerchants() { (listOfMerchant: Array<Merchant>) in

          listOfMerchant.forEach() { (merchant: Merchant) in

              let merchantId: String = merchant.id
              let title: String = merchant.title
          }
      }
  }
}

```
```java
// get merchants using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

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

How to use the returned images: the `image_url` is typically used as a header background, and the image represented by `logo_url` is overlayed on top.



### Method: getCatgories

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getCatalogs(merchantId: "123", 
       catalogId: nil) { (listOfCatalog: Array<Catalog>) in

          listOfCatalog.forEach() { (catalog: Catalog) in

              let catalogId: String = catalog.id
          }
      }
  }
}

```
```java
// get root category and first level child categories using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

		// get categories
        String merchantId = "123";
        myProductManager.getCategories(merchantId,this);
    }

    @Override
    public void onGetCategoriesSuccess(Category category) {
        String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hascategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catparentId = category.getParentId();
        List<Category> children = category.getCategories();
    }
}
```

Method signature: `session.getCategories( merchant_id, [callback or interface] )`

You must pass in the `id` of the `merchant` to whose categories you wish to get.

The method returns an array of `category` objects owned by a specific merchant.

This method returns the *root category* and *first level of child categories*.

#### category Object

|field|format|example|
|---|---|---|
|id|string|2|
|parent_id|string|1|
|name|String|Fitness Wear|
|has_products|boolean|true|
|has_categories|boolean|false|
|image|string|http://domain.com/path/image.jpg|
|imageThumbs|array of url strings|&nbsp;|
|categoryParentId|string|123|
|categories|array of category objects|&nbsp;|

Notes:

If `has_categories` is `false`, the `categories` array should be empty.

If `has_products` is `true`, call `getProducts` with the category `id` to get a product list.

This method takes a category id, and returns *that category*, and *all its child categories*.

### Method: getCategory

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getCatalogs(merchantId: "123", 
       catalogId: "A") { (listOfCatalog: Array<Catalog>) in

          listOfCatalog.forEach() { (catalog: Catalog) in

              let catalogId: String = catalog.id
          }
      }
  }
}

```
```java
// get a single category using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get single category
        String categoryId = "123";
        String merchantId = "123";
        myProductManager.getCategory(merchantId, categoryId, this);
    }

    @Override
    public void onGetCategorySuccess(Category category) {
        String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catParentId = category.getParentId();
        List<Category> children = category.getCategories();
    }
}
```

Method signature: `session.getCategory( merchant_id, categgory_id, [callback or interface] )`

The method returns a `category` object.

<aside class="notice">
TODO
Note: IOS uses the `getCatalogs` method to fetch both singuler and multiple catalogs. To get all catalogs, simply set the `catalogId` to `nil`. To get a singular catalog, specify a `catalogId`.
</aside>

### Method: getProducts

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      let pageNavigation = PageNavigation(count: 10,
                                          pageIndex: 0,
                                          sortBy: nil,
                                          sort: PageNavigationSort.ASC)

      self.mySession?.productManager.getProducts(
          merchantId: "123",
          catalogId: "A",
          pageNavigation: pageNavigation) { (productPageResult: PageResult<DisplayProduct>) in

              productPageResult.embedded.forEach() { (displayProduct: DisplayProduct) in

                  let productId = displayProduct.id
              }
          }
      }
  }
}

```
```java
// get products using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get products
        Integer count = 16;
        Integer page = 1;
        String sort_by_field = "title";
        String sort_direction = "ASC";
        String merchantId3 = "123";
        String categoryId = "123";
        myProductManager.getProducts(merchantId3, categoryId, count, page, 
        sort_by_field, sort_direction, this);
    }

    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
        Integer count = pageResult.getCount();
        Integer total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> products = pageResult.getProducts();

        for (Link link: links){
            Integer linkcount = link.getCount();
            Integer page = link.getPage();
            String sort = link.getSort();
            String sortBy = link.getSortBy();
        }

        for (DisplayProduct displayProduct : products){
            String id = displayProduct.getId();
            List<String> imageThumbs = displayProduct.getImageThumbs();
            String image = displayProduct.getImage();
            float price = displayProduct.getPrice();
            String name = displayProduct.getName();
            String categoryId = displayProduct.getCategoryId();
        }
    }
}
```

Method signature: `session.getProducts( merchant_id, category_id, pageNavigation, [callback or interface] )`

You must pass a `merchant_id`, `category_id`, and a `pageNavivation` object.

The method returns a `pageResult` object.


#### pageResult Object

pageResult is a generic object used for combining pagination info with an array of returned objects. In the case of getProducts, this is an array of DisplayProducts.

|field|format|example|explanation|
|---|---|---|---|
|links|object| |object containing First, Next, Previous, Last links|
|count|integer|10|items per page, as requested in pageNavigation.count|
|total|integer|50|total items in SDKEntity array|
|SDKEntity|array| |array of DisplayProduct objects|

#### links Object

The links object controls pagignation and sorting of paginated results.

|field|format|example|explanation|
|---|---|---|---|
|count|integer|10|number of items per page|
|page|integer|1|what page of results you are requesting|
|sortBy|string|productName|must be a field of the pageResult > SDKEntity object array (see below)|
|sort|enum|one of: ASC, DESC|


#### displayProduct Object

The displayProduct object is used when rending a series of products. It contains a subset of the product object, suitable for displaying so the user can choose a product to view.

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|categoryId|string|123|
|name|string|Fitbit Charge 2|
|price|decimal|129.00|
|imageThumbs|array of thumbnail image urls (as strings)|http://domain.com/path/image.jpg|
|image|main product imaage url (as a string)|http://domain.com/path/image.jpg|


### Method: getProduct

```swift
TODO 

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getProduct(merchantId: "123", catalogId: "A", 
       productId: "ABC") { (product: Product) in
          let productId: String = product.id
          let title: String = product.title
      }
  }
}
```
```java
// get a single product using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get single product
        Product product = new Product();
        String productId = "123abc";
        String merchantId = "123";
        String categoryId = "123";
        myProductManager.getProduct(merchantId, categoryId3, productId, this);
    }

    @Override
    public void onGetProductSuccess(com.rezolve.sdk.model.shop.Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        String description = product.getDescription();
        List<String> images = product.getImages();
        List<String[]> thumbs = product.getImageThumbs();
        String merchant_id = product.getMerchantId();
        float price = product.getPrice();
        List<Option> options = product.getOptions();
        List<Variant> optionsAvail = product.getOptionAvailable();
        List<CustomOption> customOptions = product.getCustomOptions();


        // iterate to get values of options list
        for (Option option : options) {
            String optionLabel = option.getLabel();
            String extraInfo = option.getExtraInfo();
            String optionCode = option.getCode();
            List<OptionValue> optionValues = option.getValues();

            for(OptionValue optionValue: optionValues){
                String value = optionValue.getValue();
                String label = optionValue.getLabel();
            }
        }

        // iterate to get values of variant list
        for (Variant variant : optionsAvail) {
            List<Combination> combinations = variant.getCombinations();
            // iterate to get values of composition hashmap
            for (Combination combination : combinations ){
                String comboValue = combination.getValue();
                String comboCode = combination.getCode();
            }
        }

        //iterate to get values of CustomOptions list
        for (CustomOption customOption : customOptions){
            List<CustomOptionValue> customOptionValues = customOption.getValues();
            int customOptionId = customOption.getOptionId();
            int customOptionSortOrder = customOption.getSortOrder();
            String customOptiontitle = customOption.getTitle();
            Boolean customOptionIsRequired = customOption.isRequired();

            // iterate to get values of customOptionValues
            for( CustomOptionValue customOptionValue : customOptionValues ){
                String customOptionValueTitle = customOptionValue.getTitle();
                int customOptionValuesSortOrder = customOptionValue.getSortOrder();
                String customOptionValueId = customOptionValue.getValueId();
            }
        }

    }
}
```

Method signature: `session.getProduct( merchant_id, catalog_id, product_id, [callback or interface] )`

You must pass a `merchant_id`, `catalog_id`, and a `product_id`.

The method returns a <a href="#product-object">`product object`</a> .


#### product Object

The displayProduct object is used when rending a series of products. It contains a subset of the product object, suitable for displaying so the user can choose a product to view.

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|merchant_id|string|brookstone|
|title|string|Fitbit Charge 2|
|subtitle|string|Heart Rate and Fitness Wristband|
|description|string|Make every beat count with Fitbit Charge 2 - a heart rate and fitness wristband that tracks activity, exercise and sleep, includes advanced fitness features and displays real-time stats on a large display.
|price|decimal|129.00|
|images|array of image urls|http://domain.com/path/image.jpg|
|thumbs|array of thumbnail image url arrays.|[ http://domain.com/path/thumb400.jpg, http://domain.com/path/thumb800.jpg, http://domain.com/path/thumb1600.jpg ]|
|options|array of Option objects|&nbsp;|
|optionAvailable|array of Variant objects|&nbsp;|
|customOptions|array of customOption objects|&nbsp;|

#### option Object

An option represents a choice. For example, Color or Size on a clothing item.

|field|format|example|
|---|---|---|
|label|string|Color|
|optionCode|string|color|
|extraInfo|string displayed to the user, provides additional info about the option|Color may differ from shown.|
|optionValues|array of optionValue objects|&nbsp;|

#### optionValue Object
OptionValue objects are key/value pairs, each describing one choice within an option. For example, if the option is Color, you might have three optionValue objects, one for the red option, one for blue, and one for green.

|field|format|example|
|---|---|---|
|label|string|Red|
|value|string|5|

#### variant Object

|field|format|example|
|---|---|---|
|array of Combination objects|array|&nbsp;|

#### combination Object
A combination object represents one unique combination of options for this product. For example, if a shirt has 4 sizes, and 3 colors, it should have 12 combination objects (assuming all combinations are available). The number of key/value pairs in a combination object will vary, and is dependent upon the number of options in the product. A product with size, color, and pattern, would have three key/value pairs in each combination.

|field|format|example|
|---|---|---|
|code|string|color|
|value|string|Red|


> Example data for a product with three sizes, one color:

```json
"options_available": [
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "S"
                }
            ]
        },
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "XS"
                }
            ]
        },
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "L"
                }
            ]
        }
    ]
```


#### customOption Object

|field|format|example|
|---|---|---|
|id|integer|123|
|sortOrder|integer|Items will be sorted sequentially by this integer. Exaple: 1 would come first, 2 next, etc.|
|title|string|Ring Size|
|isRequired|boolean|TRUE|
|customOptionValues|array of customOptionValue objects|&nbsp:|

#### customOptionValue Object
|field|format|example|
|---|---|---|
|id|string|123|
|title|string|Size 7|
|sortOrder|integer|Items will be sorted sequentially by this integer. Exaple: 1 would come first, 2 next, etc.|


## Module: UserActivityManager

Activity is an aggregate of Session.

The activity module handles order history and other historical data.

### Method: getOrders

```swift
import UIKit
import RezolveSDK

class UserActivityViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.userActivityManager.getOrders() { (transactions: Array<Transaction>) in

            //handle result
        }
    }
}
```
```java
// get transactions using UserActivityManager
public class Activities extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getUserActivityManager();

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

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let options: [String: OptionValue] = [
            "color": (product.options["color"]?.values[0])!
        ]

        let checkoutProduct = CheckoutProduct(
            productId: product.id,
            productTitle: product.title,
            quantity: 1,
            finalPrice: product.price,
            options: options
        )

        let loyalty: Loyalty = Loyalty(number: "123123")

        let delivery: Delivery = Delivery(addressId: "123123") // address.id

        let geoLoc: [String: Double] = [
            "lat": 12.000,
            "long": 21.000
        ]

        let myCart: Cart = Cart(
            merchantId: "1",
            loyaltySettings: loyalty,
            deliverySettings: delivery,
            email: "johndoe@domain.com",
            type: "scan",
            products: [checkoutProduct],
            geoLoc: geoLoc
        )


        self.mySession?.checkoutManager.checkoutOrder(cart: cart) { (order: Order) in

            // handle order
        }
    }
}
```
```java
// create an order and calculate cart cost
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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

```swift
self.mySession?.checkoutManager.createPaymentRequest(paymentCard: paymentCard, cvv: cvv)
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

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.checkoutManager.checkoutOrder(cart: cart) { (order: Order) in

            let paymentCard = ... // initialize paymentCard
            let cvv = ... // capture cvv

            let paymentRequest: PaymentRequest = 
            self.mySession?.checkoutManager.createPaymentRequest( paymentCard: paymentCard, 
            cvv: cvv)

            self.mySession?.checkoutManager.buyOrder(paymentRequest: paymentRequest,
             order: order) { (transaction: Transaction) in
                //handle result
            }
        }
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        let order = ... // initialize order

        self.mySession?.checkoutManager.signOrderUpdate(order: order, 
         callback: { (orderUpdate: OrderUpdate) in

            // handle the updates
        })
    }
}
```

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

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
    @IBOutlet var scanCameraView: ScanCameraView?
    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()
        //self.mySession = ... // initialize session
        let scanManager = self.mySession?.getScanManager()
        scanManager?.productResultDelegate = self
        scanManager?.rezolveScanResultDelegate = self

        scanManager?.startVideoScan(scanCameraView: self.scanCameraView!)
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        scanManager?.stop()
    }
}

extension ScanManagerViewController : ProductDelegate, RezolveScanResultDelegate {

    // in response to a scan, the following methods may be called:
    //
    // resultDidFetched   - fires when a scannable asset has been recognized.
    // productDidFetched  - fires if the asset is recognized as a product link
    // errorHappened      - fires if asset is recognized, but cannot be decoded (for
    //                      example, due to network error)
    //

    public func resultDidFetched(result: RezolveScanResult) {
        // handle valid scan result
    }

    public func productDidFetched(product: Product) {
        // handle product
    }

    public func errorHappened(error: String) {
        // handle error result
    }
}
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
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)
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

    // if scan media contains a category link, onCategoryResult fires and
    // returns a Category object
    @Override
    public void onCategoryResult(Category category, String s) {
        String categoryId = category.getId();
        String categoryParentId = category.getParentId();
        String categoryName = category.getName();
        List<String> categoryImageThumbs = category.getImageThumbs();
        String categoryImage = category.getImage();
        List<Category> categoryChildren = category.getCategories();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
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

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()
        //self.mySession = ... // initialize session
        let scanManager = self.mySession?.getScanManager()
        scanManager?.productResultDelegate = self
        scanManager?.rezolveScanResultDelegate = self

        scanManager?.startAudioScan()
    }
}

extension ScanManagerViewController : ProductDelegate, RezolveScanResultDelegate {
    // in response to a scan, the same methods may be called as with .startVideoScan
}
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
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)
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

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
	// ...
    override func viewDidLoad() {
    	// ...
        scanManager?.destroy()
    }

}
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
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)
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




