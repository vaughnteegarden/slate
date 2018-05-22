## Module: CheckoutManager

**Topics**

* <a href="#method-checkoutbundle-createcartcheckoutbundle">CheckoutBundle.CreateCartCheckoutBundle</a>
* <a href="#method-checkoutbundle-createcartinstorepickupcheckoutbundle">CheckoutBundle.CreateCartInStorePickupCheckoutBundle</a>
* <a href="#method-checkoutbundle-createproductcheckoutbundle">CheckoutBundle.CreateProductCheckoutBundle</a>
* <a href="#method-checkoutbundle-createproductinstorepickupcheckoutbundle">CheckoutBundle.CreateProductInStorePickupCheckoutBundle</a>
* <a href="#method-checkoutmanager-createpaymentrequest">CheckoutManager.createPaymentRequest</a>
* <a href="#method-checkoutmanager-addproducttocart">CheckoutManager.addProductToCart</a>
* <a href="#method-checkoutmanager-buycart">CheckoutManager.buyCart</a>
* <a href="#method-checkoutmanager-buyproduct">CheckoutManager.buyProduct</a>
* <a href="#method-checkoutmanager-checkoutcart">CheckoutManager.checkoutCart</a>
* <a href="#method-checkoutmanager-checkoutproduct">CheckoutManager.checkoutProduct</a>
* <a href="#method-checkoutmanager-getcarts">CheckoutManager.getCarts</a>
* <a href="#method-checkoutmanager-getcartbyid">CheckoutManager.getCartById</a>
* <a href="#method-checkoutmanager-removeproductfromcart">CheckoutManager.removeProductFromCart</a>
* <a href="#method-checkoutmanager-signorderupdates">CheckoutManager.signOrderUpdates</a>
* <a href="#method-checkoutmanager-updateproductincart">CheckoutManager.updateProductInCart</a>

Checkout is an aggregate of Session.

The checkout module handles the shopping cart, and creates, modifies, and completes orders with payment.

This section also covers the use of CheckoutBundle class, to create the CheckoutBundles used by CheckoutManager. The method used to create the checkout bundle  determines the order flow for checkout and purchase. For example, if you create a checkout bundle using `.CreateCartCheckoutBundle`, the consumer is prompted to for the cart, and choose a shipping address. If you instead use `.CreateCartInStorePickupCheckoutBundle`, the consumer will need to choose their in-store-pickup options. 



### Method: CheckoutBundle.CreateCartCheckoutBundle

```swift
import RezolveSDK
import Foundation

internal final class Controller: NSObject { 

	func createBundle() {

		let address = Address()
		// Set up address object
		address.shortName = "..."
		address.line1 = "..."
		address.line2 = "..."
		address.city = "..."
		address.state = "..."
		address.zip = "..."
		address.country = "..."

		let cartDetails = CartDetails(
			id: "...", 
			merchantId: "...", 
			dateCreated: "...",
			dateUpdated: "...", 
			products: [...]
		)

		let checkoutBundle = createCartCheckoutBundle(
			cartDetails: cartDetails,
			merchantId: cartDetails.merchantId,
			address: address
		)
	}
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        String merchantId = "123";
        String cartId = "123";
        String addressId = "123";

        // create a cart checkout bundle
        CheckoutBundle checkoutBundle = CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId );
    }
}
```

CreateCartCheckoutBundle creates a cart checkout bundle for standard home delivery.

Method signature: `session.CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId )`

You must pass in the `cartId`, the `merchantId`, and `addressId`, all as strings.

The method returns an `CheckoutBundle` object.


### Method: CheckoutBundle.CreateCartInStorePickupCheckoutBundle

