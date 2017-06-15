# SDK Feature Use

This section describes the intended usage of the SDK to build specific feature-related functionalities.

## Basic Usage

```swift
import UIKit

// Import the following library:
import RezolveSDK

class RezolveSDK1ViewController: ViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // initialize SDK
        // possible values for RezolveEnv enum are .Development , .Sandbox, .Production
        let API_KEY: String  = "1234567890"
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: RezolveEnv.Sandbox)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()

    }
}
```
```java
// Import the following classes:
import com.rezolve.sdk.RezolveInterface;
import com.rezolve.sdk.RezolveSDK;
import com.rezolve.sdk.RezolveSession;
import com.rezolve.sdk.model.customer.DeviceProfile;
import com.rezolve.sdk.model.foreign.SignUpRequest;

// To handle server responses you will need to import interfaces or
// abstract classes from the following packages:
import com.rezolve.sdk.core.callbacks.*;
import com.rezolve.sdk.core.interfaces.*;


// Rezolve SDK operates on models from com.rezolve.sdk.model subpackages. 
// For example to handle a response from getMerchants query you will need 
// a Merchant model:
import com.rezolve.sdk.model.shop.Merchant;

// initialize SDK
// possible values for RezolveSDK.Env enum are .DEVELOPMENT , .SANDBOX, .PRODUCTION
String API_KEY = "1234567890";
RezolveSDK sdk = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.DEVELOPMENT);

```
```java
// Note: the SDK's manifest requests the following permissions:
// On Android 6.0+, you will have to specifically request the last two. 
// See https://developer.android.com/training/permissions/requesting.html
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.CAMERA" />

```

To get started, import the SDK into your code file.

The SDK must be initialized before use. When initializing the SDK, you must specify your `API Key`, and the `server environment` you are targeting.

Your `API Key` is supplied to you when you set up a developer account with Rezolve.

Your `server environment` is an enum. See values, right.


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
        RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
        .createSession(entityId, partnerId, deviceProfile, new RezolveInterface() {
        
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
		RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION).getRezolveSession()
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

For example, if you want to use `WalletManager` methods, you can either do so directly, or implement `WalletInterface` in your current activty for convenience. See Android code at right for comparison.

The Module Reference section exclusively documents using Feature Interfaces, as this is the preferred method.


##  User Management, Session Management

For the purpose of the SDK, it is assumed the partner has an existing community of consumers, and has a method of authenticating them in the partner app. It is further assumed the partner wishes to introduce their consumes to Rezolve capabilities. Each partner consumer that wishes to utilize Rezolve services will need a Rezolve account.

This section will describe how to:

1. Register a Rezolve user via the SDK
2. Create a Session for that user
3. When the user is done, log them out

<img src="images/loginsequencediagram.png" style="margin:6px 0;"><br/>[ <a href="images/loginsequencediagram.png">View full size</a> ]

### Register User

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

To register a user, you will need to call the sdk `registerUser` method, passing in an `SignUpRequest object`. The SignUpRequest object is defined as shown to the right. It is composed of a deviceProfile object, and four user data fields. All fields are strings.

The `registerUser` method is used as shown to the right. Upon successful registration, the response will contain a `partner_id` and an `entity_id`. **Persist both these values for the life of the app.**

<aside class="notice">
You will know if you need to call `registerUser` instead of `createSession` if either `partner_id` or `entity_id` are null or blank.
</aside>

<aside class="warning">
It is recommended that <code>entity_id</code> and <code>partner_id</code> be persisted to the partner server. If these values are stored server side, a consumer who uninstalls the app and reinstalls will be able to retain their history and settings. Without this info, their account cannot be reassociated after install.
</aside>


### Create Session

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

// Note: You don't need to create a new session if user navigatess to another activity. 
// After the session is created, you can access it by calling:
RezolveSession rezolveSession = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
.getRezolveSession();
```

To log in and interact with Rezolve services, you must establish a session. A session combines several functions, and abstracts them from the developer:

* Verifies the validity of the mobile app, through the API key
* Identifies the consumer and pairs them with a session token
* Verifies each request from the mobile app by passing API Key and session token
* establishes a public key for the session, for encrypting transmitted credit card info

Once the user is authenticated on the partner side, make a call to `createSession{}`, passing in the `entity_id` and a `deviceProfile` object. The server will respond with a session authentication token, and a session public key; the SDK takes care of managing these for the session. The auth token is used to authenticate each request from the client, while the public key is used for encrypting the transmission of payment card information for this session.

When the session is established, you can begin to access services, for example:
`session.CustomerProfileManager.get`


### Logout Session

```swift
// When session ends you should inform the sdk by calling
session.authenticationManager.logout();
```
```java
// When session ends you should inform the sdk by calling
rezolveSession.getAuthenticationManager().logout(entityId);
```
When a seession is over, you should notify the SDK by passing the `entity_id` to the logout method.

## Consumer Profile Management

Once logged in, you have access to the consumer's records. These include:
* Consumer Profile - Via the `ConsumerProfileManager`. Name, email, and device profile (phone info) for the consumer
* Address Book - Via the `AddressbookManager`. A collection of postal addresses, to be used for ship-to and bill-to purposes.
* Favorites - Via the `FavouriteManager`. A collection of devices that can be topped up.  A favorite can represent a mobile phone, a tollway transponder, or other device/account.
* Wallet - Via the `WalletManager`. Wallet lets you store credit card info securely, and lets the consumer maintain the list of cards. There can be multiple cards.

There are no specific flows to consider when managing the customer profile and assicated records.

AddressbookManager, FavouriteManager, and WalletManager support the following CRUD operations: `create`, `update`, `delete`, `getAll`, `get`.

ProfileManager supports only `update` and `get`.



## Shoppable Ads flow

<img src="images/ShoppableAddsFlow.png" style="margin:6px 0;"><br/>[ <a href="images/ShoppableAddsFlow.png">View full size</a> ]

The premise of Shoppable Ads is to capture an image scan (usually of an advertisement) using the Scan Manager, resolve it into a product URL, fetch the product info, and enable purchase via saved account information.

#### 1. Capture image and get product URL


```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface, View.OnClickListener {
    //...

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // set scan view
        setContentView(R.layout.scan_activity);
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        
        //get scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.PRODUCTION)
          .getRezolveSession().getScanManager(this, true);
          
          //start video scan to acquire image
          scanManager.startVideoScan(this, rezolveScanView);
    }

	// capture product result
    @Override
    public void onProductResult(Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        // ...etc
    }

}
```
First, initialize `scanManager`, and enable the scan screen using `session.startVideo()`, and capture a watermarked image. The scanManager will recognize the encoded product data, and extract `merchantId`, `catalogId`, and `productId` from the image, and automatically call `getProduct`. The scanManager will return a `product` object. 


#### 2. Create an order and get an order total

```swift
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


  mySession?.checkoutManager.checkoutOrder(cart: myCart) { (order: Order) in
      let orderId: String = order.id
      let finalPrice: Decimal = order.finalPrice
      // ... etc
  }
