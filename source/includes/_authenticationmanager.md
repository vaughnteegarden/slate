## Module: AuthenticationManager
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






