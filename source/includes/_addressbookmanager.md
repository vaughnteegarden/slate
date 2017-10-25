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






