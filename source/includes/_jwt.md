## Implementing JWT Authentication on Your Authentication Server

As of release 1.6.0, the Rezolve SDK no longer includes an authentication system. Resultingly, the `AuthenticationManager.register` and `AuthenticationManager.logout`  methods have been deprecated.

Instead, Rezolve is utilizing a server-to-server JWT authentication system, conformant with the **https://tools.ietf.org/html/rfc7519** standard. If you are not familar with Jason Web Tokens, the site **https://jwt.io/** provides an excellent primer on the use of JWTs, as well as links to various JWT libraries you can utilize.

Rezolve expects the primary authentication of users will happen outside the SDK. Thus, exactly how you implement authentication in your app will depend on your existing auth system. In the server-to-server realm, however, there is only one instance in which your authentication server must interact with the Rezolve server. 

After that, whether or not the SDK can talk to Rezolve depends on supplying a valid JWT to the SDK from your auth system.

When the Partner creates a new user on their auth server, or wishes to associate an existing user with Rezolve for the first time, the partner must generate the Registration JWT, and then POST it to the `/api/v1/authentication/register` endpoint. The Rezolve server will validate the JWT, create a new user, create the user's public/private key pair, and return to you the Rezolve EntityId. 

When a user logs in to your auth system, generate a new Login JWT and supply to CreateSession in the SDK. As long as the JWT is valid, the SDK can talk to Rezolve. A method is suggested below for smoothly handling JWT timeouts without interrupting the SDK session. 





### JWT Flow

<img src="images/GenericSWT.png" style="margin:6px 0;"><br/>[ <a href="images/GenericSWT.png" target="_blank">View full size</a> ]





### Create the Registration JWT

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

Set type as JWT, and algorithm as HMAC using SHA-512.


#### JWT Payload

```JSON
{
	"rezolve_entity_id": ":NONE:",
	"partner_entity_id": "partner_entity_id",
	"exp": 1520869470
}
```

For the `rezolve_entity_id`, use the value `:NONE:`.

For the `partner_entity_id`, set it to the unique identifier for your user record. This may be a numerical id, or an identifying string such as email address.

For `exp`, set the expiration value to a small number, now() + 30 minutes or less. Expiration should be expressed as a unix timestamp integer. 



#### Signature

```JSON
HMACSHA512(
	base64UrlEncode(header) + "." +
	base64UrlEncode(payload),
	${$partner_auth_key}
)
```
Sign the header and payload with the `partner_auth_key`.

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
email: user@example.com
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

Once a Rezolve User has been registered and an `entity_id` obtained, you can log in the user. Log them in via your normal method in your auth server. Then create a new Login JWT. Use this JWT when creating the Rezolve Session in the SDK.

#### JWT Header

Note the addition of the "auth" line.

```JSON
{
	"auth": "v2",
	"alg": "HS512",
	"typ": "JWT"
}
```

#### JWT Payload

```JSON
{
	"rezolve_entity_id": "entity123",
	"partner_entity_id": "partner_entity_id",
	"exp": 1520869470
}
```
For the `rezolve_entity_id`, use the entity_id you obtained during registration.

For the `partner_entity_id`, set it to the unique identifier for your user record. 

For `exp`, set the expiration value to a small number, now() + 30 minutes or less. Expiration should be expressed as a unix timestamp integer. 


#### Signature

```JSON
HMACSHA512(
	base64UrlEncode(header) + "." +
	base64UrlEncode(payload),
	${$partner_auth_key}
)
```
Sign the header and payload with the `partner_auth_key`.

#### Create the Session

```swift
TODO

```
```java
String API_KEY = "your_api_key";
String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";
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




### Handling JWT Expiration & Session Preservation

```swift
TODO
```
```java
// handle JWT expiration without ending session
private void attemptToGetAddressBook(final RezolveSession session, final boolean retryOnFail) {
	session.getAddressbookManager().getAll(new AddressbookCallback() {
		@Override
		public void onAddressbookGetAllSuccess(List<Address> addresses) {
			setAddresses(addresses);
		}
		@Override
		public void onFailure(HttpResponse httpResponse) {
			// TODO - create generic example
			// Show getting a new JWT
			// Show setting new JWT in Session Header. 
			// Anything else?
		}
	});
}
```

The Login JWT you generate is included in the headers of every SDK transmission. Thus, when your consumer logs out, you can expire the JWT, and the app will cease communication with the Rezolve server. To do this, create a new JWT with an expiration stamp in the past, and supply it to the SDK.

However, this also means you are required to handle JWT token expiration/renewal. To do this smoothly without interrupting the user session, Rezolve recommends creating a wrapper around any call that contacts the server. If the server returns an authorization error, you can call your token renewal endpoint and issue a new JWT. 

Here is an example, using the getAddressBook method of the AddressBookManager class. Note the use of a boolean to only retry once; this is to prevent continual retries in the event of a purposeful logout.  




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