```swift
import RezolveSDK
import Foundation

internal final class Controller: NSObject { 

	func createBundle() {

		let address = StoreAddress(
			street1 = "...",
			street2 = "...",
			region = "...",
			postCode = "...",
			country = "...",
			city = "..."
		)

		let cartDetails = CartDetails(
			id: "...", 
			merchantId: "...", 
			dateCreated: "...",
			dateUpdated: "...", 
			products: [...]
		)

		let shippingMethod = ShippingMethod(
			carrierCode: "",
			methodCode: "",
			displayName: "",
			extensionAttribute: [](),
			store: nil
		)

		let checkoutBundle = createCartInStorePickupCheckoutBundle(
			cartDetails: cartDetails,
			merchantId: cartDetails.merchantId,
			shippingMethod: shippingMethod,
			storeAddress: address
		)
	}
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        String cartId = "123";
        String merchantId = "123";
        ShippingMethod shippingMethod = new ShippingMethod();
        StoreAddress storeAddress = new StoreAddress();

        // create a pick up in store checkout bundle
        CheckoutBundle.createCartInStorePickupCheckoutBundle( cartId, merchantId, shippingMethod, storeAddress);
    }
}
```

CreateCartInStorePickupCheckoutBundle creates a cart checkout bundle for in store pickup. 

Method signature: `session.CheckoutBundle.createCartInStorePickupCheckoutBundle( cartId, merchantId, shippingMethod, storeAddress);`

You must pass in the `cartId` and `merchantId` as strings, a `ShippingMethod` object, and a `StoreAddress` object.

The method returns an `CheckoutBundle` object.




### Method: CheckoutBundle.CreateProductCheckoutBundle

```swift
import RezolveSDK
import Foundation

internal final class Controller: NSObject { 

	func createBundle() {

		let address = Address()
		// Set up address object
		address.shortName = "..."
		address.line1 = "..."
		address.line2 = "..."
		address.city = "..."
		address.state = "..."
		address.zip = "..."
		address.country = "..."

		let merchantId = "..." // Merchant id

		let checkoutProduct = CheckoutProduct(
			id: 0, 
			qty: 10, 
			productPlacement: ProductPlacement(adID: "", placementID: ""), 
			configurableOptions: []()], 
			customOptions: []()
		)

		let checkoutBundle = createProductInStorePickupCheckoutBundle(
			merchantId: merchantId,
			address: shippingMethod,
			checkoutProduct: address
		)
	}
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        String cartId = "123";
        String merchantId = "123";

		// create a product checkout bundle for standard home delivery
        CheckoutBundle.createProductCheckoutBundle( merchantId, addressId, checkoutProduct );
    }
}
```

CreateProductCheckoutBundle creates a product checkout bundle for standard home delivery.

Method signature: `session.CheckoutBundle.createProductCheckoutBundle( merchantId, addressId, checkoutProduct )`

You must pass in the `merchantId` and `addressId` as strings, and a  `CheckoutProduct` object.

The method returns an `CheckoutBundle` object.




### Method: CheckoutBundle.CreateProductInStorePickupCheckoutBundle

```swift
import RezolveSDK
import Foundation

internal final class Controller: NSObject { 

	func createBundle() {

		let address = StoreAddress(
			street1 = "...",
			street2 = "...",
			region = "...",
			postCode = "...",
			country = "...",
			city = "..."
		)

		let cartDetails = CartDetails(
			id: "...", 
			merchantId: "...", 
			dateCreated: "...",
			dateUpdated: "...", 
			products: [...]
		)

		let shippingMethod = ShippingMethod(
			carrierCode: "",
			methodCode: "",
			displayName: "",
			extensionAttribute: [](),
			store: nil
		)

		let checkoutProduct = CheckoutProduct(
			id: 0, 
			qty: 10, 
			productPlacement: ProductPlacement(adID: "", placementID: ""), 
			configurableOptions: []()], 
			customOptions: []()
		)

		let merchantId = "..." // Merchant id string

		let checkoutBundle = createProductInStorePickupCheckoutBundle(
				merchantId: merchantId,
				shippingMethod: shippingMethod,
				storeAddress: address,
				checkoutProduct: checkoutProduct
		)
	}
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        String cartId = "123";
        ShippingMethod shippingMethod = new ShippingMethod();
        StoreAddress storeAddress = new StoreAddress();

        // create a product checkout bundle for pick up in store
        CheckoutBundle.createProductInStorePickupCheckoutBundle( merchantId, shippingMethod, storeAddress, checkoutProduct );
    }
}
```

