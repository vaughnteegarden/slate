## Module: PaymentOptionsManager

**Topics**

* <a href="#method-checkoutbundle-createcartcheckoutbundle">CheckoutBundle.CreateCartCheckoutBundle</a>


PaymentOptionsManager is an aggregate of Session.

PaymentOptionsManager retrieves payment options and shipping options, for either a single product or a cart . 



### Method: PaymentOptionsManager.GetCartOptions

```swift
// Fetch PaymentOption for cart
rezolveSession.paymentOptionManager.getCartOptions(
    merchantId: merchantId,
    cartId: cartId,
    callback: { (paymentOptionList: [PaymentOption]) in

        // Handle [PaymentOption]

}, errorCallback: { httpResponse in
    // Error handling code
})
```
```java
public class PaymentOptionsMgr extends AppCompatActivity implements PaymentOptionInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        PaymentOptionManager pom = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getPaymentOptionManager();

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
}
```

GetCartOptions gets payment options and shipping options for a cart. 

Method signature: `session.PaymentOptionsManager.getCartOptions( merchantId, cartId, [interface or callback])`

You must pass in the `merchantId` and `cartId` as strings, and supply an interface or callback.

The method returns an array of `PaymentOption` objects.


### Method: PaymentOptionsManager.GetProductOptions

```swift
// Fetches PaymentOption for Product
rezolveSession.paymentOptionManager.getProductOptions(
    checkoutProduct: checkoutProduct,
    merchantId: merchantId,
    callback: { (paymentOption: PaymentOption) ->

    let paymentMethods = paymentOption.supportedPaymentMethods
    let shippings = paymentOption.supportedDeliveryMethods

}, errorCallback: { httpResponse in
    // Error handling
})
```
```java
public class PaymentOptionsMgr extends AppCompatActivity implements PaymentOptionInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        PaymentOptionManager pom = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getPaymentOptionManager();

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

GetProductOptions gets payment options and shipping options for a single product. 

Method signature: `session.PaymentOptionsManager.getProductOptions( checkoutProduct, merchantId, [interface or callback])`

You must pass in a `checkoutProduct` object, the `merchantId` as a string, and supply an interface or callback.

The method returns a `PaymentOption` object.