## JWT Authentication

**Topics**

* <a href="#terminology">Terminology</a>
* <a href="#jwt-flow">JWT Flow</a>
* <a href="#create-the-registration-jwt">Create the Registration JWT</a>
* <a href="#register-a-new-rezolve-user">Register a new Rezolve User</a>
* <a href="#logging-in-a-user">Logging in a User</a>
* <a href="#handling-jwt-expiration-amp-session-preservation">Handling JWT Expiration & Session Preservation</a>
* <a href="#http-error-responses">HTTP Error Responses</a>

As of release 1.6.0, the Rezolve SDK no longer includes an authentication system. Resultingly, the `AuthenticationManager.register` and `AuthenticationManager.logout`  methods have been deprecated.

Instead, Rezolve is utilizing a server-to-server JWT authentication system, conformant with the **https://tools.ietf.org/html/rfc7519** standard. If you are not familar with JSON Web Tokens, the site **https://jwt.io/** provides an excellent primer on the use of JWTs, as well as links to various JWT libraries you can utilize.

Rezolve expects the primary authentication of users will happen outside the SDK. Thus, exactly how you implement authentication in your app will depend on your existing auth system. In the server-to-server realm, however, there is only one instance in which your authentication server must interact with the Rezolve server. 

After that, whether or not the SDK can talk to Rezolve depends on supplying a valid JWT to the SDK from your auth system.

When the Partner creates a new user on their auth server, or wishes to associate an existing user with Rezolve for the first time, the partner must generate the Registration JWT, and then POST it to the `/api/v1/authentication/register` endpoint. The Rezolve server will validate the JWT, create a new user, create the user's public/private key pair, and return to you the Rezolve EntityId. 

When a user logs in to your auth system, generate a new Login JWT and supply to CreateSession in the SDK. As long as the JWT is valid, the SDK can talk to Rezolve. A method is suggested below for smoothly handling JWT timeouts without interrupting the SDK session. 


### Terminology

|Term|Definition|
|---|---|
|partner_id|A numerical id you are assigned by Rezolve. Usually a 2-4 digit integer.|
|partner_api_key|The API key you are assigned by Rezolve. 36 characters including dashes.|
|partner_auth_key|The Auth key you are assigned by Rezolve. This plays the role of the JWT Secret. The partner_auth_key is typically a ~90 character hash.|
|JWT token|A JSON Web Token, consisting of a header, payload, and signature. The header and signature are signed with the parther_auth_key, above. It is used as a bearer token when communicating with the Rezolve server.|
|accessToken|In the IOS and Android code samples, the accessToken is the JWT Token you generated.|
|deviceId|An id that is randomly generated upon app install and stored. This id is placed in both the JWT payload and x-header sent by the SDK. The Rezolve server checks that these values match to deter request origin spoofing. All calls except Registration calls require this.|


### JWT Flow

<img src="images/wsd-jwt-flow.png" style="margin:6px 0;"><br/>[ <a href="images/wsd-jwt-flow.png" target="_blank">View full size</a> ]



### Create the Registration JWT

<aside class="notice">
**Note:**

For testing via cURL or Postman without a server-side JWT library, you can use https://jwt.io/#debugger to generate valid JWT tokens. 

Use https://www.epochconverter.com/ to generate the token expiration in Epoch time. 
</aside>

#### Requirements

You must possess:

|field|description|example|
|---|---|---|
|partner_id|The numerical id you are assigned by Rezolve |317|
|partner_api_key|The API key you are assigned by Rezolve|a1b2c3d4-e5f6-g7h8-i9j0-a1b2c3d4e5f6|
|partner_auth_key|The Auth key you are assigned by Rezolve|qwer+4ty ... JYG6XavJg== (approx 90 characters)|

#### JWT Header

```JSON
{
	"alg": "HS512",
	"typ": "JWT"
}
```

|key|value|notes|
|---|---|---|
|alg|HS512|algorithm, HMAC SHA-512|
|typ|JWT|type|


#### JWT Payload

```JSON
{
	"rezolve_entity_id": ":NONE:",
	"partner_entity_id": "partner_entity_id",
	"exp": 1520869470
}
```

|key|value|notes|
|---|---|---|
|rezolve_entity_id|:NONE:|use :NONE: when registering|
|partner_entity_id|your_user_id|The unique identifier for your user record. This may be a numerical id, or an identifying string such as email address.|
|exp|1520869470|Expiration, as a unix timestamp integer. Set the expiration value to a small number, now() + 30 minutes or less.|




#### Signature

```JSON
HMACSHA512(
	base64UrlEncode(header) + "." +
	base64UrlEncode(payload),
	${$partner_auth_key}
)
```
Sign the header and payload with the `partner_auth_key`.  It is not necessary to decode the key before using it. Pass the whole value as a string to your library's `getBytes` method.  