```
```java
// pull info from the product to build a checkoutProduct, and add it to the cart. 
HashMap<String, String> options = new HashMap<>();
options.put("color", "red");

CheckoutProduct checkoutProduct = new CheckoutProduct();
checkoutProduct.setId(product.getId());
checkoutProduct.setPrice(product.getPrice());
checkoutProduct.setTitle(product.getTitle());
checkoutProduct.setQty(5);
checkoutProduct.setOptions(options);

HashMap<String, String> deliverySettings = new HashMap<>();
deliverySettings.put("delivery_address_id", "123123");

HashMap<String, String> loyaltySettings = new HashMap<>();
loyaltySettings.put("loyalty_id", "123123");

HashMap<String, Double> geoLoc = new HashMap<>();
geoLoc.put("lat", 12.3123);
geoLoc.put("long", 31.1323);

List<CheckoutProduct> items = new ArrayList<>();
items.add(checkoutProduct);

// build the cart
Cart cart = new Cart.Builder()
        .merchantId(product.getMerchantId())
        .type("scan")
        .deliverySettings(deliverySettings)
        .loyaltySettings(loyaltySettings)
        .geoLoc(geoLoc)
        .items(items)
        .build();

// call checkoutOrder. It will return an Order with embedded pricing info.
rezolveSession.getCheckoutManager().checkoutOrder(cart, new CheckoutCallback() {
    @Override
    public void onCheckoutOrderSuccess(Order order) {
        String orderId = order.getOrderId();
        //handle result
    }
});
```

Once you have product information, call the SDK `checkoutOrder` method. Pass in the merchant, type, delivery address id, loyalty info (if any), geolocation if available, and finally the information on the desired product, including variant choices.  The response will include an order id, final total price, and a price breakdown (composed of base price, variant price premium if any, shipping, and tax). 

#### 3. Show payment card choices

```java
rezolveSession.getWalletManager().getAll(new WalletCallback() {
    @Override
    public void onWalletGetAllSuccess(List<PaymentCard> list) {
        // handle getAll response here
    }
});
```

If the consumer agrees with the price and wishes to complete the order, use `walletManager.getAll` to list the available card choices. The consumer will choose a payment card.

At this point, we recommend using a "slide to buy" button to confirm purchase intent, while preserving the maximum ease of use.

#### 4. Submit payment for order


```java
// Create an encrypted payment request using the CheckoutManager.createPaymentRequest
PaymentRequest paymentRequest = mySession.getCheckoutManager()
.createPaymentRequest( paymentCard, cvv );

