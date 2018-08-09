## Module: CheckoutManagerV2

**Topics**

* <a href="#method-checkoutbundlev2-createcartcheckoutbundlev2">CheckoutBundleV2.CreateCartCheckoutBundleV2</a>
* <a href="#method-checkoutbundlev2-createproductcheckoutbundlev2">CheckoutBundleV2.CreateProductCheckoutBundleV2</a>
* <a href="#method-checkoutmanagerv2-createpaymentrequest">CheckoutManagerV2.CreatePaymentRequest</a>
* <a href="#method-checkoutmanagerv2-checkoutcartoption">CheckoutManagerV2.CheckoutCartOption</a>
* <a href="#method-checkoutmanagerv2-checkoutproductoption">CheckoutManagerV2.CheckoutProductOption</a>
* <a href="#method-checkoutmanagerv2-buycart">CheckoutManagerV2.BuyCart</a>
* <a href="#method-checkoutmanagerv2-buyproduct">CheckoutManagerV2.BuyProduct</a>


CheckoutManagerV2 is an aggregate of Session.

CheckoutManagerV2 is a complete refactor of CheckoutManager, designed to simplify usage and reduce the number of methods require to accomplish checkout. It also lays the groundwork for Alternate Checkout, the ability to use non-credit-card checkout methods. The checkout module handles the shopping cart, and creates, modifies, and completes orders with payment.

This section also covers the use of CheckoutBundleV2 class, to create the CheckoutBundles used by CheckoutManagerV2. There are separate methods for single product "instant buy" purchase, and cart purchase.



### Method: CheckoutBundleV2.CreateCartCheckoutBundleV2

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager


        // create a cart checkout bundle
        CheckoutBundleV2 cartCheckoutBundle = CheckoutBundleV2.createCartCheckoutBundleV2 ( merchantId, optionId, cartId, phonebookId, supportedPaymentMethod, deliveryMethod  );
    }
}
```

CreateCartCheckoutBundleV2 creates a cart checkout bundle.

Method signature: `session.CheckoutBundleV2.createCartCheckoutBundleV2 ( merchantId,  optionId, cartId, phonebookId, addressId, supportedPaymentMethod, deliveryMethod )`

You must pass in the `merchantId`, `optionId`, `cartId`, and `phonebookId` all as strings, a SupportedPaymentMethod object, and a DeliveryMethod object.

Get SupportedPaymentMethod and DeliveryMethod using PaymentOptionsManager.

The method returns a `CartCheckoutBundle` object.






### Method: CheckoutBundleV2.CreateProductCheckoutBundleV2

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager

        // create a product checkout bundle
        CheckoutBundleV2 productCheckoutBundle = CheckoutBundleV2.createProductCheckoutBundleV2(merchantId, optionId, checkoutProduct, phonebookId, supportedPaymentMethod, deliveryMethod);
    }
}
```

CreateCartCheckoutBundleV2 creates a product checkout bundle.

Method signature: `session.CheckoutBundleV2.createProductCheckoutBundleV2 ( merchantId,  optionId, checkoutProduct, phonebookId, addressId, supportedPaymentMethod, deliveryMethod )`

You must pass in the `merchantId`, `optionId`, and `phonebookId` all as strings, a SupportedPaymentMethod object, and a DeliveryMethod object.

Get SupportedPaymentMethod and DeliveryMethod using PaymentOptionsManager.

The method returns a `ProductCheckoutBundle` object.





### Method: CheckoutManagerV2.CreatePaymentRequest

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        PaymentCard paymentCard = new PaymentCard();  // get this from WalletManager
        String cvv = "123";

        // create a payment request
        PaymentRequest paymentRequest = checkoutManagerV2.createPaymentRequest( paymentCard, cvv);
    }
}
```

CreatePaymentRequest creates a payment request, tying together a payment card and CVV that will be used for current transaction.

Method signature: `session.CheckoutManagerV2.createPaymentRequest ( paymentCard, cvv )`

You must pass in a payment card object, and the `cvv` as a string.

The method returns a `PaymentRequest` object.




### Method: CheckoutManagerV2.CheckoutCartOption

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager


        // create a cart checkout bundle
        CheckoutBundleV2 cartCheckoutBundle = CheckoutBundleV2.createCartCheckoutBundleV2 ( merchantId, optionId, cartId, phonebookId, supportedPaymentMethod, deliveryMethod  );

        // checkout Cart
        checkoutManagerV2.checkoutCartOption(cartCheckoutBundle,this);

        // checkout Product
        checkoutManagerV2.checkoutProductOption(productCheckoutBundle,this);

        // buy the contents of a cart
        checkoutManagerV2.buyCart(paymentRequest, cartCheckoutBundle, orderId, this );

        // buy a single product
        checkoutManagerV2.buyProduct(paymentRequest, productCheckoutBundle, orderId, this);
    }

    @Override
    public void onCartOptionCheckoutSuccess(Order order) {
        String orderId = order.getOrderId();
        float finalPrice = order.getFinalPrice();
        List<PriceBreakdown> breakdowns = order.getBreakdowns();

        for (PriceBreakdown priceBreakdown:breakdowns){
            String type = priceBreakdown.getType();
            float amount = priceBreakdown.getAmount();
        }
    }
}
```