You may need to specify the charset as UTF8. 
Example 1, Microsoft: `SecretKey(Encoding.UTF8.GetBytes(key));`  
Example 2, Java:  `Secret.getBytes("UTF-8");`

```
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.
eyJyZXpvbHZlX2VudGl0eV9pZCI6IjpOT05FOiIsInBhcnRuZXJfZW50aXR5X2lkIjoiMTIzIiwiZXhwIjoxNTIwODY5NDcwfQ.
5y2e6QpUKcqTNLTv75nO6a6iFPVxrF8YeAH5NTg2ZO9dkub31GEs0N46Hu2IJf1bQ_vC2IOO9Z2N7drmvA_AKg
```

The resulting JWT will look something like this (except without linebreaks); the first third is the header, the second third the payload, and the last third the signature: 




### Register a new Rezolve User

```
POST: https://sandbox-api-tw.rzlvtest.co/api/v1/authentication/register
-H content-type: application/json
-H x-rezolve-partner-apikey: your-api-key
-H authorization: Bearer signed-jwt
-d {
"email": "user@example.com"
}
```

After the JWT is created, POST it to the Rezolve registration endpoint. This will create a Rezolve User, generate a public/private keypair for the user, and return to you the corresponding Entity Id. 

Example, using Sandbox endpoint: 


```JSON
{
    "entity_id" : "entity123",
    "partner_id" : "3"
}
```
The endpoint will reply with an entity id and the partner id. You should save the Rezolve Entity Id to your authentication database and associate it with the user's record.




### Logging in a User

Once a Rezolve User has been registered and an `entity_id` obtained, you can log in the user using the instructions below. 

For returning users, log them in via your normal method in your auth server, and then follow the instructions below.

Create a new `Login JWT`, and use it as the `accessToken` in the `createSession` method.

#### JWT Header

Note the addition of the "auth" line.

```JSON
{
	"auth": "v2",
	"alg": "HS512",
	"typ": "JWT"
}
```

|key|value|notes|
|---|---|---|
|auth|v2|auth version to use, login uses v2|
|alg|HS512|algorithm, HMAC SHA-512|
|typ|JWT|type|

#### JWT Payload

Note the addition of the `device_id`.

```JSON
{
	"rezolve_entity_id": "entity123",
	"partner_entity_id": "partner_entity_id",
	"exp": 1520869470, 
    "device_id": "wlkCDA2Hy/CfMqVAShslBAR/0sAiuRIUm5jOg0a"
}
```

|key|value|notes|
|---|---|---|
|rezolve_entity_id|your_rezolve_entity_id|use the entity_id you obtained during registration|
|partner_entity_id|your_partner_entity_id|set it to the unique identifier for your user record|
|exp|1520869470|Expiration, as a unix timestamp integer. Set the expiration value to a small number, now() + 30 minutes or less.|
|device_id|wlkCDA2Hy/CfMqVAShslBAR/0sAiuRIUm5jOg0a|An id randomly generated upon app installation and stored. This id is placed in both the JWT payload and x-header sent by the SDK. See below for generation instructions.|


#### Signature

```JSON
HMACSHA512(
	base64UrlEncode(header) + "." +
	base64UrlEncode(payload),
	${$partner_auth_key}
)
```
Sign the header and payload with the `partner_auth_key`.

#### Generating the device_id

```swift
// IOS uses the IOS "identifierForVendor" string, so there is no need to generate it. 
// The IOS SDK pulls this value automatically using `UIDevice.current.identifierForVendor?.uuidString` and supplies it to the SDK for the x-header.
// You will need to use this same call to supply the device_id to your auth server for storage with the user profile.

UIDevice.current.identifierForVendor?.uuidString
```

```java
// generate the random id
String deviceId = UUID.randomUUID().toString();

// store the device_id
private static void writeDeviceIdFile(File deviceidfile) throws IOException {
    FileOutputStream out = new FileOutputStream(deviceidfile);
    String id = UUID.randomUUID().toString();
    out.write(id.getBytes());
    out.close();
}

// read the stored device_id
private static String readDeviceIdFile(File deviceidfile) throws IOException {
    RandomAccessFile f = new RandomAccessFile(deviceidfile, "r");
    byte[] bytes = new byte[(int) f.length()];
    f.readFully(bytes);
    f.close();
    return new String(bytes);
}

// supply the random id to the SDK
RezolveSDK.setDeviceIdHeader(deviceId);

```

To generate and set the device_id, see the OS specific examples to the right. On IOS it is handled automatically using `identifierForVendor?.uuidString`.  Android must manually generate, store, read, and pass the `device_id` to the SDK. 

#### Create the Session

