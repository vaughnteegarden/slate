# SDK Feature Use

This section describes the usage of the SDK to build specific feature-related functionalities.





## Android-specific instructions on Managers

```java

// 	THE FOLLOWING TWO METHODS ARE EQUIVALENT, but Interface saves effort

//
// Using MANAGER, you must handle the response in a WalletCallback:
//

public class MyActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
 		...
        rezolveSDK = new RezolveSDK.Builder()
           .setApiKey(API_KEY)
           .setEnv(ENVIRONMENT)
           .setAuthRequestProvider(new PartnerAuthRequestProvider(AuthService.getInstance()))
           .build();

            rezolveSDK.setAuthToken(accessToken);

            rezolveSDK.createSession( accessToken, entityId, partnerId, new RezolveInterface() {


            @Override
            public void onInitializationSuccess(RezolveSession rezolveSession, String 
            entityId, String partnerId) {
                rezolveSession.getWalletManager().getAll(new WalletCallback() {

                    @Override
                    public void onWalletGetAllSuccess(List<PaymentCard> list) {

                    // handle getAll response here
                        for(PaymentCard paymentCard : list) {
                            String cardId = paymentCard.getId();
                            String expiresOn = paymentCard.getExpiresOn();
                            // ...etc
                    	}
                    }
                });
            }
        });
    }
}

//
// Using INTERFACE, you can save some development time
//

public class MyActivity extends AppCompatActivity implements WalletInterface {
	...
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// .getRezolveSession gets an already created session
		RezolveSDK.getInstance().getRezolveSession()
        .getWalletManager().getAll(this);
	}

	@Override
	public void onWalletGetAllSuccess(List<PaymentCard> list) {
		// handle getAll response here
		for(PaymentCard paymentCard : list) {
    		String cardId = paymentCard.getId();
			String expiresOn = paymentCard.getExpiresOn();
            // ...etc
		}
	}
}
```

In the Android version of the Rezolve Inside<sup>TM</sup> SDK, each FeatureManager has an equivalent FeatureInterface.

For example, to use `WalletManager` methods, you can either do so directly, or implement `WalletInterface` in your current activity for convenience. See Android code at right for comparison.

The Module Reference section exclusively documents using Feature Interfaces, as this is the preferred method.

## Automatic thumbnail generation

> Example JSON for 1 large image, and 4 associated "thumbnail" images

```JSON
      "image": "https:\/\/s3.amazonaws.com\/1\/27\/Image.png",
      "image_thumbs": [
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_400x550.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_800x1100.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_1200x1651.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_1600x2201.png"
      ],
```

When large images are uploaded to the merchant portal, the portal automatically generates a series of smaller images, which are referred to as thumbnails even though some are fairly large. The rules for thumbnail generation are as follows:

* Generated images respect the aspect ratio of the original image; their height/width ratio will not be distorted
* If the uploaded image is: 
	* smaller or equal to 400px maximum dimension (width or height), no resized image is produced.
	* larger than 400px maximum dimension, it makes a 400px max dimension image. (referred to as the "thumbnail" image)
    * larger than 800px maximum dimension, it makes a 800px max dimension image, and all smaller sizes.
    * larger than 1200px maximum dimension, it makes a 1200px max dimension image, and all smaller sizes.
	* larger than 1600px maximum dimension, it makes a 1600px max dimension image, and all smaller sizes.
* Additionally, the original upload image always returned, even if gigantic. Consider if you need images larger than 1600x1600 in your app.
* This applies to all merchant uploaded images that are supplied to the SDK. 

Each uploaded image will have a corresponding array of thumbnails. If an object can have an array of images, it will have an array of thumbnail arrays.


##  User Management Introduction

For the purpose of the SDK, it is assumed the partner has an existing community of consumers, and has a method of authenticating them in the partner app. It is further assumed the partner wishes to introduce their consumers to Rezolve capabilities. Each partner consumer that wishes to utilize Rezolve services will need a Rezolve account.

The Rezolve SDK has no built-in authentication mechansim, but that is not to say there is no security. Rezolve is utilizing a server-to-server JWT authorization system, conformant with the **https://tools.ietf.org/html/rfc7519** standard. If you are not familar with JSON Web Tokens, the site **https://jwt.io/** provides an excellent primer on the use of JWTs, as well as links to various JWT libraries you can utilize.

Exactly how you implement authentication in your app will depend on what authorization system you use. You may either:

- Utilize an existing authentication server, and adapt it to serve the JWT required by Rezolve. This makes most sense if you already have a community of users with logins.

- Utilize Rezolve User Authentication (RUA), our implementation of a JWT-aware auth system. If you do not have an existing auth system, adopting RUA may save development time, as opposed to implementing a new system from the ground up. Your Rezolve sales representative can help you with this decision.

The primary task in implementing user management is to create the JWT that will allow you to bind a Partner user to a Rezolve profile. Through a server-to-server transaction, you can create a new Rezolve User Profile, and get back an identifying `entityId` that you should persist in your authentication database, as an addition to your user record. This `entityId` will then be used by the Rezolve SDK to identify the user to the Rezolve Server. 

