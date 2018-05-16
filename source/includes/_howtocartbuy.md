

## Product Scan, Cart flow


<img src="images/JWT-shoppable-ad-flow-cart-buy.png" style="margin:6px 0;"><br/>[ <a href="images/JWT-shoppable-ad-flow-cart-buy.png" target="_blank">View full size</a> ]


The premise of Shoppable Ads is to capture an image scan (usually of an advertisement) using the Scan Manager, resolve it into a product URL, fetch the product info, and enable purchase via saved account information.

In the Cart Buy flow, one or more products are added to a cart. Each merchant will have a separate cart, and multiple carts can contain products at a time. For this example we are assuming only one cart is active, but you can check for multiple carts using `CheckoutManager.getCarts`.



#### 1. Capture image and get product

```swift
class ViewController: UIViewController, ProductDelegate {

    var session: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

                self.session = session
                self.session?.getScanManager().productResultDelegate = self
                self.session?.getScanManager().startVideoScan(scanCameraView: self.view as! ScanCameraView)

            }, errorCallback: { print($0) })
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
        scanManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT)
          .getRezolveSession().getScanManager(this, true);

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

First, initialize `scanManager`, and enable the scan screen using `session.startVideo()`, and capture a watermarked image. The scanManager recognizes the encoded product data, and extracts `merchantId`, `catalogId`, and `productId` from the image, automatically calling `getProduct`. The scanManager returns a `product` object.





#### 2. Add the product to the cart, and create an order with an order total

```swift
let checkoutProduct = createCheckoutProductWithVariant(product: product)

session.cartManager.createCartWithProduct(merchantId: MERCHANT_ID, product: checkoutProduct, callback: { cartDetails in


}, errorCallback: { print($0) })
```
```java
// add product to cart
checkout.addProductToCart(this, checkoutProduct, merchantId, new CheckoutCallback() {
    @Override
    public void onAddProductsToCartSuccess(CartDetails cartDetails) {
        super.onAddProductsToCartSuccess(cartDetails);

        String cartId = cartDetails.getId();
        String merchantId = "123"; //use actual merchant id
        String addressId = "123";  //use actual address id

        // immediately call CheckoutCart to get an Order with totals
        checkout.addProductToCart( checkoutProduct, merchantId, new CheckoutCallback() {
            @Override
            public void onAddProductsToCartSuccess(CartDetails cartDetails) {
                super.onAddProductsToCartSuccess(cartDetails);

                String cartId = cartDetails.getId();
                String merchantId = "123"; //use actual merchant id
                String addressId = "123";  //use actual address id

                // create checkoutBundle
                CheckoutBundle checkoutBundle = CheckoutBundle.createCartCheckoutBundle ( cartId,  merchantId, addressId );

                //  get order id and totals for a cart
                checkout.checkoutCart(checkoutBundle,this);
            }
        });
    }
});
```


Once you have product information, create a CheckoutProduct object.  Then call the SDK `CheckoutManager.checkoutCart` method to create an order and get totals.  The response order object includes an order id, order total, and price breakdowns.



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



#### 4. Submit payment for order

```swift
let PARIS_LOCATION = RezolveLocation(type: .point, coordinates: (latitude: 48.8736645, longitude: 2.2910793))

let paymentRequest = session.checkoutManager.createPaymentRequest(paymentCard: remoteCard, cvv: CVV)

session.checkoutManager.buyProduct(merchantId: MERCHANT_ID, checkoutProduct: checkoutProduct, address: remoteAddress, paymentRequest: paymentRequest, location: PARIS_LOCATION, callback: { (order: CheckoutOrder) in


}, errorCallback: { print($0) })
```
```java
// create a paymentRequest object, and then use this with the checkoutProduct object and rezolveLocation object to purchase the item.
// create paymentRequest object
PaymentCard paymentCard2 = new PaymentCard(); // use consumer's chosen payment card from step 3
String cvv2 = "123"; // use actual cvv
PaymentRequest paymentRequest2 = checkout.createPaymentRequest(paymentCard,cvv);

// add consumer's location
RezolveLocation rezolveLocation2 = new RezolveLocation();
rezolveLocation.setLatitude(48.8736645);
rezolveLocation.setLongitude(2.2910793);

// buy the cart
String cartId = "123"; // either buy current cart, or use getCarts to choose a cart

checkout.buyCart( checkoutBundle, phonebookId, rezolveLocation, paymentRequest,  new CheckoutCallback() {
	public void onCartOrderPlaced(String orderId) {
		// display order confirmation
	}
});
```



When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This creates the encrypted `paymentRequest` object needed for checkout.

Pass the `checkoutBundle` object, `phonebookId` string, a `rezolveLocation` object, the `paymentRequest` object, and an interface or callback to the `buyCart` method. The success response will be the `order id` as a string. Note that this does not mean the order was confirmed, only that the request was successfully received.

When the method returns successfully, in Android it will automatically initiate the signOrderUpdate method. In IOS, call signOrderUpdate in the callback.



#### 5. Wait for signOrderUpdate to return a final order status.

```swift
session.checkoutManager.buyCart(merchantId: MERCHANT_ID, cart: cartDetails, address: remoteAddress, paymentRequest: paymentRequest, location: DEFAULT_LOCATIONS, phone: phone, callback: { (order: CheckoutOrder) in

session.checkoutManager.signOrderUpdate(merchantId: MERCHANT_ID, order: order, callback: { status, transaction in

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