CreateProductInStorePickupCheckoutBundle creates a product checkout bundle for in store pickup.

Method signature: `session.CheckoutBundle.createProductInStorePickupCheckoutBundle( merchantId, shippingMethod, storeAddress, checkoutProduct )`

You must pass in the `merchantId` as a string, a `ShippingMethod` object, a `StoreAddress` object, and a `CheckoutProduct` object.

The method returns an `CheckoutBundle` object.



### Method: CheckoutManager.createPaymentRequest

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) i
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
              let address: Address = createAddress()
              session.addressbookManager.create(address: address) { (remoteAddress: Address) in
                  let card: PaymentCard = createPaymentCard(addressId: remoteAddress.id)
                  session.walletManager.create(paymentCard: card, callback: { (remoteCard: PaymentCard) in
                      let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)
                      let paymentRequest = session.checkoutManager.createPaymentRequest(paymentCard: remoteCard, cvv: CVV)
                  }, validationErrorCallback: { print($0) })
              }
            }, errorCallback: { print($0) })
        })
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        PaymentCard paymentCard = new PaymentCard();
        String cvv = "123";

        // create a payment request
        PaymentRequest paymentRequest = checkoutManager.createPaymentRequest( paymentCard, cvv);

    }
}
```

CreatePaymentRequest is a utility method that creates a PaymentRequest object.

Method signature: `session.CheckoutManager.createPaymentRequest( PaymentCard, CVV, [callback or interface] )`

You must pass in a `PaymentCard` object, and a `CVV` as a string. Use WalletManager to get a PaymentCard object.

The method returns an `PaymentRequest` object.




### Method: CheckoutManager.addProductToCart

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

              session.productManager.getProduct(merchantId: MERCHANT_ID, categoryId: CATEGORY_ID, productId: PRODUCT_ID, callback: { (remoteProduct: Product) in

                   let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)

                   session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct, callback: { cartDetails in



                   }, errorCallback: { print($0) })

               }, errorCallback: { print($0) })

            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {

        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!

        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        String merchantId = "123";

        // add a product to the cart
        checkoutManager.addProductToCart(checkoutProduct, merchantId, this);
    }

    @Override
    public void onAddProductsToCartSuccess(CartDetails cartDetails) {
        String dateCreated = cartDetails.getDateCreated();
        String dateUpdated = cartDetails.getDateUpdated();
        String merchantId = cartDetails.getMerchantId();
        List<CheckoutProduct> products = cartDetails.getProducts();

        // extract product info from array of CheckoutProduct objects
        for (CheckoutProduct checkoutProduct : products ) {
            List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();
            int id = checkoutProduct.getId();
            Placement productPlacement = checkoutProduct.getProductPlacement();
            float qty = checkoutProduct.getQty();

            // extract configurable options
            for (ConfigurableOption configurableOption : configurableOptions) {
                String code = configurableOption.getCode();
                int value = configurableOption.getValue();
            }

            // extract product placement
            String adId = productPlacement.getAdId();
            String placementId = productPlacement.getPlacementId();
        }
    }
}
```

Adds a product to a cart.

Method signature: `session.CheckoutManager.addProductToCart(checkoutProduct, merchantId, [callback or interface] )`

You must pass in a `CheckoutProduct` object, the merchantId as a string, and an interface or callback.

The method returns an `CartDetails` object.


#### CartDetails object

|field|format|example|
|---|---|---|
|dateCreated|ISO8601 date string|2017-09-05T07:45:56.692Z|
|dateUpdated|ISO8601 date string|2017-09-05T07:45:56.692Z|
|merchantId|string|123|
|products|array of CheckoutProduct objects|&nbsp;|

#### CheckoutProduct object

|field|format|example|
|---|---|---|
|configurableOptions|array of ConfigurableOption objects|&nbsp;|
|id|integer|123|
|productPlacement|ProductPlacement object|&nbsp;|
|qty|float|123|

#### ConfigurableOption object

|field|format|example|
|---|---|---|
|code|string|abc123|
|value|int|123|