CheckoutCartOption queries the server to obtain an order id, total, and price breakdown for a particular Cart.

Method signature: `session.CheckoutManagerV2.checkoutCartOption ( cartCheckoutBundle, [callback or interface] )`

You must pass in a CartCheckoutBundle, and a callback or interface.

The method returns an `Order` object.




### Method: CheckoutManagerV2.CheckoutProductOption

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()
        PaymentCard paymentCard = new PaymentCard();  // get this from WalletManager
        String cvv = "123";

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager

        // create a product checkout bundle
        CheckoutBundleV2 productCheckoutBundle = CheckoutBundleV2.createProductCheckoutBundleV2(merchantId, optionId, checkoutProduct, phonebookId, supportedPaymentMethod, deliveryMethod);

        // checkout Product
        checkoutManagerV2.checkoutProductOption(productCheckoutBundle,this);
    }


    @Override
    public void onProductOptionCheckoutSuccess(Order order) {
        String orderId = order.getOrderId();
        float finalPrice = order.getFinalPrice();
        List<PriceBreakdown> breakdowns = order.getBreakdowns();

        for (PriceBreakdown priceBreakdown:breakdowns){
            String type = priceBreakdown.getType();
            float amount = priceBreakdown.getAmount();
        }
    }
}
```

CheckoutProductOption queries the server to obtain an order id, total, and price breakdown for a particular Product.

Method signature: `session.CheckoutManagerV2.checkoutProductOption ( productCheckoutBundle, [callback or interface] )`

You must pass in a ProductCheckoutBundle, and a callback or interface.

The method returns an `Order` object.



### Method: CheckoutManagerV2.BuyCart

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()
        PaymentCard paymentCard = new PaymentCard();  // get this from WalletManager
        String cvv = "123";

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager


        // create a cart checkout bundle
        CheckoutBundleV2 cartCheckoutBundle = CheckoutBundleV2.createCartCheckoutBundleV2 ( merchantId, optionId, cartId, phonebookId, supportedPaymentMethod, deliveryMethod  );

        // create a payment request
        PaymentRequest paymentRequest = checkoutManagerV2.createPaymentRequest( paymentCard, cvv);

        // buy the contents of a cart
        checkoutManagerV2.buyCart(paymentRequest, cartCheckoutBundle, orderId, this );
    }

    @Override
    public void onCartOptionBuySuccess(OrderSummary orderSummary) {
        // skip this call, ContactInformation entity is not public
        // Merchant.ContactInformation contactInformation = orderSummary.getContactInformation();
        JSONObject orderData = orderSummary.getData();
        String orderId = orderSummary.getOrderId();
        String partnerId = orderSummary.getPartnerId();
        String partnerName = orderSummary.getPartnerName();
    }
}
```

BuyCart takes the prepared CheckoutBundle, Order Id, and Payment information, and submits it to make a purchase. 

Method signature: `session.CheckoutManagerV2.buyCart ( paymentRequest, cartCheckoutBundle, orderId, [callback or interface] )`

You must pass in a PaymentRequest object, a CartCheckoutBundle object, an order id, and a callback or interface.

The method returns an `OrderSummary` object.





### Method: CheckoutManagerV2.BuyProduct

```swift

```
```java
public class CheckoutMgrV2 extends AppCompatActivity implements CheckoutV2Interface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        CheckoutManagerV2 checkoutManagerV2 = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getCheckoutManagerV2();

        String cartId = "123";
        String merchantId = "123";
        String phonebookId = "123";
        String optionId = "123";
        String orderId="123"; // get this from  order.getOrderId()
        PaymentCard paymentCard = new PaymentCard();  // get this from WalletManager
        String cvv = "123";

        CheckoutProduct checkoutProduct = new CheckoutProduct();
        SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); // get this from PaymentOptionsManager
        DeliveryMethod deliveryMethod = new DeliveryMethod();  // get this from PaymentOptionsManager

        // create a product checkout bundle
        CheckoutBundleV2 productCheckoutBundle = CheckoutBundleV2.createProductCheckoutBundleV2(merchantId, optionId, checkoutProduct, phonebookId, supportedPaymentMethod, deliveryMethod);

        // create a payment request
        PaymentRequest paymentRequest = checkoutManagerV2.createPaymentRequest( paymentCard, cvv);

        // buy a single product
        checkoutManagerV2.buyProduct(paymentRequest, productCheckoutBundle, orderId, this);
    }

    @Override
    public void onProductOptionBuySuccess(OrderSummary orderSummary) {
        // skip this call, ContactInformation entity is not public
        // Merchant.ContactInformation contactInformation = orderSummary.getContactInformation();
        JSONObject orderData = orderSummary.getData();
        String orderId = orderSummary.getOrderId();
        String partnerId = orderSummary.getPartnerId();
        String partnerName = orderSummary.getPartnerName();
    }
}
```

BuyCart takes the prepared CheckoutBundle, Order Id, and Payment information, and submits it to make a purchase. 

Method signature: `session.CheckoutManagerV2.buyProduct ( paymentRequest, productCheckoutBundle, orderId, [callback or interface] )`

You must pass in a PaymentRequest object, a ProductCheckoutBundle object, an order id, and a callback or interface.

The method returns an `OrderSummary` object.