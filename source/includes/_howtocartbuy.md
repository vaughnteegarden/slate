

## Product Scan, Cart flow


<img src="images/wsd-jwt-cart-buy-flow.png" style="margin:6px 0;"><br/>[ <a href="images/wsd-jwt-cart-buy-flow.png" target="_blank">View full size</a> ]


The premise of Shoppable Ads is to capture an image scan (usually of an advertisement) using the Scan Manager, resolve it into a product URL, fetch the product info, and enable purchase via saved account information.

In the Cart Buy flow, one or more products are added to a cart. Each merchant will have a separate cart, and multiple carts can contain products at a time. For this example we are assuming only one cart is active, but you can check for multiple carts using `CheckoutManagerV2.getCarts`.



#### 1. Capture image and get product

```swift
// Initialize `ScanManager` based on your RezolveSDK Session

guard let scanManager = rezolveSession?.getScanManager() else {
  	return
}

scanManager.productResultDelegate = self
try? scanManager.startVideoScan(scanCameraView: self.view as! ScanCameraView, rectOfInterest: .frame)

extension ViewController: ProductDelegate {
  	
    func onStartRecognizeImage() {
      	// Suggestion: Show an interstitial loader
    }
  	
    func onFinishRecognizeImage() {
      	// Suggestion: Hide an interstitial loader
    }
  	
    func onCategoryResult(merchantId: String, category: RezolveSDK.Category) {
				// See "Mall" section "3. If the consumer clicks a subcategory, call `getProductsAndCategories`"
    }
  	
    func onCategoryProductsResult(merchantId: String, category: RezolveSDK.Category, productsPage: PageResult<DisplayProduct>) {
      	// See "Mall" section "3. If the consumer clicks a subcategory, call `getProductsAndCategories`"
    }
  	
  	func onProductResult(product: Product) {
      	// See "Mall" section "4. If the consumer clicks a Product, call `getProduct`"
    }
  	
    func onError(error: String) {
      	// Handle error gracefully
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
        scanManager = RezolveSDK.getInstance().getRezolveSession().getScanManager(this, true);

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


#### 2. Add Product to the Cart

```swift
let sampleCheckoutProduct = createCheckoutProductWithVariant(product: product)
let sampleMerchantID = "12"

rezolveSession?.cartManager.createCartWithProduct(sampleCheckoutProduct, merchantId: sampleMerchantID) { (result: Result<CartDetails, RezolveError>) in
		switch result {
    case .success(let cart):
      	{
          	print(cart.id)
          	print(cart.merchantId)
          	
          	// ...
        }
      	
   	case .failure(let error):
    		// Handle error gracefully
    }
})
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
            }
        });
    }
});
```

Call `CheckoutManagerV2.addProductToCart` to add the product to cart.

#### 3. Get shipping and payment options for the cart

```swift
let sampleMerchantID = "12"
let sampleCartID = "1"

rezolveSession?.paymentOptionManager.getPaymentOptionsForCartWith(merchantId: sampleMerchantID, cartId: sampleCartID) { (result: Result<[PaymentOption], RezolveError>) in
		switch result {
    case .success(let options):
      	{
          	// For this example we assume the user chooses the first option. In reality, we should display all options and provide the ability to choose.
          	let paymentMethod  = options.first?.supportedPaymentMethods.first
          	let shippingMethod = options.first?.supportedDeliveryMethods.first
        }
      	
    case .failure(let error):
      	// Handle error gracefully
    }
})
```

```java
public class PaymentOptionsMgr extends AppCompatActivity implements PaymentOptionInterface {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        PaymentOptionManager pom = RezolveSDK.getInstance().getRezolveSession().getPaymentOptionManager();

        String merchantId = "12345";
        String cartId = "12345";