// pass the paymentRequest object, order object, and callback to the buyOrder method
rezolveSession.getCheckoutManager().buyOrder(paymentRequest, order, new CheckoutCallback() {
    @Override
    public void onBuyOrderSuccess(Transaction transaction) {
        //handle result
    }
});
```

When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This will create the encrypted `paymentRequest` object needed for checkout.

Pass the `paymentRequst` object and the `order` object to the `buyOrder` method. The response will contain either a `transaction` object with order confirmation with receipt info, or if rejected, an error with the reason for the order rejection.







## Top Up flow

<img src="images/Topup%20Flow.png" style="margin:6px 0;"><br/>[ <a href="images/Topup%20Flow.png">View full size</a> ]

#### 1. Add the top up target as a Favorite

```java
Favourite favourite = new Favourite(value, type, provider);

rezolveSession.getFavouriteManager().create(favourite, new FavouriteCallback() {
    @Override
    public void onFavouriteCreateSuccess(Favourite favourites) {
    }
});

```
The top up flow gives the mobile consumer the ability to add money to a remote account that is linked with a specific device, such as a mobile phone or tollway transponder. The consumer must have first added the topup device (called a Favorite) to their account, using the `FavouriteManager.create` method.

#### 2. List available favorites

```java
rezolveSession.getFavouriteManager().getAll(new FavouriteCallback() {
    @Override
    public void onFavouriteGetAllSuccess(List<Favourite> favourites) {
        for(Favourite favourite : favourites) {
            String favouriteId = favourite.getId();
            // handle response
        }
    }
});
```
Once there is one or more favorites, use the `FavouriteManager.getAll` method to list them. The customer will choose a favorite.

#### 3. Get a list of topup amounts using getProducts

```java
rezolveSession.getProductManager().getProducts(merchant, catalog, count, 
pageIndex, sortBy, sortDirection, new ProductCallback() {
    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
        int count = pageResult.getCount();
        int total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> displayProducts = pageResult.getEmbedded();
        for(DisplayProduct displayProduct : displayProducts) {
            String title = displayProduct.getTitle();
            // fetch other params as needed
        }
    }
});

```
The customer next wants to see a list of possible top up amounts. Each top up amount is stored as a separate product with its own SKU, and these products are grouped into a catalog. To list the top up amounts, we use the `getProducts` method, passing in a fixed category id. 

Display the list of top up choices. You can display each "product" as a currency amount, or utilize the "description" field of the product to give more information about the topup. It depends on your application. The consumer will choose a top up amount.

#### 4. Get an order total

```swift
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


  mySession?.checkoutManager.checkoutOrder(cart: myCart) { (order: Order) in
      let orderId: String = order.id
      let finalPrice: Decimal = order.finalPrice
      // ... etc
  }
```
```java
rezolveSession.getCheckoutManager().checkoutOrder(cart, new CheckoutCallback() {
    @Override
    public void onCheckoutOrderSuccess(Order order) {
        String orderId = order.getOrderId();
        //handle result
    }
});
```
When you have the product choice, call the SDK `checkoutOrder` method. Pass in the merchant, type, delivery address id, loyalty info (if any), geolocation if available, and finally the information on the chosen product.  The response will include an order id, final total price, and a price breakdown (composed of base price, shipping, and tax).

#### 5. Show payment card choices

```java
rezolveSession.getWalletManager().getAll(new WalletCallback() {
    @Override
    public void onWalletGetAllSuccess(List<PaymentCard> list) {
        // handle getAll response here
    }
});
```
If the consumer agrees with the price and wishes to complete the order, use `walletManager.getAll` to list the available card choices. The consumer will choose a payment card.

At this point, we recommend using a "slide to buy" button to confirm purchase intent, while preserving the maximum ease of use.

#### 6. Submit payment for order

```java
// Create an encrypted payment request using the CheckoutManager.createPaymentRequest
PaymentRequest paymentRequest = mySession.getCheckoutManager()
.createPaymentRequest( paymentCard, cvv );

// pass the paymentRequest object, order object, and callback to the buyOrder method
rezolveSession.getCheckoutManager().buyOrder(paymentRequest, order, new CheckoutCallback() {
    @Override
    public void onBuyOrderSuccess(Transaction transaction) {
        //handle result
    }
});
```
When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This will create the encrypted `paymentRequest` object needed for checkout.

Pass the `paymentRequst` object and the `order` object to the `buyOrder` method. The response will contain either a `transaction` object with order confirmation with receipt info, or if rejected, an error with the reason for the order rejection.


# Error Handling

```json
{
  "errors":[
    {
      "type":"<error_type>",
      "code":"<error_code>",
      "message":"<error_message>"
    }
  ]
}
```
All SDK methods use a uniform error format, as shown at the right. Possible values are:

|Type|Error Code|Message|
|---|---|---|
|Authentication|1|Access is denied.|
|Registration|2|Registration failed.|
|Not Found|4|Requested resource not found|
|Bad Request|5|Incorrect or malformed parameters|


