## Product Scan, Instant Buy flow


<img src="images/wsd-jwt-product-buy-flow.png" style="margin:6px 0;"><br/>[ <a href="images/JWT-shoppable-ad-flow-single-buy.png" target="_blank">View full size</a> ]


The premise of Shoppable Ads is to capture an image scan (usually of an advertisement) using the Scan Manager, resolve it into a product URL, fetch the product info, and enable purchase via saved account information.

In the Instant Buy flow, we purchase the product immediately, without first adding it to the cart.


#### 1. Capture image and get product



```swift
class ViewController: UIViewController, ProductDelegate {

    var session: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize RezolveSDK
            let rezolveSdk = RezolveSDK(
                apiKey: REZOLVE_API_KEY, 
                env: REZOLVE_SDK_ENV, 
                config: config, 
                dataClient: dataClient
            )

            // Creates Session
            rezolveSdk.createSession(
                accessToken: token, 
                entityId: entityId, 
                partnerId: partnerId, 
                callback: { session in

                    self.session = session
                    self.session?.getScanManager().productResultDelegate = self
                    self.session?.getScanManager().startVideoScan(scanCameraView: self.view as! ScanCameraView)

                }, errorCallback: { response in
                if response.statusCode == 401 { // Invalid token return
                    // Developer shoud put token refreshing logic
                    // using `credentials/ping` endpoint
                }

                   // Handle other errors
	       })
        }
    }


    func onError(error: String) -> Void {

      // handle error
    }

    func onStartRecognizeImage() -> Void {

      // suggestion: show a loading indicator
    }

    func onFinishRecognizeImage() -> Void {

      // suggestion: alert user with some sound
    }

    func onProductResult(product: Product) -> Void {

    }

    func onCategoryResult(category: RezolveCategory) -> Void {

    }

    func onCategoryProductsResult(category: RezolveCategory, productsPage: PageResult<DisplayProduct>) -> Void {

    }
}

```

```java

public class ScanActivity extends AppCompatActivity implements ScanManagerInterface, View.OnClickListener {


    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // set scan view
        setContentView(R.layout.scan_activity);
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);

        //get scan manager
        boolean barcodeenabled = true;
        boolean videoenabled = true;
        scanManager = RezolveSDK.getInstance().getRezolveSession().getScanManager(this, barcodeenabled, videoenabled );                                     

        //start video scan to acquire image
        scanManager.startVideoScan(this, rezolveScanView);
    }


	// capture product result
    @Override
    public void onProductResult(Product product) {

        // get product info
        String productId = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();

        // ...etc
    }
}

```

First, initialize `scanManager`, and enable the scan screen using `session.startVideoScan()`, and capture a watermarked image. The scanManager recognizes the encoded product data, and extracts `merchantId`, `catalogId`, and `productId` from the image, automatically calling `getProduct`. The scanManager returns a `product` object.


#### 2. Get shipping and payment options for the product

```swift
    // Fetches PaymentOption for Product
    rezolveSession.getProductOptions(
        checkoutProduct: checkoutProduct,
        merchantId: merchantId,
        callback: { (paymentOption: PaymentOption) in

        let paymentMethods = paymentOption.supportedPaymentMethods
        let shippings = paymentOption.supportedDeliveryMethods

    }, errorCallback: { httpResponse in
        // Error handling
    })

    /// for this example we assume the user chooses the first option. In reality, display all options to the user, and let them choose.
    let supportedPaymentMethod = paymentOption.supportedPaymentMethods.first!
    let shippingMethod = paymentOption.supportedDeliveryMethods.first!
```

