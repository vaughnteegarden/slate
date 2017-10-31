## Module: CheckoutManager

Checkout is an aggregate of Session.

The checkout module handles the shopping cart, and creates, modifies, and completes orders with payment.

### Method: addProductToCart

```swift
TODO
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

Method signature: `session.addProductToCart( CheckoutProduct, String TOTO, [callback or interface] )`

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
TODO
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
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();


        // buy cart; returns an order id
        myCheckoutManager.buyCart(merchantId, cartId, addressId, rezolveLocation, paymentRequest, checkoutInterface);

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
    public void onProductOrderPlaced(String s) {
        String orderId = s;
    }

    @Override
    public void onCartOrderPlaced(String s) {
        String orderId = s;
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

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: buyProduct

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: checkoutCart

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: createPaymentRequest

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: getCartById

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: getCarts

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: removeProductFromCart

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: signOrderUpdates

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||



### Method: updateProductInCart

```swift
TODO
```
```java

```

methodname does something.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### objectname object

|field|format|example|
|---|---|---|
||||


<!-- ****************************************************************************-->
<!-- ****************************************************************************-->
<!-- ****************************************************************************-->


### Method: checkoutOrder

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

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


        self.mySession?.checkoutManager.checkoutOrder(cart: cart) { (order: Order) in

            // handle order
        }
    }
}
```
```java
// create an order and calculate cart cost
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // create a cart object for demo use
        // in actual use, pass an existing cart object
        HashMap<String, String> options = new HashMap<>();
        options.put("color", "red");

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        checkoutProduct.setId("123");
        checkoutProduct.setPrice(125);
        checkoutProduct.setTitle("Fitbit 2");
        checkoutProduct.setQty(5);
        checkoutProduct.setOptions(options);

        List<CheckoutProduct> items = new ArrayList<>();
        items.add(checkoutProduct);

        HashMap<String, Double> geoLoc = new HashMap<>();
        geoLoc.put("lat", 12.3123);
        geoLoc.put("long", 31.1323);

        HashMap<String, String> deliverySettings = new HashMap<>();
        deliverySettings.put("delivery_address_id", "123123");

        HashMap<String, String> loyaltySettings = new HashMap<>();
        loyaltySettings.put("loyalty_id", "123123");

        String merchantId = "123";

        Cart cart = new Cart.Builder()
                .merchantId(merchantId)
                .type("scan")
                .deliverySettings(deliverySettings)
                .loyaltySettings(loyaltySettings)
                .geoLoc(geoLoc)
                .items(items)
                .build();


        // create order and calculate cost
        myCheckoutManager.checkoutOrder(cart, this);

    }


    @Override
    public void onCheckoutOrderSuccess(Order order) {
        // get order info
        String orderId = order.getOrderId();
        float finalPrice = order.getFinalPrice();
        List<PriceBreakdown> breakdowns = order.getBreakdowns();
        //iterate price breakdown array
        for (PriceBreakdown breakdown : breakdowns ) {
            float amount = breakdown.getAmount();
            String type = breakdown.getType();
        }
    }
}
```

CheckoutOrder creates an order id and calculates the cost of the order.

Method signature: `session.checkoutOrder( cart, [callback or interface] )`

You must pass in a `cart` object.

The method returns an `order` object.

#### cart Object

|field|format|example|
|---|---|---|
|merchantId|string|&nbsp;|
|loyaltySettings|object|&nbsp;|
|deliverySettings|object|&nbsp;|
|type|string|&nbsp;|
|products|array of checkoutProduct objects|&nbsp;|
|geoLoc|Object|&nbsp;|

#### loyaltySettings Object

|field|format|example|
|---|---|---|
|loyalty_id|string|123123|

#### deliverySettings Object

|field|format|example|
|---|---|---|
|delivery_address_id|string|14|

#### checkoutProduct Object

|field|format|example|
|---|---|---|
|productId|string|12345c|
|productTitle|string|Fitbit Charge 2|
|quantity|decimal|1|
|finalPrice|decimal|159.00|
|options|map of OptionValue objects|&nbsp;|

See <a href="#optionvalue-object">`optionValue object`</a>.

#### geoLoc Object

|field|format|example|
|---|---|---|
|lat|double|51.5237737|
|long|double|-0.1585369|

#### order Object

|field|format|example|
|---|---|---|
|id|string|123|
|finalPrice|decimal|129.00|
|breakdown|array of priceBreakdown objects|&nbsp;|