#### ProductPlacement object

|field|format|example|
|---|---|---|
|adId|string|abc123|
|placementId|string|abc123|


### Method: CheckoutManager.buyCart

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
		
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
              let address: Address = createAddress()

              session.addressbookManager.create(address: address) { (remoteAddress: Address) in
                  let card: PaymentCard = createPaymentCard(addressId: remoteAddress.id)

                  session.walletManager.create(paymentCard: card, callback: { (remoteCard: PaymentCard) in
                      let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)
                      let paymentRequest = session.checkoutManager.createPaymentRequest(paymentCard: remoteCard, cvv: CVV)

                      session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct, callback: { cartDetails in
                          session.checkoutManager.buyCart(merchantId: MERCHANT_ID, cart: cartDetails, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, callback: { (order: CheckoutOrder) in
                          }, errorCallback: { print($0) })
                      }, errorCallback: { print($0) })
                  })
              }
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        String cartId = "123";
        String merchantId = "123";
        String addressId = "123";
        String phonebookId = "123";
        RezolveLocation rezolveLocation = new RezolveLocation();
        PaymentCard paymentCard = new PaymentCard();
        String cvv = "123";

        // create a payment request
        PaymentRequest paymentRequest = checkoutManager.createPaymentRequest( paymentCard, cvv);
		
        // create a cart checkout bundle for standard home delivery
        CheckoutBundle checkoutBundle = CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId );

        // buy the contents of a cart
        checkoutManager.buyCart(checkoutBundle, phonebookId, rezolveLocation, paymentRequest, this );
    }

    @Override
    public void onCartOrderPlaced(String merchantId, String orderId) {
        // show order confirmation
    }
}
```

BuyCart sends payment information to purchase the contents of a cart.

Method signature: `session.CheckoutManager.buyCart(checkoutBundle, phonebookId, rezolveLocation, paymentRequest, [callback or interface] )`

You must pass in a `CheckoutBundle` object, the `phonebookId` as a string, a `RezolveLocation` object, a `PaymentRequest` object, and an interface or callback.

The method returns a `merchantId` and an `orderId`. 

#### RezolveLocation object

|field|format|example|
|---|---|---|
|latitude|double|38.9072|
|longitude|double|-77.0369|

#### PaymentRequest object

|field|format|example|
|---|---|---|
|PaymentCard|object|(see <a href="#module-walletmanager">WalletManager</a>)|
|CVV|string|345|






### Method: CheckoutManager.buyProduct

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
                let address: Address = createAddress()
                session.addressbookManager.create(address: address) { (remoteAddress: Address) in
                    let card: PaymentCard = createPaymentCard(addressId: remoteAddress.id)
                    session.walletManager.create(paymentCard: card, callback: { (remoteCard: PaymentCard) in
                        let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)
                        let paymentRequest = session.checkoutManager.createPaymentRequest(paymentCard: remoteCard, cvv: CVV)
                        session.checkoutManager.buyProduct(merchantId: MERCHANT_ID, checkoutProduct: checkoutProduct, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, callback: { (order: CheckoutOrder) in
                        }, errorCallback: { print($0) })
                    })
                }
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();


        CheckoutProduct checkoutProduct = new CheckoutProduct();
        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        RezolveLocation rezolveLocation = new RezolveLocation();
        PaymentCard paymentCard = new PaymentCard();
        String cvv = "123";

        // create a product checkout bundle for standard home delivery
        CheckoutBundle.createProductCheckoutBundle(cartId, merchantId, checkoutProduct);

        // create a payment request
        PaymentRequest paymentRequest = checkoutManager.createPaymentRequest( paymentCard, cvv);

        // buy a single product (identical to buy cart?)
        checkoutManager.buyProduct(checkoutBundle, phonebookId, rezolveLocation, paymentRequest, this);

    }

    @Override
    public void onProductOrderPlaced(String merchantId, String orderId) {
        // show order confirmation
    }
}
```

BuyProduct sends payment information to purchase a single product.

