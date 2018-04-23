## Module: CheckoutManager

Checkout is an aggregate of Session.

The checkout module handles the shopping cart, and creates, modifies, and completes orders with payment.

### Method: addProductToCart

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String merchantId;
    RezolveLocation rezolveLocation;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();


        // add product to cart
        myCheckoutManager.addProductToCart(this, checkoutProduct, merchantId, checkoutInterface);
    }


    @Override
    public void onAddProductsToCartSuccess(CartDetails cartDetails) {
        // get properties of cartDetails object
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

AddProductToCart adds a product to the cart for a particular merchant.

IOS Method signature: `session.CheckoutManager.addProductToCart( CheckoutProduct, String merchantId, [callback or interface] )`

Android Method signature: `session.CheckoutManager.addProductToCart( Context, CheckoutProduct, String merchantId, [callback or interface] )`

You must pass in a `CheckoutProduct` object, the merchantId as a string, and a `CheckoutInterface` object (or callback).

The method returns an `CartDetails` object.

#### CheckoutProduct object

|field|format|example|
|---|---|---|
|checkoutProductId|int|123|
|checkoutProductQty|float|1|
|configurableOptions|array of configurableOption objects|@nbsp;|

#### ConfigurableOption object

|field|format|example|
|---|---|---|
|configurableOptionCode|string|Logo|
|configurableOptionValue|int|123|

#### CartDetails object
|field|format|example|
|---|---|---|
|cartDateCreated|date string in format YYYY-MM-DD|2017-12-25|
|cartDateUpdated|date string in format YYYY-MM-DD|2017-12-25|
|cartId|string|abc123|
|cartMerchantId|string|abc123|
|cartProducts|array of CheckoutProduct objects|&nbsp;|





### Method: buyCart

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

                          session.checkoutManager.buyCart(merchantId: MERCHANT_ID, cart: cartDetails, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, phone: phone, callback: { (order: CheckoutOrder) in


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
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String cartId;
    String merchantId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;
    String phonebookId;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();


        // buy cart; returns an order id
        myCheckoutManager.buyCart(merchantId, cartId, addressId, phonebookId,
        rezolveLocation, paymentRequest, checkoutInterface);

    }

    @Override
    public void onCartOrderPlaced(String s) {
        String orderId = s;
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

BuyCart sends payment information to the backend to purchase the contents of a cart. 

If this method returns successfully, it will automatically call `signOrderUpdate` and start listening on a socket for the server to return an order status. 

Method signature: `session.CheckoutManager.buyCart( MerchantId, CartId, AddressId, 
        RezolveLocation, PaymentRequest, [callback or interface] )`

You must pass in strings MerchantId, CartId, AddressId, a RezolveLocation object, a PaymentRequest object, and a CheckoutInterface (or callback).

The method returns an `OrderId` string.

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





### Method: buyProduct

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

                        session.checkoutManager.buyProduct(merchantId: MERCHANT_ID, checkoutProduct: checkoutProduct, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, phone: phone, callback: { (order: CheckoutOrder) in



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
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String merchantId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;
    String phonebookId;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // buy product directly, bypassing cart; returns an order id
        myCheckoutManager.buyProduct(this, merchantId, checkoutProduct, addressId, phonebookId,
        rezolveLocation, paymentRequest, checkoutInterface);
    }

    @Override
    public void onProductOrderPlaced(String s) {
        String orderId = s;
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}

```

BuyProduct purchases a product directly, bypassing the cart.

If this method returns successfully, it will automatically call `signOrderUpdate` and start listening on a socket for the server to return an order status.

IOS Method signature: `session.buyProduct( MerchantId, CheckoutProduct, AddressId, 
        RezolveLocation, PaymentRequest, [callback or interface] )`

Android Method signature: `session.buyProduct( Context, MerchantId, CheckoutProduct, AddressId, 
        RezolveLocation, PaymentRequest, [callback or interface] )`

You must pass in MerchantId and AddressId as strings, a CheckoutProduct Object, a RezolveLocation object, a PaymentRequest object, and a CheckoutInterface (or callback). 

The method returns an `OrderId` string.




### Method: checkoutCart

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // checkout cart: returns an Order object with price breakdown
        myCheckoutManager.checkoutCart(cartId,merchantId, addressId, checkoutInterface);
    }

    @Override
    public void onCheckoutCartSuccess(Order order) {
        // get properties of order array
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        float finalPrice = order.getFinalPrice();
        String orderId = order.getOrderId();

        // get properties of PriceBreakdown array
        for (PriceBreakdown priceBreakdown : breakdowns) {
            float breakdownAmount = priceBreakdown.getAmount();
            String breakdownType = priceBreakdown.getType();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}

```

CheckoutCart requests updated pricing, including shipping and tax, based on the contents of the cart.

Method signature: `session.CheckoutManager.checkoutCart( CartId, MerchantId, AddressId, [callback or interface] )`

You must pass in CartId, MerchantId, and AddressId as strings, and a CheckoutInterface (or callback).

The method returns an `Order` object with PriceBreakdown information.

#### Order object

|field|format|example|
|---|---|---|
|breakdowns|array of PriceBreakdown objects|&nbsp;|
|finalPrice|float|19.95|
|orderId|string|abc123|

#### PriceBreakdown object

|field|format|example|
|---|---|---|
|breakdownAmount|float|14.95|
|breakdownType|string enum, one of (MAIN, UNIT, SHIPPING, OPTION_VARIANT, TAX, or DISCOUNT)|TAX|




### Method: checkoutProduct

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

                        session.checkoutManager.checkoutProduct(merchantId: MERCHANT_ID, checkoutProduct: checkoutProduct, address: remoteAddress, callback: { (order: CheckoutOrder) in


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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String merchantId;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // checkout product; returns an Order object with price breakdown
        myCheckoutManager.checkoutProduct(this, merchantId, checkoutProduct, 
        addressId, checkoutInterface);
    }

    @Override
    public void onCheckoutProductSuccess(Order order) {
        // get properties of order array
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        float finalPrice = order.getFinalPrice();
        String orderId = order.getOrderId();

        // get properties of PriceBreakdown array
        for (PriceBreakdown priceBreakdown : breakdowns) {
            float breakdownAmount = priceBreakdown.getAmount();
            String breakdownType = priceBreakdown.getType();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

CheckoutProduct requests pricing, including shipping and tax, for a single product.

IOS Method signature: `session.CheckoutManager.checkoutProduct( MerchantId, CheckoutProduct, AddressId,  [callback or interface] )`

Android Method signature: `session.CheckoutManager.checkoutProduct( Context, MerchantId, CheckoutProduct, AddressId,  [callback or interface] )`

You must pass in a MerchantId string, a CheckoutProduct object, an AddressId string, and a CheckoutInterface (or callback).

The method returns an `Order` object with PriceBreakdown information.




### Method: createPaymentRequest

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

                  }, validationErrorCallback: { print($0) })
              }

            }, errorCallback: { print($0) })
        })
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String cartId;
    String cvv;
    String merchantId;
    String orderId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;
    CheckoutProduct checkoutProduct2;
    PaymentCard paymentCard;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // create a payment request prior to buying product or cart
        myCheckoutManager.createPaymentRequest(paymentCard, cvv);
    }
}
```

CreatePaymentRequest is a utility method that creates a PaymentRequest object.

Method signature: `session.CheckoutManager.createPaymentRequest( PaymentCard, CVV, [callback or interface] )`

You must pass in a `PaymentCard` object, and a `CVV` as a string.

The method returns an `PaymentRequest` object.




### Method: getCartById

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
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

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

You must pass in a `Cartid` string, a `MerchantId` string, and a CheckoutInterface (or callback).

The method returns an `CartDetails` object.




### Method: getCarts

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
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

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

You must pass in a `CheckoutInterface` (or callout).

The method returns an array of `CartDetail` objects.





### Method: removeProductFromCart

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // removes a product from cart: returns a CartDetails object
        myCheckoutManager.removeProductFromCart(this, cartId, merchantId,
        checkoutProduct, checkoutInterface);
    }


    @Override
    public void onRemoveProductFromCartSuccess(CartDetails cartDetails) {
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

RemoveProductFromCart removes a previously added product from the specified cart.

IOS Method signature: `session.CheckoutManager.removeProductFromCart( CartId, MerchantId, CheckoutProduct, [callback or interface] )`

Android Method signature: `session.CheckoutManager.removeProductFromCart( Context, CartId, MerchantId, CheckoutProduct, [callback or interface] )`

You must pass in CartId as a string, MerchantId as a string, the CheckoutProduct to remove, and a CheckoutInterface (or callback).

The method returns a `CartDetails` object.






### Method: signOrderUpdates

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
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

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

You must pass in an OrderId as a string, a MerchantId as string, and a CheckoutInterface (or callback).

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





### Method: updateProductInCart

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    String addressId;
    String cartId;
    String cvv;
    String merchantId;
    String orderId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;
    CheckoutProduct checkoutProduct2;
    PaymentCard paymentCard;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getCheckoutManager();

        // update product(s) in cart: returns a CartDetails object
        myCheckoutManager.updateProductInCart(this, cartId, merchantId, 
        checkoutProduct, checkoutProduct2, checkoutInterface);
    }


    @Override
    public void onUpdateProductInCartSuccess(CartDetails cartDetails) {
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

UpdateProductInCart modifies a CheckoutProduct object that is already in the cart.

IOS Method signature: `session.CheckoutManager.updateProductInCart( CartId, MerchantId, CheckoutProduct, CheckoutProduct2, [callback or interface] )`

Android Method signature: `session.CheckoutManager.updateProductInCart( Context, CartId, MerchantId, CheckoutProduct, CheckoutProduct2, [callback or interface] )`

You must pass in a `Cartid` as a string, a `MerchantId` as a string, a CheckoutProduct object (representing the object you want to change), a second CheckoutProduct object (with the updated values), and a checkoutInterface (or callback).

The method returns a `CartDetails` object.