A valid JWT is required to initialize the SDK and start a sesssion. 


## Minimum Implementation

As a developer, you may be thinking "There is a lot of documentation here. What is the minimum feature set I need to implement to make a technical demo?" 

It depends on what you want to demonstrate, but the typical demonstration of Rezolve's offering looks like:

**Scan Engagement**&nbsp;&nbsp;&nbsp;>>>&nbsp;&nbsp;&nbsp;**View Product**&nbsp;&nbsp;&nbsp;>>>&nbsp;&nbsp;&nbsp;**Instant Buy**

This section walks you through the *minimum* implementation required to build a limited, functional, end-to-end demo. Several shortcuts are identified. These shortcuts are not appropriate for an app processing real transactions, but are acceptible for a technical demonstration on the Sandbox server. The shortcuts guide you to create a demo with limited UI elements, and only a single user. You are of course free to implement as many of the features as you wish to demonstrate. 

### 1. Authentication

Two things must be in place before the SDK can communicate with the server

1. You must have an `API Key` and `Partner Id`, and be must talking to the correct `Environment`.  This information was provided to you when you signed up for API Access.
2. You must have a valid `JSON Web Token`, or `JWT`. A `JWT Secret` was provided when you signed up for API Access, and is used to create the `JWT`. 

Examples of both authenticaton measures are shown under **<a href="#jwt-authentication">JWT Authentication</a>** .

<aside class="notice">

<b>Shortcut</b>

<p>New developers sometimes get stuck on #2, JWT Authentication, as it is somewhat complex and requires a server-to-server integration. For a technical trial, instead of modifying your auth server, you can take a partial shortcut:

<p>At <a href="#create-the-registration-jwt">Create the Registration JWT</a>, there are links to an online JWT debugger, epoch converter, and a zip file. The zip file contains screenshots showing how to use these tools in conjunction with <a href="https://www.getpostman.com/" target="_blank">Postman</a> to manually create and use JSON Web Tokens.  You must create the <code>Registration JWT</code>, and then create the <code>Login JWT</code>, as documented and get your <code>entity_id</code>.  But when creating the <code>Login JWT</code>, create one with an expiration date well in the future, 6 months from the current date.  You can then use this <code>JWT</code> and the corresponding <code>entity_id</code> in your code while you test. This frees you from doing full auth server integration for a simple technical trial.

</aside>

Be aware this is UNSAFE for production, or even pilot use. For any rollout involving real consumers and real purchases, you must do the auth server integration, so JWT tokens are tied properly to login status for a single user, and tokens can be renewed and expired.

### 2. Profile Building Blocks

The end consumer is represented by a Profile. Attached to this profile are one or more addresses, one or more phone numbers, and one or more payment devices. To create these objects, you must implement:

* `ConsumerProfileManager` - and create a profile record
* `AddressbookManager` - and create an address record
* `PhonebookManager` - and create a phone record.
* `WalletManager` - and add a credit card. (we recommend using VISA `4111 1111 1111 1111` for testing)

During the scan and purchase flow, ids for profile, address, phone, and payment device will be needed. You will get these by querying the above managers to get to get the ids of the records you created.

See **<a href="#consumer-profile-management">Consumer Profile Management</a>**. 

<aside class="notice">

<b>Shortcut</b>

<p>Normally, you would create a UI for each of these managers, allowing the consumer to enter their information.

<p>For a technical demo, you might forgo creating an UI.  Instead, after authentication, to a GET to each manager, and if incomplete or null results are returned, create the needed record from hard coded data. 

</aside>

### 3. The Scan Interface

You must implement `ScanManager`, and all it's methods. 

You will also need `ProductManager`, to get and parse the retrieved product. 

See the example at **<a href="#product-scan-instant-buy-flow">Product Scan, Instant Buy Flow</a>**.


### 4. Checkout and Payment

Checkout and payment rely upon the following managers. 

* `PaymentOptionsManager`
* `CheckoutManagerV2` 

You must at least stub all the methods of these managers. But you may notice there are some methods specific to cart, and some specific to product. For instant buy, you only need to fully implement the PRODUCT methods. 

<aside class="notice">

<b>Shortcut</b>

<p>It is recommended you display full product info after the scan, and implement a slide-to-buy control to make the purchase. However, you might skip the interactive UI where the consumer would normally choose quantity, shipping address, and payment card, and have these choices hard coded instead. 

<p>Note that a simple product requires only a choice of quantity, so these are easiest to make a demo with.

</aside>

See the example at **<a href="#product-scan-instant-buy-flow">Product Scan, Instant Buy Flow</a>**.

### Conclusion

The above steps outline the absolute shortest route to a fully functional tech demonstraton. Please follow them, and embrace the shortcuts provided, to create your tech demo in the minimal time. 

But be aware that the Minimum Implementation is suitable for a Technical Demo only. Any application that will be given to consumers and used to make real purchases must fully implement JWT Auth, a proper Consumer UI, and included features omitted from this section, like Mall, Shopping Cart, Multi-Cart, and more.  

The <a href="#reference-app">Rezolve Reference App</a> is a great example of a full-featured implementation. If haven't used it yet, request access and give it a try.  