Method signature: `session.CheckoutManager.buyProduct(checkoutBundle, phonebookId, rezolveLocation, paymentRequest, [callback or interface] )`

You must pass in a `CheckoutBundle` object, the `phonebookId` as a string, a `RezolveLocation` object, a `PaymentRequest` object, and an interface or callback.

The method returns a `merchantId` and an `orderId`. 



### Method: CheckoutManager.checkoutCart

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
                let address: Address = createAddress()
                session.addressbookManager.create(address: address) { (remoteAddress: Address) in
                    let card: PaymentCard = createPaymentCard(addressId: remoteAddress.id)
                    session.walletManager.create(paymentCard: card, callback: { (remoteCard: PaymentCard) in
                        let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)
                        session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct, callback: { cartDetails in
                            session.checkoutManager.checkoutCart(cart: cartDetails, address: remoteAddress, callback: { order in
                            }, errorCallback: { print($0) })
                        }, errorCallback: { print($0) })
                    }, validationErrorCallback: { print($0) })
                }
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        String cartId = "123";
        String merchantId = "123";
        String addressId = "123";

        // create a cart checkout bundle for standard home delivery
        CheckoutBundle checkoutBundle = CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId );

        //  get order id and totals for a cart
        checkoutManager.checkoutCart( checkoutBundle, this );
    }

    @Override
    public void onCheckoutCartSuccess(Order order) {
        // extract order details
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        float finalPrice = order.getFinalPrice();
        String orderId = order.getOrderId();

        // extract price breakdowns
        for (PriceBreakdown priceBreakdown : breakdowns ) {
            float amount = priceBreakdown.getAmount();
            String type = priceBreakdown.getType();
        }
    }
}
```

CheckoutCart generates order totals, including shipping and tax, based on the contents of the cart. 

Method signature: `session.CheckoutManager.checkoutCart( checkoutBundle, [callback or interface] )`

You must pass in a `CheckoutBundle` object, and an interface or callback.

The method returns an `Order` object that includes final price and order id.

#### Order object

|field|format|example|
|---|---|---|
|breakdowns|array of PriceBreakdown objects|&nbsp;|
|finalPrice|float|499.95|
|orderId|string|abcd1234|

#### PriceBreakdown object

|field|format|example|
|---|---|---|
|amount|float|49.01|
|type|string|tax|





### Method: CheckoutManager.checkoutProduct

```swift
import RezolveSDK
import Foundation

internal final class ViewController: UIViewController {

    let rezolveSdk: RezolveSDK?

    func checkoutProduct() {

        let checkoutBundle = createProductCheckoutBundle(...) // Create checkout bundle

        self.rezolveSdk?.checkout(
            bundle: checkoutBundle,
            callback: { order: Order in 
                print(order.finalPrice)
                order.breakdown.forEach {
                    print($0.type)
                    print($0.amount)
                }
                guard let settings =  order.settings else { return }
                print(settings.paymentMethod)
                print(settings.shippingAddress)
            },
            errorCallback: { _ in 
                // Handle error
            }
        )
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        String cartId = "123";
        String merchantId = "123";
        String addressId = "123";

        // create a cart checkout bundle for standard home delivery
        CheckoutBundle checkoutBundle = CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId );

