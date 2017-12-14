## Module: UserActivityManager

Activity is an aggregate of Session.

The user activity module handles order history and other historical data.

Note that this module contains some functions reserved for future functionality, namely:

* getActsHistory()
* getActById()
* getScansHistory()
* getScanById()

These functions are NOT implemented server-side, and should not be used at this time.

### Method: getOrders

```swift
TODO
```
```java
// get orders using UserActivityManager
public class userActivity extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.SANDBOX).getRezolveSession().getUserActivityManager();

        // these strings represent dates in yyyy-MM-dd format. Both are nullable.
        String from = "2016-09-30";
        String to = "2016-12-30";

        // get transactions from User Activity Manager
        myActivityManager.getOrders( this, from, to);

        // The following are reserved for future functionality:
        // myActivityManager.getActsHistory();
        // myActivityManager.getActById();
        // myActivityManager.getScansHistory();
        // myActivityManager.getScanById();
    }

    @Override
    public void onGetOrdersSuccess(OrderHistoryObject orderHistoryObject) {

        String orderHistoryCount = orderHistoryObject.getCount();
        String orderHistoryFrom = orderHistoryObject.getFrom();
        String orderHistoryTo = orderHistoryObject.getTo();
        List<OrderDetails> orderHistoryOrders = orderHistoryObject.getOrders();

        // get properties of orderDetails array
        for (OrderDetails orderDetails : orderHistoryOrders) {
            String orderMerchantId = orderDetails.getMerchantId();
            String orderId = orderDetails.getOrderId();
            String orderPan4 = orderDetails.getPan4();
            String orderPayWith = orderDetails.getPayWith();
            String orderStatus = orderDetails.getStatus();
            String orderTimestamp = orderDetails.getTimestamp();
            RezolveLocation orderLocation = orderDetails.getGeoloc();
            List<OrderProduct> orderItems = orderDetails.getItems();

            Order price = orderDetails.getPrice();
            String partnerId = orderDetails.getPartnerId();
            Address billingAddress = orderDetails.getBillingAddressDetails();
            Address shippingAddress = orderDetails.getShippingAddressDetails();
            String email = orderDetails.getEmail();
            String firstName = orderDetails.getFirstName();
            String lastName = orderDetails.getLastName();
            Phone phone = orderDetails.getPhone();
            String merchantEmail = orderDetails.getMerchantEmail();
            String merchantName = orderDetails.getMerchantName();
            String merchantPhone = orderDetails.getMerchantPhone();




            // get properties of finalPrice object
            String orderFinalPriceOrderId = price.getOrderId();
            float orderFinalPriceFinalPrice = price.getFinalPrice();
            List<PriceBreakdown> orderFinalPriceBreakdowns = price.getBreakdowns();

            // get properties of PriceBreakdown array
            for (PriceBreakdown priceBreakdown : orderFinalPriceBreakdowns) {
                float breakdownAmount = priceBreakdown.getAmount();
                String breakdownType = priceBreakdown.getType();
            }

            // get properties of orderLocation  object
            double orderLatitude = orderLocation.getLatitude();
            double orderLongitude = orderLocation.getLongitude();

            // get properties of orderItems array
            for (OrderProduct orderProduct : orderItems) {
                List<ConfigurableOptionValue> configurableOptionValues = orderProduct.getConfigurableOptionValues();
                int quantity = orderProduct.getQuantity();

                for (ConfigurableOptionValue configurableOptionValue : configurableOptionValues) {
                    String configurableOptionValueCode = configurableOptionValue.getCode();
                    String configurableOptionValueLabel = configurableOptionValue.getLabel();
                    String configurableOptionValueValue = configurableOptionValue.getValue();
                    int configurableOptionValueValueId = configurableOptionValue.getValueId();
                }
            }
        }
    }

    @Override
    public void onGetScansSuccess(RezolveHistoryObject rezolveHistoryObject) {
        // reserved for future functionality
        // not implemented server-side
    }

    @Override
    public void onGetScanByIdSuccess(RezolveScanObject rezolveScanObject) {
        // reserved for future functionality
        // not implemented server-side
    }

    @Override
    public void onPostHistoryItemSuccess(RezolveScanObject rezolveScanObject) {
        // reserved for future functionality
        // not implemented server-side
    }

    @Override
    public void onGetActsSuccess(RezolveHistoryObject rezolveHistoryObject) {
        // reserved for future functionality
        // not implemented server-side
    }

    @Override
    public void onGetActByIdSuccess(RezolveHistoryObject rezolveHistoryObject) {
        // reserved for future functionality
        // not implemented server-side
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

Method signature: `session.getOrders( [callback or interface] )`

The method returns a `orderHistory object`.

#### OrderHistoryObject Object

|field|format|example|
|---|---|---|
|orderHistoryCount|string|15|
|orderHistoryFrom|date string in format YYYY-MM-DD|2017-12-01|
|orderHistoryTo|date string in format YYYY-MM-DD|2018-01-01|
|orderHistoryOrders|array of OrderDetails objects|&nbsp;|


#### OrderDetails object

|field|format|example|
|---|---|---|
|orderMerchantId|string|123|
|orderId|string|123|
|orderPan4|string|5546|
|orderPayWith|string|VISA|
|orderStatus|string|Complete|
|orderTimestamp|string (unix timestamp)|1493130773|
|orderLocation|RezolveLocation object|&nbsp;|
|partnerId|string|123|
|billingAddress|Address object|&nbsp;|
|shippingAddress|Address object|&nbsp;|
|email|string|user@example.com|
|firstName|string|John|
|lastName|string|Everyman|
|phone|Phone object|&nbsp;|
|merchantEmail|string|merchant@email.com|
|merchantName|string|Adam's Apples|
|merchantPhone|string|555-555-1212|
|price|Order object|&nbsp;|
|orderItems |array of OrderProduct objects|&nbsp;|

#### Phone Object
|field|format|example|
|---|---|---|
|phoneId|string|123abc|
|phoneName|string|Home phone|
|phoneNumber|string|555-555-1212|

#### Address Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123abc|
|short_name|string|My house|
|line1|string|Theodore Lowe|
|line2|string|Ap #867-859 Sit Rd.|
|city|string|New York|
|state|string|NY|
|zip|string|39531|
|country|string|USA|

#### Order object

|field|format|example|
|---|---|---|
|orderFinalPriceOrderId|string|123|
|orderFinalPriceFinalPrice|float|123.45|
|orderFinalPriceBreakdowns|array of PriceBreakdown objects|&nbsp;|


#### PriceBreakdown object

|field|format|example|
|---|---|---|
|breakdownAmount|float|123.45|
|breakdownType|string|Subtotal|


#### RezolveLocation object

|field|format|example|
|---|---|---|
|orderLatitude|double|66.123|
|orderLongitude|double|-140.889|


#### OrderProduct object

|field|format|example|
|---|---|---|
|configurableOptionValues|array of ConfigurableOptionValue objects|&nbsp;|
|quantity|integer|1|


#### ConfigurableOptionValue object

|field|format|example|
|---|---|---|
|configurableOptionValueCode|string|pl123|
|configurableOptionValueLabel|string|Pattern|
|configurableOptionValueValue|string|plaid|
|configurableOptionValueValueId|integer|123|

