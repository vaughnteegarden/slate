## Module: AuthenticationManager

**Topics**

* <a href="#method-register-deprecated">Register (deprecated)</a>
* <a href="#method-create-session-v2-with-jwt">Create Session (v2, with JWT)</a>
* <a href="#method-create-session-v1-deprecated">Create Session (v1, deprecated)</a>
* <a href="#method-logout-deprecated">Logout (deprecated)</a>

Authentication services are an aggregate of SDK.

This module handles consumer user creation and authentication.

<aside class="warning">
Note that several methods in this class are now deprecated.
</aside>





### Method: Register (deprecated)

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

RezolveSDK.getInstance(API_KEY, ENVIRONMENT).registerUser(
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








### Method: Create Session (v2, with JWT)

```swift
import UIKit
import RezolveSDK


class SandboxViewController: UIViewController {

    private var API_KEY: String = "your_api_key"
    private var API_ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"
    private var accessToken: String = "abc123.abc123.abc123"
    private var entityId: String = "123"
    private var partnerId: String = "123"
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        var sdk = RezolveSDK(
            apiKey: API_KEY,
            env: API_ENVIRONMENT
        )
        
        sdk.createSession(accessToken: accessToken, entityId: entityId, partnerId: partnerId) { session
            
            // your rezolve SDK logic here
        }
    }
}
```
```java
String API_KEY = "your_api_key";
String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";
String accessToken = "abc123.abc123.abc123";  // JWT token from auth server
String entityId = "123";	// from auth server
String partnerId = "123";   // from auth server

// NEW session requires Login JWT  from auth server (customer auth or RUA)
RezolveSDK.getInstance(API_KEY, ENVIRONMENT).createSession( accessToken, entityId, partnerId, new RezolveInterface() {

	@Override
	public void onInitializationSuccess(RezolveSession rezolveSession, String entityId, String partnerId) {
		// use created session to access managers.  Example...
		rezolveSession.getCustomerProfileManager().get();
	}

	@Override
	public void onInitializationFailure() {
		// handle error
	}
});
```

Method signature: `sdk.createSession( accessToken, entityId, partnerId, [callback or interface] )`

`accessToken` is a Jason Web Token as a string, returned from the auth server (customer auth server, or RUA)

`entityId` is a string, returned from the auth server (customer auth server, or RUA)

`partnerId` is a string, returned from the auth server (customer auth server, or RUA)

The method returns a `rezolveSession` object.  A valid `rezolveSession` gives you access to the rest of the SDK methods;  .getAddressbookManager(), .getAuthenticationManager(), .getCheckoutManager(), etc. 





### Method: Create Session (v1, deprecated)

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
RezolveSDK.getInstance(API_KEY, ENVIRONMENT).createSession(entityId, 
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







### Method: Logout (deprecated)

This method is no longer needed. 

With v2 auth (JWT-based), when your consumer logs out, you expire the JWT at the auth server, and this prevents the SDK from talking to the Rezolve servers.

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