        //  get order id and totals for a single product
        checkoutManager.checkoutProduct(checkoutBundle,this);
    }

    @Override
    public void onCheckoutProductSuccess(Order order) {
        // extract order details
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        float finalPrice = order.getFinalPrice();
        String orderId = order.getOrderId();

        // extract price breakdowns
        for (PriceBreakdown priceBreakdown : breakdowns ) {
            float amount = priceBreakdown.getAmount();
            String type = priceBreakdown.getType();
        }
    }
}
```

CheckoutProduct generates order totals, including shipping and tax, for a single product. 

Method signature: `session.CheckoutManager.checkoutProduct( checkoutBundle, [callback or interface] )`

You must pass in a `CheckoutBundle` object, and an interface or callback.

The method returns an `Order` object that includes final price and order id.



### Method: CheckoutManager.getCarts

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
                session.cartManager.getCarts(callback: { carts in

                }, errorCallback: { print($0) })
            }, errorCallback: { print($0) })
        })
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // gets carts for the current session: returns an array of CartDetail objects
        myCheckoutManager.getCarts(checkoutInterface);
    }

    @Override
    public void onGetCartsSuccess(List<CartDetails> successCartDetails) {
        // get properties of successCartDetails array
        for(CartDetails cartDetails: successCartDetails){
            String cartDateCreated = cartDetails.getDateCreated();
            String cartDateUpdated = cartDetails.getDateUpdated();
            String cartId = cartDetails.getId();
            String cartMerchantId = cartDetails.getMerchantId();
            List<CheckoutProduct> cartProducts = cartDetails.getProducts();

            // get properties of cartProducts array
            for(CheckoutProduct checkoutProduct:cartProducts){
                int checkoutProductId = checkoutProduct.getId();
                List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();
                float checkoutProductQty = checkoutProduct.getQty();

                // get properties of configurableOptions array
                for(ConfigurableOption configurableOption:configurableOptions){
                    String configurableOptionCode = configurableOption.getCode();
                    int configurableOptionValue = configurableOption.getValue();
                }
            }
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

GetCarts gets a list of carts associated with the user.

Method signature: `session.CheckoutManager.getCarts( [callback or interface] )`

You must pass in a `CheckoutInterface` or callback.

The method returns an array of `CartDetail` objects.



### Method: CheckoutManager.getCartById

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
              session.cartManager.getCart(merchantId: MERCHANT_ID, id: cartDetails.id, callback: { remoteCart in

              }, errorCallback: { print($0) })
            }, errorCallback: { print($0) })
        })
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // get cart by id: returns a CartDetails object
        myCheckoutManager.getCartById(cartId, merchantId, checkoutInterface);
    }


    @Override
    public void onGetCartByIdSuccess(CartDetails cartDetails) {
        // get properties of cartDetails array
        String cartDateCreated = cartDetails.getDateCreated();
        String cartDateUpdated = cartDetails.getDateUpdated();
        String cartId = cartDetails.getId();
        String cartMerchantId = cartDetails.getMerchantId();
        List<CheckoutProduct> cartProducts = cartDetails.getProducts();

        // get properties of cartProducts array
        for(CheckoutProduct checkoutProduct:cartProducts){
            int checkoutProductId = checkoutProduct.getId();
            List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();
            float checkoutProductQty = checkoutProduct.getQty();

            // get properties of configurableOptions array
            for(ConfigurableOption configurableOption:configurableOptions){
                String configurableOptionCode = configurableOption.getCode();
                int configurableOptionValue = configurableOption.getValue();
            }
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

GetCardById returns the contents of a cart, as specified by cartId.

Method signature: `session.CheckoutManager.getCartById( CartId, MerchantId, [callback or interface] )`

You must pass in a `Cartid` string, a `MerchantId` string, and a `CheckoutInterface` or callback.

The method returns an `CartDetails` object.




### Method: CheckoutManager.removeProductFromCart

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
              session.productManager.getProduct(merchantId: MERCHANT_ID, categoryId: CATEGORY_ID, productId: PRODUCT_ID, callback: { (remoteProduct: Product) in
                session.productManager.getProduct(merchantId: MERCHANT_ID, categoryId: CATEGORY_2, productId: PRODUCT_2, callback: { (remoteProduct2: Product) in
                  let checkoutProduct1 = createCheckoutProductWithVariant(product: remoteProduct)
                  let checkoutProduct2 = createCheckoutProductWithVariant(product: remoteProduct2)
                  session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct1, callback: { cartDetails1 in
                    session.cartManager.addCartProduct(merchantId: MERCHANT_ID, cart: cartDetails1, product: checkoutProduct2, callback: { cartDetails2 in
                      session.cartManager.removeCartProduct(merchantId: MERCHANT_ID, cart: cartDetails2, product: checkoutProduct1, callback: { remoteCart in
                      }, errorCallback: { print($0) })
                    }, errorCallback: { print($0) })
                  }, errorCallback: { print($0) })
                }, errorCallback: { print($0) })
              }, errorCallback: { print($0) })
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        String cartId = "123";
        String merchantId = "123";

        // remove a product from a cart
        checkoutManager.removeProductFromCart(cartId, merchantId, checkoutProduct, this);
    }

    @Override
    public void onRemoveProductFromCartSuccess(CartDetails cartDetails) {
        String dateCreated = cartDetails.getDateCreated();
        String dateUpdated = cartDetails.getDateUpdated();
        String merchantId = cartDetails.getMerchantId();
        List<CheckoutProduct> products = cartDetails.getProducts();

        // extract product info from array of CheckoutProduct objects
        for (CheckoutProduct checkoutProduct : products ) {
            List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();
            int id = checkoutProduct.getId();
            Placement productPlacement = checkoutProduct.getProductPlacement();
            float qty = checkoutProduct.getQty();

            // extract configurable options
            for (ConfigurableOption configurableOption : configurableOptions) {
                String code = configurableOption.getCode();
                int value = configurableOption.getValue();
            }

            // extract product placement
            String adId = productPlacement.getAdId();
            String placementId = productPlacement.getPlacementId();
        }
    }
}
```