In the samples to the right, `accessToken` is the Login JWT token you created above. 

Note in the Android sample you are also passing in an AuthRequestProvider. This is used for handling expiring JWT sessions, and is explained in the next section.


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
String deviceId = "wlkCDA2Hy/CfMqVAShslBAR/0sAiuRIUm5jOg0a"; // from stored device_id, see "Generating the device_id" above

// Use builder to create instance of SDK and set SDK Params
// Pass in an AuthRequestProvider here, to handle expiring JWT tokens
rezolveSDK = new RezolveSDK.Builder()
               .setApiKey(API_KEY)
               .setEnv(ENVIRONMENT)
               .setAuthRequestProvider(new PartnerAuthRequestProvider(AuthService.getInstance()))
               .build();

// Set JWT Auth Token from partner auth server
rezolveSDK.setAuthToken(accessToken);

// Start session, again supplying JWT auth token
RezolveSDK.createSession( accessToken, entityId, partnerId, new RezolveInterface() {

	@Override
	public void onInitializationSuccess(RezolveSession rezolveSession, String entityId, String partnerId) {
        // set device_id so it can be passed in x-header
        RezolveSDK.setDeviceIdHeader(deviceId);
    
		// use created session to access managers.  Example...
		rezolveSession.getAddressbookManager().get(...);
	}

	@Override
	public void onInitializationFailure() {
		// handle error
	}
});
```




### Handling JWT Expiration & Session Preservation

```swift
import RezolveSDK
import Foundation

internal enum Result<T> {
    case success(T)
    case failure(HttpResponse)
}

internal protocol TokenRenewProtocol {
    func renewToken(result: @escaping (Result<RezolveSession>) -> Void)
}

internal class AuthService: NSObject, TokenRenewProtocol {

    func renewToken(result: @escaping (Result<RezolveSession, AuthenticationError>) -> Void) {

        let urlString = ""
        let endpoint = "/api/v1/authentication/register"
        let data = ["username": "user@email.com", "password": "password"]

        guard let url = URL(string: urlString).appendPathComponent(endpoint),
              let request = URLRequest(url: url) else {
            preconditionFailure("Url not created, verify check path and host")
        }

        let device: DeviceProfile  = ... // The device profile 
        let entityId: String =  ... // Entity Id
        let partnerId: String = ... // Partner Id

        let dictionary: [String: String] = [
            "device_id": device.deviceId,
            "make": device.make,
            "os_type": device.osType,
            "os_version": device.osVersion,
            "locale": device.locale
            "entityId": entityId,
            "partnerId": partnerId
        ]

        guard let body = try JSONSerialization.data(withJSONObject: dictionary, options: []) else {
            preconditionFailure("Payload convertion to Data failed")
        }

        request.httpMethod = "POST"
        request.httpBody = body


        let config = URLSessionConfiguration.default
        let session = URLSession(configuration: config)
        let task = session.dataTask(with: urlRequest) { data, response, error in
                if let httpResponse = response as? HTTPURLResponse,
                    httpResponse.statusCode == 200 {
                    if let responseData = data,
                       let jsonDictionary = try? JSONSerialization.jsonObject(with: responseData, options: []) as? [String: Any] {
                        let accessToken = jsonDictionary["access_token"]
                        let publicKey =   jsonDictionary["public_key"]
                        // Securily store received keys
                        // Create new session with received accessToken
                        self.rezolveSdk?.createSession(
                            accessToken: accessToken,
                            entityId: entityId,
                            partnerId: partnerId, 
                            callback: { rezolveSession in
                                // Store new token

                                // Proccess callback
                                callback(.success(rezolveSession))
                        },  errorCallback: { error in
                                // Error handling

                                // Proccess callback
                                callback(.failure(error))
                        })
                       }
                    else {
                        // Handle parsing error
                    }
                }
                else {
                    // Handle error
                }
        }
    }
}

internal final class AddressBookViewController: UIViewController {

    fileprivate(set) var authService: (NSObjectProtocol & TokenRenewProtocol)?

