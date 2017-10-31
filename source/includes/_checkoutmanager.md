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
    String merchantId;
    RezolveLocation rezolveLocation;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();


        // add product to cart
        myCheckoutManager.addProductToCart(checkoutProduct, merchantId, checkoutInterface);
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

Method signature: `session.CheckoutManager.addProductToCart( CheckoutProduct, String TOTO, [callback or interface] )`

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    String addressId;
    String cartId;
    String merchantId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();


        // buy cart; returns an order id
        myCheckoutManager.buyCart(merchantId, cartId, addressId, 
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

Method signature: `session.CheckoutManager.buyCart( MerchantId, CartId, AddressId, 
        RezolveLocation, PaymentRequest, [callback or interface] )`

You must pass in strings MerchantId, CartId, AddressId, a RezolveLocation object, a PaymentRequest object, and a CheckoutInterface (or callback).

The method returns an `OrderId` string.

#### RezolveLocation object

|field|format|example|
|---|---|---|
|latitude|double|38.9072|
|longitude|double|-77.0369|
|locationType|string (must be POINT)|POINT|

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
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";
    String addressId;
    String merchantId;
    RezolveLocation rezolveLocation;
    PaymentRequest paymentRequest;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // buy product directly, bypassing cart; returns an order id
        myCheckoutManager.buyProduct(merchantId, checkoutProduct, addressId, 
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

Method signature: `session.buyProduct( MerchantId, CheckoutProduct, AddressId, 
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
    String addressId;
    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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
    String addressId;
    String merchantId;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // checkout product; returns an Order object with price breakdown
        myCheckoutManager.checkoutProduct(merchantId, checkoutProduct, 
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

Method signature: `session.CheckoutManager.checkoutProduct( MerchantId, CheckoutProduct, AddressId,  [callback or interface] )`

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
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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
    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

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
    String addressId;
    String cartId;
    String merchantId;
    CheckoutInterface checkoutInterface;
    CheckoutProduct checkoutProduct;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // removes a product from cart: returns a CartDetails object
        myCheckoutManager.removeProductFromCart(cartId, merchantId,
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

Method signature: `session.CheckoutManager.removeProductFromCart( CartId, MerchantId, CheckoutProduct, [callback or interface] )`

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
    String merchantId;
    String orderId;
    CheckoutInterface checkoutInterface;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // sign order updates
        myCheckoutManager.signOrderUpdates(orderId, merchantId, checkoutInterface);
        // this is done automatically when there's a success response to buyProduct or buyCart call.
        // this method is exposed in case the developer wants to listen for updates using a background service


    }

    @Override
    public void onOrderUpdateReceived(Transaction.Status status, Transaction transaction) {
        // get properties of status object
        String statusLabel = status.getLabel();

        // get properties of transaction object
        Order transAmount = transaction.getAmount();
        List<TransactionItem> transItems = transaction.getItems();
        String transLastUpdated = transaction.getLastUpdated();
        String transOrderId = transaction.getOrderId();
        String transStatus = transaction.getStatus();
        String transTimestamp = transaction.getTimestamp();

        // get properties of Order object
        String transOrderAmount = transAmount.getOrderId();
        float transOrderFinalPrice = transAmount.getFinalPrice();
        List<PriceBreakdown> transOrderBreakdowns = transAmount.getBreakdowns();

        // get properties of transItems array
        for(TransactionItem transactionItem : transItems){
            Address addressDetails = transactionItem.getAddressDetails();
            CustomerDetails customerDetails = transactionItem.getCustomerDetails();
            RezolveLocation location = transactionItem.getLocation();
            List<CheckoutProduct> products = transactionItem.getProducts();

            // get properties of addressDetails object
            String id = addressDetails.getId();
            String shortName = addressDetails.getShortName();
            String city = addressDetails.getCity();
            String country = addressDetails.getCountry();
            String line1 = addressDetails.getLine1();
            String line2 = addressDetails.getLine2();
            String state = addressDetails.getState();
            String zip = addressDetails.getZip();

            // get properties of customerDetails object
            String customerDetailsId = customerDetails.getCustomerId();
            String customerDetailsEmail = customerDetails.getEmail();
            String customerDetailsFirstName = customerDetails.getFirstName();
            String customerDetailsLastName = customerDetails.getLastName();
            String customerDetailsLoyaltyCardNo = customerDetails.getLoyaltyCardNumber();
            Boolean customerDetailsHasLoyalty = customerDetails.isHasLoyaltyCard();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

methodname does something.

Method signature: `session.CheckoutManager.signOrderUpdates( OrderId, MerchantId, [callback or interface] )`

You must pass in an OrderId as a string, a MerchantId as string, and a CheckoutInterface (or callback).

The method returns a `Transaction` object, and a `Transaction.Status` object.

#### Transaction object

|field|format|example|
|---|---|---|
|transactionAmount|Order object|&nbsp;|
|transactionItems|array of TransactionItem objects|&nbsp;|
|transactionLastUpdated|date string in YYYY-MM-DD format|2017-10-31|
|transactionOrderId|string|abc123|
|transactionStatus|string|Complete|
|transactionTimestamp|date string in YYYY-MM-DD format|2017-10-28|

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
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // update product(s) in cart: returns a CartDetails object
        myCheckoutManager.updateProductInCart(cartId, merchantId, 
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

Method signature: `session.CheckoutManager.updateProductInCart( CartId, MerchantId, CheckoutProduct, CheckoutProduct2, [callback or interface] )`

You must pass in a `Cartid` as a string, a `MerchantId` as a string, a CheckoutProduct object (representing the object you want to change), a second CheckoutProduct object (with the updated values), and a checkoutInterface (or callback).

The method returns a `CartDetails` object.

#### objectname object

|field|format|example|
|---|---|---|
||||