RemoveProductFromCart removes a previously added product from the specified cart.

Method signature: `session.CheckoutManager.removeProductFromCart( CartId, MerchantId, CheckoutProduct, [callback or interface] )`

You must pass in `CartId` as a string, `MerchantId` as a string, the `CheckoutProduct` to remove, and a `CheckoutInterface` or callback.

The method returns a `CartDetails` object.




### Method: CheckoutManager.signOrderUpdates

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
                let address: Address = createAddress()
                session.addressbookManager.create(address: address) { (remoteAddress: Address) in
                    let card: PaymentCard = createPaymentCard(addressId: remoteAddress.id)
                    session.walletManager.create(paymentCard: card, callback: { (remoteCard: PaymentCard) in
                        let checkoutProduct = createCheckoutProductWithVariant(product: remoteProduct)
                        let paymentRequest = session.checkoutManager.createPaymentRequest(paymentCard: remoteCard, cvv: CVV)
                        session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct, callback: { cartDetails in
                            session.checkoutManager.buyCart(merchantId: MERCHANT_ID, cart: cartDetails, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, callback: { (order: CheckoutOrder) in
                                session.checkoutManager.signOrderUpdate(merchantId: MERCHANT_ID, order: order, callback: { status, transaction in
                                    print(status)
                                    if status == .completed {
                                    }
                                })
                            }, errorCallback: { print($0) })
                        }, errorCallback: { print($0) })
                    })
                }
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    String merchantId;
    String orderId;
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // sign order updates
        myCheckoutManager.signOrderUpdates(orderId, merchantId, checkoutInterface);
        // this is done automatically when there's a success response to buyProduct or buyCart call.
        // this method is exposed in case the developer wants to listen for updates using a background service


    }

    @Override
    public void onOrderUpdateReceived(Transaction.Status status, Transaction transaction) {
        // get properties of status object
        String statusLabel = status.getLabel();   // will return one of: completed, canceled, or processing

        // get properties of transaction object
        String transStatus = transaction.getStatus();
        String transOrderId = transaction.getOrderId();
        String transLastUpdated = transaction.getLastUpdated();
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

This method is called automatically if `buyCart` or `buyProduct` return successfully. SignOrderUpdates starts a listener on a socket, waiting for the server to return one of the following order status:
* completed
* canceled
* processing

If the orders status is "completed" or "canceled", the listener will close. If the order status returns as "processing", the listener will remain open until an update of either "completed" or "canceled" is received.

Method signature: `session.CheckoutManager.signOrderUpdates( OrderId, MerchantId, [callback or interface] )`

You must pass in an `OrderId` as a string, a `MerchantId` as string, and a `CheckoutInterface` or callback.

The method returns a `Transaction` object, and a `Transaction.Status` object.

#### Transaction object

|field|format|example|
|---|---|---|
|transactionLastUpdated|date string in YYYY-MM-DD format|2017-10-31|
|transactionOrderId|string|abc123|
|transactionStatus|string|Complete|

#### Order object

|field|format|example|
|---|---|---|
|orderFinalPriceOrderId|string|123|
|orderFinalPriceFinalPrice|float|123.45|
|orderFinalPriceBreakdowns|array of PriceBreakdown objects|&nbsp;|




### Method: CheckoutManager.updateProductInCart

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "..."
let CATEGORY_ID = Int32(100)
let PRODUCT_ID = "..."

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)
        let signUpRequest = createSingUpRequest()
        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in
              session.productManager.getProduct(merchantId: MERCHANT_ID, categoryId: CATEGORY_ID, productId: PRODUCT_ID, callback: { (remoteProduct: Product) in
                session.productManager.getProduct(merchantId: MERCHANT_ID, categoryId: CATEGORY_2, productId: PRODUCT_2, callback: { (remoteProduct2: Product) in
                  let checkoutProduct1 = createCheckoutProduct(product: remoteProduct)
                  let checkoutProduct2 = createCheckoutProduct(product: remoteProduct2)
                  session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct1, callback: { cartDetails1 in
                    session.cartManager.addCartProduct(merchantId: MERCHANT_ID, cart: cartDetails1, product: checkoutProduct2, callback: { cartDetails2 in
                      let newC = cartDetails2.products[0]
                      newC.qty = 20
                      let newC2 = cartDetails2.products[1]
                      newC2.qty = 40
                      session.cartManager.updateProductInCart(merchantId: MERCHANT_ID, cart: cartDetails2, oldProduct: checkoutProduct1, newProduct: newC, callback: { remoteCart in

                      }, errorCallback: { print($0) })
                    }, errorCallback: { print($0) })
                  }, errorCallback: { print($0) })
                }, errorCallback: { print($0) })
              }, errorCallback: { print($0) })
            }, errorCallback: { print($0) })
        })
    }

    func createCheckoutProductWithVariant(product: Product, qty: Decimal = 1.0 ) -> CheckoutProduct {
        let configurableOptions = (product.optionAvailable.first?.combination.map { $0.configurableOption() })!
        return CheckoutProduct(id: product.id, qty: qty, configurableOptions: configurableOptions, customOptions: [])
    }
}
```
```java
public class Checkout3 extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager checkoutManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManager();

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        CheckoutProduct checkoutProduct2 = new CheckoutProduct();
        String cartId = "123";
        String merchantId = "123";

        // update a product in a cart
        // first checkoutProduct is the one to update, second checkoutProduct is what to update it to
        checkoutManager.updateProductInCart(cartId, merchantId, checkoutProduct, checkoutProduct2, this);
    }

    @Override
    public void onUpdateProductInCartSuccess(CartDetails cartDetails) {
        String dateCreated = cartDetails.getDateCreated();
        String dateUpdated = cartDetails.getDateUpdated();
        String merchantId = cartDetails.getMerchantId();
        List<CheckoutProduct> products = cartDetails.getProducts();

        // extract product info from array of CheckoutProduct objects
        for (CheckoutProduct checkoutProduct : products ) {
            List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();
            int id = checkoutProduct.getId();
            Placement productPlacement = checkoutProduct.getProductPlacement();
            float qty = checkoutProduct.getQty();

            // extract configurable options
            for (ConfigurableOption configurableOption : configurableOptions) {
                String code = configurableOption.getCode();
                int value = configurableOption.getValue();
            }

            // extract product placement
            String adId = productPlacement.getAdId();
            String placementId = productPlacement.getPlacementId();
        }
    }
}
```

UpdateProductInCart modifies a CheckoutProduct object that is already in the cart.

Method signature: `session.CheckoutManager.updateProductInCart( CartId, MerchantId, CheckoutProduct, CheckoutProduct2, [callback or interface] )`

You must pass in a `Cartid` as a string, a `MerchantId` as a string, a `CheckoutProduct` object (the product you want to change), a second `CheckoutProduct` object (with the updated values), and a `CheckoutInterface` or callback.

The method returns a `CartDetails` object.