    fileprivate func attemptToGetAddressBook(session: RezolveSession, retryOnFail: Bool) {
        session.addressbookManager.getAll(callback: { addressList in
            // Handle addressList

            }, errorCallback: { httpResponse in
                if retryOnFail && httpResponse.statusCode == 401 {
                    self.authService?.renewToken { result in
                        swith result {
                            case let .success(newSession):
                                // Retries to fetch data from AddressBookManager using the new RezolveSession
                                self.attemptToGetAddressBook(session: newSession, retryOnFail: false)

                            case let .failure(error):
                                // Handle Token Renewal failure
                                debugPrint(error)
                        }
                    }
                }
        })
    }
}
```
```java
// example Partner Auth Request Provider 
// this would handle partner user login against partner server, password reset,
// as well as JWT token renewal
class PartnerAuthRequestProvider implements RezolveSDK.AuthRequestProvider {
    private final AuthService authService;
    RuaAuthRequestProvider(AuthService authService) {
        this. authService = authService;
    }
    @Override
    public RezolveSDK.GetAuthRequest getAuthRequest() {
        if(Looper.myLooper() == Looper.getMainLooper()) {
            throw new IllegalStateException("You can't run this method from main thread");
        }
		
		//set blocking call as the refresh token callback
        final RefreshTokenCallbackToBlockingCall callback = new RefreshTokenCallbackToBlockingCall();
        authService.refreshAuthToken(callback);
		
		// ping the partner auth service
        RezolveSDK.GetAuthRequest authRequest = PartnerPingCallbackToBlockingCall.getResult();
        return authRequest;
    }
}

class RefreshTokenCallbackToBlockingCall {
    private RezolveSDK.GetAuthRequest result = null;
    private final CountDownLatch countDownLatch = new CountDownLatch(1);

	// on successful refresh, wait for the ping response 
    public void onRefreshAuthTokenSuccess(@NonNull String authToken) {
        result = RezolveSDK.GetAuthRequest.authorizationHeader(authToken);
        countDownLatch.countDown();
    }
	
	// getResult is only triggered after a result is received from the partner auth server
    RezolveSDK.GetAuthRequest getResult() {
        try {
            countDownLatch.await();
            return result;
        } catch (InterruptedException e) {
            // handle the exception
        }
    }
}
```

The Login JWT you generate is included in the headers of every SDK transmission. Thus, when your consumer logs out, you can expire the JWT, and the app will cease communication with the Rezolve server. To do this, create a new JWT with an expiration stamp in the past, and supply it to the SDK.

This also means you are required to handle JWT token expiration/renewal if you want a session to continue. 

Examples are provided to the right. These are NOT an example of implementing SDK code, but rather an example of implementing session rewnewal with your own authentication server. 

**IOS**

To handle session expiration smoothly, without interrupting the user session, Rezolve recommends creating a wrapper around any call that contacts the server. If the server returns an authorization error, you can call your token renewal endpoint and issue a new JWT. 

The sample shows two classes, an `AuthService` class and an `AddressBookViewController` class. 

The `AuthService` class may, for example, process username/passwords for login, handle registering your users, and handle password resets.  It should also handle the JSON Web Tokens to register, create session, and maintain session with Rezolve. The example method `renewJwtToken` is provided as an example of how to renew a token. 

The `AddressBookViewController` is an example of how you would implement the `AddressbookManager.getAll` method so as to smoothly handle token expiration and renewal to provide continuity of session. 

1. call the method `attemptToGetAddressBook` passing in a Rezolve session and the `retryOnFail` boolean
2. the method calls the `addressbookManager.getAll` method
3. if the call succeeds, proceed as normal, displaying the `addressList`
4. if the call results in an error, check if the error is a 401.
5. If the error is a 401, and the `retryOnFail` is true, request a new JWT, calling the `renewJwtToken` method. Then retry the request.
6. If the error is 401, and `retryOnFail` is false, we know that we requested a new token and the token provided was expired (indicating end of login session at auth server). Do not retry.
7. If a different error occurs, log and handle the error.



**Android**

The SDK makes every call to the Rezolve server using an http client; if a call to the server results in a "401 token expired" response, the http client will ask for a new token. The Partner Auth Service you passed in to the SDK Builder must handle this JWT renewal.

It should be noted that the Partner Auth Service will typically handle all partner auth needs. Duties may include processesing username/passwords for login, handling registering your users, and handling password resets, in addition to JWT renewal. 

The code examples show one way of implementing JWT renewal. 

In the class `PartnerAuthRequestProvider` the Partner Auth Service implements `RezolveSDK.AuthRequestProvider`, to handle the requirements of the SDK. If the http client receives a "401 token expired", it will call "RezolveSDK.GetAuthRequest" to either confirm logout or renew the token. The token is renewed, but is only returned if the ping to the partner auth server suceededs. If the partner auth server says the user is not logged in, the renewed token is not returned, and the user can no longer make requests.




### HTTP Error Responses 

#### 403

```
{
	"type": "Authentication"
	"code": "1"
	"message": "Access is denied."
}
```
For security reasons, Rezolve masks certain potential issues behind a generic 403 response:

- invalid partner api key
- partner doesn't have an auth key
- token not found
- invalid token signature
- token is missing required fields
- token belongs to another user


#### 401

```
{
	"type": "Expired Token"
	"code": "8"
	"message": "Your token has expired, refresh your token and try again."
}
```
If you receive this response, call your JWT renewal endpoint. 