#### priceBreakdown Object

>Example breakdown object:

```json
  "breakdown": [
    { "type": "main", "amount": 129.00  },
    { "type": "shipping", "amount": 10.00  },
    { "type": "tax", "amount": 10.00  },
    { "type": "option_variant", "amount": 10.00  },
  ]
```

|field|format|example|
|---|---|---|
|type|string|tax|
|amount|decimal|10.00|

### Method: createPaymentRequest

```swift
self.mySession?.checkoutManager.createPaymentRequest(paymentCard: paymentCard, cvv: cvv)
```
```java
PaymentCard card = new PaymentCard(); // in actual use, pass in an existing card object
String cvv = "123";
PaymentRequest paymentRequest = myCheckoutManager.createPaymentRequest(card, cvv);
```

Creates an encrypted paymentRequest object to be used with buyOrder.

Method signature: `session.createPaymentRequest( paymentCard, cvv, [callback or interface] )`

You must pass in a <a href="#paymentcard-object">`paymentCard object`</a> and a `cvv` string.

#### paymentRequest object

|field|format|example|
|---|---|---|
|paymentCard|object|(encrypted)|
|cvv|string|(encrypted)|

### Method: buyOrder

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.checkoutManager.checkoutOrder(cart: cart) { (order: Order) in

            let paymentCard = ... // initialize paymentCard
            let cvv = ... // capture cvv

            let paymentRequest: PaymentRequest = 
            self.mySession?.checkoutManager.createPaymentRequest( paymentCard: paymentCard, 
            cvv: cvv)

            self.mySession?.checkoutManager.buyOrder(paymentRequest: paymentRequest,
             order: order) { (transaction: Transaction) in
                //handle result
            }
        }
    }
}
```
```java
public class Checkout extends AppCompatActivity implements CheckoutInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        CheckoutManager myCheckoutManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getCheckoutManager();

        // buy Order
        // first create the payment request from card and cvv
        // in actual use, pass in an existing card object
        PaymentCard card = new PaymentCard();
        String cvv = "123";
        PaymentRequest paymentRequest = myCheckoutManager.createPaymentRequest(card, cvv);
        
        // then submit the order
        // in actual use, pass in an existing order object
        Order order = new Order();
        myCheckoutManager.buyOrder(paymentRequest, order, this);
    }

    @Override
    public void onBuyOrderSuccess(Transaction transaction) {
    	// get transaction info
        Map<String, String> deliverySettings = transaction.getDeliverySettings();
        float finalPrice = transaction.getFinalPrice();
        Map<String, Double> geoLoc = transaction.getGeoLoc();
        List<TransactionItem> items = transaction.getItems();
        Map<String, String> loyaltySettings = transaction.getLoyaltySettings();
        String merchantId = transaction.getMerchantId();
        String pan4 = transaction.getPan4();
        String payWith = transaction.getPayWith();
        String status = transaction.getStatus();
        String timestamp = transaction.getTimestamp();
        String type = transaction.getType();

        // iterate to get delivery settings
        for (String key : deliverySettings.keySet()) {
            String label = key;
            String value = deliverySettings.get(key);
        }
        // iterate to get loyalty settings
        for (String key : loyaltySettings.keySet()) {
            String label = key;
            String value = loyaltySettings.get(key);
        }
        // iterate to get transaction items
        for (TransactionItem item : items){
            String itemId = item.getId();
            String itemTitle = item.getTitle();
            String qty = item.getQty();
            String price = item.getPrice();
            String imageUrl = item.getImageUrl();
        }
    }
}
```

BuyOrder references an order id and supplies payment for the order.

Method signature: `session.buyOrder( paymentRequest, order, [callback or interface] )`

You must pass in a <a href="#paymentrequest-object">`paymentRequest object`</a>, and the <a href="#order-object">`order object`</a> you want to provide payment for.

The method returns a <a href="#transaction-object">`transaction object`</a>.

### Method: signOrderUpdates

```swift
import UIKit
import RezolveSDK

class CheckoutViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        let order = ... // initialize order

        self.mySession?.checkoutManager.signOrderUpdate(order: order, 
         callback: { (orderUpdate: OrderUpdate) in

            // handle the updates
        })
    }
}
```

In environments where transactions can take a very long time (minutes to tens of minutes) to return a status, this method provides a way to subscribe and be notified of transaction status updates.

Method signature: `session.buyOrder( entity_id, order_id, [callback or interface] )`

This method is reserved for future functionality.






