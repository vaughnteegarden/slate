

## Shoppable Ads, Instant Buy flow



<img src="images/ShoppableAdFlowInstantBuy.png" style="margin:6px 0;"><br/>[ <a href="images/ShoppableAdFlowInstantBuy.png">View full size</a> ]



The premise of Shoppable Ads is to capture an image scan (usually of an advertisement) using the Scan Manager, resolve it into a product URL, fetch the product info, and enable purchase via saved account information.

In the Instant Buy flow, we purchase the product immediately, without first adding it to the cart.



#### 1. Capture image and get product



```swift

import UIKit

import RezolveSDK



class ScanManagerViewController: UIViewController {



    @IBOutlet var scanCameraView: ScanCameraView?

    var mySession: RezolveSession?



    override func viewDidLoad() {

        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let scanManager = self.mySession?.getScanManager()

        scanManager?.productResultDelegate = self

        scanManager?.rezolveScanResultDelegate = self

        scanManager?.startVideoScan(scanCameraView: self.scanCameraView!)

    }



    override func didReceiveMemoryWarning() {

        super.didReceiveMemoryWarning()

        scanManager?.stop()

    }

}



extension ScanManagerViewController : ProductDelegate, RezolveScanResultDelegate {

    public func productDidFetched(product: Product) {

        // handle product

        let productId: String  = product.id

        let title: String = product.title

        let subtitle: String = product.subtitle

        // ...etc

    }

}

```

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

        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)

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



Once you have product information, call the SDK `checkoutOrder` method. Pass in the merchant, type, delivery address id, loyalty info (if any), geolocation if available, and finally the information on the desired product, including variant choices.  The response includes an order id, final total price, and a price breakdown (composed of base price, variant price premium if any, shipping, and tax).



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



If the consumer agrees with the price and wishes to complete the order, use `walletManager.getAll` to list the available card choices. The consumer then chooses a payment card.



At this point, we recommend using a "slide to buy" button to confirm purchase intent, while preserving the maximum ease of use.



#### 4. Submit payment for order



```swift

  let paymentRequest: PaymentRequest = PaymentRequest(paymentCard: paymentCard, cvv: cvv)



  self.mySession?.checkoutManager.buyOrder(paymentRequest: paymentRequest, 

  order: order) { (transaction: Transaction) in

      //handle result

  }

```

```java

// Create an encrypted payment request using the CheckoutManager.createPaymentRequest



let paymentRequest: PaymentRequest = self.mySession?.checkoutManager.createPaymentRequest(

paymentCard: paymentCard, cvv: cvv) 



// pass the paymentRequest object, order object, and callback to the buyOrder method

rezolveSession.getCheckoutManager().buyOrder(paymentRequest, order, new CheckoutCallback() {

    @Override

    public void onBuyOrderSuccess(Transaction transaction) {

        //handle result

    }

});

```



When the user confirms intent, pass the card choice and the entered CVV value to the `createPaymentRequest` method. This creates the encrypted `paymentRequest` object needed for checkout.



Pass the `paymentRequst` object and the `order` object to the `buyOrder` method. The response contains either a `transaction` object with order confirmation with receipt info, or if rejected, an error with the reason for the order rejection.