```java
public class PaymentOptionsMgr extends AppCompatActivity implements PaymentOptionInterface {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        PaymentOptionManager pom = RezolveSDK.getInstance().getRezolveSession().getPaymentOptionManager();

        String merchantId = "12345";
        CheckoutProduct checkoutProduct = new CheckoutProduct();

        // get PaymentOptions for a product
        pom.getProductOptions(checkoutProduct, merchantId, this);

    }


    @Override
    public void onProductOptionsSuccess(PaymentOption paymentOption) {
        String id = paymentOption.getId();
        List<CheckoutProduct> checkoutProducts = paymentOption.getCheckoutProducts();
        JSONObject paymentOptionOptions = paymentOption.getOptions();
        List<Shipping> supportedDeliveryMethods =  paymentOption.getSupportedDeliveryMethods();
        List<SupportedPaymentMethod> supportedPaymentMethods = paymentOption.getSupportedPaymentMethods();
        String type = paymentOption.getType();

        for (CheckoutProduct checkoutProduct : checkoutProducts) {
            // breakdown checkoutProduct object
            float qty = checkoutProduct.getQty();
            Placement productPlacement = checkoutProduct.getProductPlacement();
            int cpId  = checkoutProduct.getId();
            List<ConfigurableOption> configurableOptions = checkoutProduct.getConfigurableOptions();

            String placementId = productPlacement.getPlacementId();
            String adId = productPlacement.getAdId();

            for (ConfigurableOption configurableOption : configurableOptions) {
                int value = configurableOption.getValue();
                String code = configurableOption.getCode();
            }
        }

        for (Shipping shipping : supportedDeliveryMethods) {
            ShippingDetails shippingDetails = shipping.getShippingDetails();
            ShippingMethod shippingMethod = shipping.getShippingMethod();
        }

        for (SupportedPaymentMethod supportedPaymentMethod : supportedPaymentMethods ) {
            PaymentMethodData paymentMethodData = supportedPaymentMethod.getPaymentMethodData();
            String spmType = supportedPaymentMethod.getType();
            JSONObject originalDataJson = supportedPaymentMethod.getOriginalDataJson();

            JSONObject requirements = paymentMethodData.getRequirements();
            List<String> supportedDelivery = paymentMethodData.getSupportedDelivery();
            List<String> supportedNetworks = paymentMethodData.getSupportedNetworks();
            List<String> supportedTypes = paymentMethodData.getSupportedTypes();
        }
    }
}
```

Call `PaymentOptionManager` to get shipping and payment options for the current merchant. This tutorial assumes the consumer chose a form of credit card payment, and chose home delivery. 

Note: You must repeat this call if the user chooses a different product variant (size, color, etc), changes product quantity, changes shipping choice, or changes payment option.


#### 3. Show payment card choices



```swift

  mySession?.walletManager.getAll() { (listOfCards: Array<PaymentCard>) in
      // handle list of cards here
  }

```

```java

rezolveSession.getWalletManager().getAll(new WalletCallback() {

    @Override
    public void onWalletGetAllSuccess(List<PaymentCard> list) {
        // handle getAll response here
    }

});

```

Use `walletManager.getAll` to list the available card choices. If the consumer wishes to buy, they will select a payment card to use, and provide confirmation of ordering intent.

We recommend using a "slide to buy" button to confirm purchase intent, while preserving the maximum ease of use.


#### 4. Create a checkout bundle, checkout the produc to get totals, and create an order

```swift
let productBundleV2 = createProductCheckoutBundleV2(
    checkoutProduct: checkoutProduct,
    let delivery = DeliveryMethod(addressId: addressObject.id), // address id is blank because this is only needed for Click and Collect
    merchantId: merchantId,
    optionId: optionId,
    paymentMethod: paymentMethod,
    phoneId: phoneId
)

rezolveSession.checkoutManagerV2.checkout(
    bundle: productBundleV2, 
    callback: { order in 
        // Order handling
    },
    errorCallback: { _ in 
        // Error handling
})
```
```java
@Override
public void onProductResult(Product product) {
	// get product info
	String product_id = product.getId();
	String title = product.getTitle();
	String subtitle = product.getSubtitle();
	// ... etc


	// create a CheckoutProduct object with the product id , and set the quantity
	CheckoutProduct checkoutProduct = new CheckoutProduct();
	checkoutProduct.setId(Integer.parseInt(product_id));
	checkoutProduct.setQty(1);

   // use info from productPaymentOption to populate Shipping and PaymentMethod choices...
    String productPaymentOptionId = productPaymentOption.getId();
    List<SupportedPaymentMethod> supportedPaymentMethods = productPaymentOption.getSupportedPaymentMethods();
    List<Shipping> deliveryMethods = productPaymentOption.getSupportedDeliveryMethods();
    SupportedPaymentMethod supportedPaymentMethod = supportedPaymentMethods.get(0);  // in reality customer chooses an option here
    Shipping deliveryMethod = deliveryMethods.get(0); // in reality customer chooses an option here
    String phonebookId = "123"; // use real consumer phonebook id
    
    // create the delivery unit
    DeliveryUnit deliveryUnit = new DeliveryUnit(supportedPaymentMethod, address.getId()); 

    // create a product checkout bundle           
	CheckoutBundleV2.createProductCheckoutBundleV2(merchantId, paymentOption.getId(), checkoutProduct, phoneId, supportedPaymentMethod, deliveryUnit);

    // call the CheckoutProductOption method to get an order object and totals
    checkout.checkoutProductOption(productCheckoutBundleV2, new CheckoutV2Callback() {
        public void onCheckoutProductSuccess(Order order) {
            // get pricing breakdown and final price
            List<PriceBreakdown> pricingBreakdown = order.getBreakdowns();
            float finalPrice = order.getFinalPrice();
            String orderId = order.getOrderId();

            // create a paymentRequest object, and then use this with the checkoutProduct object to purchase the item.
            PaymentCard paymentCard = new PaymentCard(); // use consumer's chosen payment card
            String cvv = "123"; // use actual cvv
            PaymentRequest paymentRequest = checkout.createPaymentRequest(paymentCard,cvv);

            // buy a single product
            checkout.buyProduct(paymentRequest, productCheckoutBundleV2, orderId, new CheckoutV2Callback() {
                @Override
                public void onProductOptionBuySuccess(OrderSummary orderSummary) {
                    super.onProductOptionBuySuccess(orderSummary);
                    //display order summary
                    String orderId = orderSummary.getOrderId();
                    JSONObject orderData = orderSummary.getData();
                }
            });

        }
    });
}
```

