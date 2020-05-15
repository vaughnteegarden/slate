## Module: PhonebookManager

**Topics**

* <a href="#method-phonebookmanager-create">phonebookManager.create</a>
* <a href="#method-phonebookmanager-update">phonebookManager.update</a>
* <a href="#method-phonebookmanager-delete">phonebookManager.delete</a>
* <a href="#method-phonebookmanager-get">phonebookManager.get</a>

The PhonebookManager is an aggregate of Session.

This module provides CRUD functions for consumer phone numbers. The consumer may add one or more phone numbers to their account.

### Method: phonebookManager.create

``` swift
import UIKit
import RezolveSDK

internal final class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()


        let rezolveSDK = ... // Initialize RezolveSDK

        let accessToken: String = ... // Access Token
        let deviceProfile: DeviceProfile = DeviceService().getDeviceProfile()
        let sdkData = SessionService().getSdkData()
        guard let entityId = sdkData.entityId,
              let partnerId = sdkData.partnerId else {
                preconditionFailure("SDK Data is nil")
        }


        rezolveSdk?.createSession(
            accessToken: "",
            entityId: entityId,
            partnerId: partnerId, callback: { (rezolveSession) in

                // New RezolveSDK.Phone
                let phone = Phone()
                phone.name = "Home phone"
                phone.number = "555-555-1212"

                rezolveSession.phonebookManager.create(phone: phone, callback: { (aPhone) in
                    print(aPhone.id)
                    print(aPhone.name)
                    print(aPhone.phone)
                })
        })
    }
}
```
``` java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        Phone phone = new Phone();
        phone.setName("Home phone");
        phone.setPhone("555-555-1212");

        myPhonebookManager.create(phone, this);
    }

    @Override
    public void onPhonebookCreateSuccess(Phone phone) {
        String phoneId = phone.getId();
        String phoneName = phone.getName();
        String phoneNumber = phone.getPhone();
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```
Method signature: `session.phonebookManager.create( phone, [callback or interface] )`

You must pass in a valid `phone` object with the `id` field blank.

The method returns the created `phone` object with `id` upon success.

#### phone Object

|field|format|example|
|---|---|---|
|phoneId|auto-assigned string|123abc|
|phoneName|string|Home phone|
|phonePhone|string|+1(555)555-1212|

### Method: phonebookManager.update

```swift
import UIKit
import RezolveSDK

internal final class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()


        let rezolveSDK = ... // Initialize RezolveSDK

        let accessToken: String = ... // Access Token
        let deviceProfile: DeviceProfile = DeviceService().getDeviceProfile()
        let sdkData = SessionService().getSdkData()
        guard let entityId = sdkData.entityId,
              let partnerId = sdkData.partnerId else {
                preconditionFailure("SDK Data is nil")
        }


        rezolveSdk?.createSession(
            accessToken: accessToken,
            entityId: entityId,
            partnerId: partnerId, callback: { (rezolveSession) in

                // Target RezolveSDK.Phone object
                let phone = Phone()
                phone.id = "111"
                phone.name = "Home phone"
                phone.number = "555-555-1212"

                rezolveSession.phonebookManager.update(phone: phone, callback: { (aPhone) in
                    print(aPhone.id)
                    print(aPhone.name)
                    print(aPhone.phone)
                })
        })
    }
}
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        String newNumber = "555-555-1313";
        phone.setId("123");
        phone.setName("HomePhone");
        phone.setPhone(newNumber);
        myPhonebookManager.update(phone,this);
    }

    @Override
    public void onPhonebookUpdateSuccess(Phone phone) {
        String phoneId = phone.getId();
        String phoneName = phone.getName();
        String phoneNumber = phone.getPhone();
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

Method signature: `session.phonebookManager.update( phone, [callback or interface] )`

You must pass in a valid `phone` object with `id` populated.

The method returns the updated `phone` object upon success.


### Method: phonebookManager.delete

```swift
import UIKit
import RezolveSDK

internal final class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()


        let rezolveSDK = ... // Initialize RezolveSDK

        let accessToken: String = ... // Access Token
        let deviceProfile: DeviceProfile = DeviceService().getDeviceProfile()
        let sdkData = SessionService().getSdkData()
        guard let entityId = sdkData.entityId,
              let partnerId = sdkData.partnerId else {
                preconditionFailure("SDK Data is nil")
        }


        rezolveSdk?.createSession(
            accessToken: accessToken,
            entityId: entityId,
            partnerId: partnerId, callback: { (rezolveSession) in

                // Target RezolveSDK.Phone object
                let phone = Phone()
                phone.id = "111"
                phone.name = "Home phone"
                phone.number = "555-555-1212"

                rezolveSession.phonebookManager.delete(phone: phone, callback: {
                    // Void closure
                })
        })
    }
}
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        Phone phone = new Phone();
        phone.setName("Home phone");
        phone.setPhone("555-555-1212");

        myPhonebookManager.delete(phone,this);
    }

    @Override
    public void onPhonebookDeleteSuccess(HttpResponse httpResponse) {
        // display post-deletion confirmation
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}

```

Method signature: `session.phonebookManager.delete( phone, [callback or interface] )`

You must pass in the `phone` object to delete.



### Method: phonebookManager.get

```swift
import UIKit
import RezolveSDK

internal final class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let rezolveSDK = ... // Initialize RezolveSDK

        let accessToken: String = ... // Access Token
        let deviceProfile: DeviceProfile = DeviceService().getDeviceProfile()
        let sdkData = SessionService().getSdkData()
        guard let entityId = sdkData.entityId,
              let partnerId = sdkData.partnerId else {
                preconditionFailure("SDK Data is nil")
        }

        rezolveSdk?.createSession(
            accessToken: "",
            entityId: entityId,
            partnerId: partnerId, callback: { (session) in
                session.phonebookManager.get(callback: { (phone) in
                    if let aPhone = phone {
                        print(aPhone.id)
                        print(aPhone.name)
                        print(aPhone.phone)
                    }
                })
        })
    }
}
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        myPhonebookManager.get(this);
    }

    @Override
    public void onPhonebookGetSuccess(List<Phone> list) {
        for (Phone phone:list) {
            String phoneId = phone.getId();
            String phoneName = phone.getName();
            String phoneNumber = phone.getPhone();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

Method signature: `session.phonebookManager.get( [callback or interface] )`

The method takes no parameters.

The Android method returns an array of `Phone` objects. The IOS method returns only the first `Phone` object.