        // get PaymentOptions for cart
        pom.getCartOptions( merchantId, cartId, this);
    }

    @Override
    public void onCartOptionsSuccess(List<PaymentOption> list) {
        for (PaymentOption paymentOption : list ){
            String type = paymentOption.getType();
            List<SupportedPaymentMethod> supportedPaymentMethods = paymentOption.getSupportedPaymentMethods();
            List<Shipping> supportedDeliveryMethods = paymentOption.getSupportedDeliveryMethods();
            JSONObject options = paymentOption.getOptions();
            String id = paymentOption.getId();
            List<CheckoutProduct> checkoutProducts = paymentOption.getCheckoutProducts();

            for (SupportedPaymentMethod supportedPaymentMethod : supportedPaymentMethods ) {
                PaymentMethodData paymentMethodData = supportedPaymentMethod.getPaymentMethodData();
                String spmType = supportedPaymentMethod.getType();
                JSONObject originalDataJson = supportedPaymentMethod.getOriginalDataJson();

                JSONObject requirements = paymentMethodData.getRequirements();
                List<String> supportedDelivery = paymentMethodData.getSupportedDelivery();
                List<String> supportedNetworks = paymentMethodData.getSupportedNetworks();
                List<String> supportedTypes = paymentMethodData.getSupportedTypes();
            }

            for (Shipping shipping : supportedDeliveryMethods) {
                ShippingDetails shippingDetails = shipping.getShippingDetails();
                ShippingMethod shippingMethod = shipping.getShippingMethod();
            }

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
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}


// Assuming the user picks traditional shipping. 
// Click and Collect options are discussed in a separate tutorial.
// Create DeliveryUnit as follows:  


```

Call `PaymentOptionManager` to get shipping and payment options for the current merchant. This tutorial assumes the consumer chose a form of credit card payment, and chose home delivery. 

Note: You must repeat this call if the user chooses a different product variant (size, color, etc), changes product quantity, changes shipping choice, or changes payment option.

For more information on what is returned by `getCartOptions`, see the <a href="#background-listening">Background Listening</a> tutorial.


#### 4. Show payment card choices

```swift
rezolveSession?.walletManager.getAll { (result: Result<[PaymentCard], RezolveError>) in
    // Handle payment cards
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

#### 5. Create a checkout bundle, checkout the cart to get totals, and create an order

```swift
let sampleCartCheckoutBundle = CheckoutBundle(
  	cartId: cartId,
    shippingMethod: deliveryMethod,
    merchantId: merchantId,
    optionId: optionId,
    paymentMethod: paymentMethod,
    paymentRequest: nil,
    phoneId: phoneId,
    location: userLocation
)

rezolveSession?.checkoutManager.checkout(bundle: sampleCartCheckoutBundle) { (result: Result<Price, Error>) in
		switch result {
    case .success(let order):
      	{
          	print(order.id)
          	print(order.finalPrice)
          	
          	// ...
        }
      	
    case .failure(let error):
      	// Handle error gracefully
    }
})
```
```java
// create a Cart Checkout Bundle
String phonebookId = "123"; // use real consumer phonebook id
String optionId = "123"; // get this from productPaymentOption.getId();
String cartId = "123";
SupportedPaymentMethod supportedPaymentMethod = new SupportedPaymentMethod(); //get this from productPaymentOption.getSupportedPaymentMethods()

// create the delivery unit
DeliveryUnit deliveryUnit = new DeliveryUnit(supportedPaymentMethod, address.getId()); 
CheckoutBundleV2 checkoutBundleV2 = CheckoutBundleV2.createCartCheckoutBundleV2( merchantId, optionId, cartId, phonebookId, supportedPaymentMethod, deliveryUnit);

// call CheckoutCartOption method to get an order object and totals
checkout.checkoutCartOption(cartCheckoutBundleV2, new CheckoutV2Callback() {
    @Override
    public void onCartOptionCheckoutSuccess(Order order) {
        super.onCartOptionCheckoutSuccess(order);
        // get order id
        String orderId = order.getOrderId();
        // get price breakdowns
        List<PriceBreakdown> priceBreakdowns = order.getBreakdowns();
    }
});
```

Create a cart checkout bundle.  Then call the SDK `CheckoutManagerV2.checkoutCart` method (Android) or `CheckoutManagerV2.checkout` (IOS)  method to create an order and get totals.  The response order object includes an order id, order total, and price breakdowns.







#### 6. Submit payment for order

```swift
let paymentCard = // RezolveSDK.PaymentCard
let cardCVV = "000" // Card CVV

let sampleCartCheckoutBundle = CheckoutBundle(
  	cartId: cartId,
    shippingMethod: deliveryMethod,
    merchantId: merchantId,
    optionId: optionId,
    paymentMethod: paymentMethod,
    paymentRequest: PaymentRequest(paymentCard: paymentCard, cvv: cardCVV),
    phoneId: phoneId,
    location: userLocation
)

rezolveSession?.checkoutManager.buy(bundle: sampleCartCheckoutBundle) { (result: Result<CheckoutOrder, RezolveError>) in
		switch result {
    case .success(let order):
      	{
          	print(order.id)
          	print(order.partnerId)
          	print(order.partnerName)
          	
          	// ...
        }
      	
    case .failure(let error):
      	// Handle error gracefully
    }
})
```
```java
    // create a paymentRequest object, and then use this with the checkoutProduct object to purchase the cart.
    PaymentCard paymentCard = new PaymentCard(); // use consumer's chosen payment card
    String cvv = "123"; // use actual cvv
    PaymentRequest paymentRequest = checkout.createPaymentRequest(paymentCard,cvv);

    // buy the cart
    String orderId = "123";  // get from order.getOrderId();
    checkout.buyCart(paymentRequest, cartCheckoutBundleV2, orderId, new CheckoutV2Callback() {
        @Override
        public void onCartOptionBuySuccess(OrderSummary orderSummary) {
            super.onCartOptionBuySuccess(orderSummary);
            //display order summary
            String orderId = orderSummary.getOrderId();
            JSONObject orderData = orderSummary.getData();
        }
    });
```

When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This creates the encrypted `paymentRequest` object needed for checkout.

In this tutorial, we assume the user chose credit card payment. Note that `paymentRequest` is actually optional here, and can be null. To determine if it's needed, please check selected `SupportedPaymentMethod`'s type.

Pass the `paymentRequest` object, the`checkoutBundleV2` object, the `orderId`,  and an interface or callback to the `buyCart` method. The success response will be the `order id` as a string. Note that this does not mean the order was confirmed, only that the request was successfully received.


Note that the call to `signOrderUpdate` shown in the sequence diagram is no longer required.