Once you have product information, create a CheckoutProduct object. Then call the SDK `CheckoutManager.checkoutProduct` method to create an order and get totals.  The response order object includes an order id, order total, and price breakdowns.



#### 5. Submit payment for order

```swift
    let paymentCard = // RezolveSDK.PaymentCard

    let cardCVV = "000" // Card CVV

    let paymentRequest = PaymentRequest(
        paymentCard: paymentCard, 
        cvv: cardCVV
    )

    rezolveSession.checkoutManagerV2.buy(
        bundle: productBundleV2,
        paymentRequest: paymentRequest,
        checkoutId: checkoutId,
        callback: { checkoutOrder in
            // CheckoutOrder handling
        },
        errorCallback: { _ in 
            // Error handling
    })
```
```java
// create a paymentRequest object, and then use this with the checkoutBundle object 

// create paymentRequest object
PaymentCard paymentCard = new PaymentCard(); // use consumer's chosen payment card from step 3
String cvv = "123"; // use actual cvv
PaymentRequest paymentRequest = checkout.createPaymentRequest(paymentCard,cvv);


// buy a single product
checkoutManagerV2.buyProduct(paymentRequest, productCheckoutBundleV2, orderId, this);

@Override
public void onProductOptionBuySuccess(OrderSummary orderSummary) {
    // skip this call, ContactInformation entity is not public
    // Merchant.ContactInformation contactInformation = orderSummary.getContactInformation();
    JSONObject orderData = orderSummary.getData();
    String orderId = orderSummary.getOrderId();
    String partnerId = orderSummary.getPartnerId();
    String partnerName = orderSummary.getPartnerName();
}

```



When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This creates the encrypted `paymentRequest` object needed for checkout.

In this tutorial, we assume the user chose credit card payment. Note that `paymentRequest` is actually optional here, and can be null. To determine if it's needed, please check selected `SupportedPaymentMethod`'s type.

Pass a `paymentRequest` object, `checkoutBundleV2` object, the `orderId`, and an interface or callback to the `buyProduct` method. The success response will an `OrderSummary` object. Note that this does not mean the order was confirmed, only that the request was successfully received.

When the method returns successfully, it will automatically initiate the signOrderUpdate method.



#### 6. Wait for signOrderUpdate to return a final order status.

```swift
session.checkoutManagerV2.buyCart(merchantId: MERCHANT_ID, cart: cartDetails, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, phone: phone, callback: { (order: CheckoutOrder) in

session.checkoutManagerV2.signOrderUpdate(merchantId: MERCHANT_ID, order: order, callback: { status, transaction in

    print(status)

    if status == .completed {


    }
})

}, errorCallback: { print($0) })
```
```java
@Override
public void onOrderUpdateReceived(Transaction.Status status, Transaction transaction) {
    // get properties of status object
    String statusLabel = status.getLabel();   // will return one of: completed, canceled, or processing

    // get properties of transaction object
    String transStatus = transaction.getStatus();
    String transOrderId = transaction.getOrderId();
    String transLastUpdated = transaction.getLastUpdated();
}
```

SignOrderUpdate starts a socket listener with the order id, and waits for the server to return an order status. The server will return one of three status: 
- completed
- canceled
- processing

If either `completed` or `canceled` is returned, the listener stops and the socket closes. If `processing` is returned, the listener will remain active until the order status is updated to either `completed` or `canceled`.











