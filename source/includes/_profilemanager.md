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






