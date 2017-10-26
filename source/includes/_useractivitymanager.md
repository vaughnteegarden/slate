## Module: UserActivityManager

Activity is an aggregate of Session.

The user activity module handles order history and other historical data.

Note that this module contains some functions reserved for future functionality, namely:
* getActsHistory()
* getActById()
* getScansHistory()
* getScanById()

Thse functions are NOT implemented server-side, and should not be used at this time.

### Method: getOrders

```swift
TODO

import UIKit
import RezolveSDK

class UserActivityViewController: UIViewController {

    let API_KEY: String = "your_api_key"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.userActivityManager.getOrders() { (transactions: Array<Transaction>) in

            //handle result
        }
    }
}
```
```java
// get orders using UserActivityManager
public class userActivity extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX).getRezolveSession().getUserActivityManager();

        // get transactions from User Activity Manager
        myActivityManager.getOrders(this);

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
            String orderType = orderDetails.getType();
            Order orderFinalPrice = orderDetails.getFinalPrice();
            RezolveLocation orderLocation = orderDetails.getGeoloc();
            List<HistoryItem> orderItems = orderDetails.getItems();

            // get properties of finalPrice object
            String orderFinalPriceOrderId = orderFinalPrice.getOrderId();
            float orderFinalPriceFinalPrice = orderFinalPrice.getFinalPrice();
            List<PriceBreakdown> orderFinalPriceBreakdowns = orderFinalPrice.getBreakdowns();

            // get properties of PriceBreakdown array
            for (PriceBreakdown priceBreakdown : orderFinalPriceBreakdowns) {
                float breakdownAmount = priceBreakdown.getAmount();
                String breakdownType = priceBreakdown.getType();
            }

            // get properties of orderLocation  object
            String orderLocationType = orderLocation.getLocationType();
            double orderLatitude = orderLocation.getLatitude();
            double orderLongitude = orderLocation.getLongitude();

            // get properties of orderItems array
            for (HistoryItem historyItem : orderItems) {
                Address historyItemAddressDetails = historyItem.getAddressDetails();
                CustomerDetails historyItemCustomerDetails = historyItem.getCustomerDetails();
                List<Product> historyItemProducts = historyItem.getProducts();

                // get properties of historyItemAddressDetails object
                String historyAddressId = historyItemAddressDetails.getId();
                String historyAddressShortName = historyItemAddressDetails.getShortName();
                String historyAddressCity = historyItemAddressDetails.getCity();
                String historyAddressCountry = historyItemAddressDetails.getCountry();
                String historyAddressLine1 = historyItemAddressDetails.getLine1();
                String historyAddressLine2 = historyItemAddressDetails.getLine2();
                String historyAddressState = historyItemAddressDetails.getState();
                String historyAddressZip = historyItemAddressDetails.getZip();

                // get properties of historyItemCustomerDetails object
                String historyCustomerId = historyItemCustomerDetails.getCustomerId();
                String historyCustomerEmail = historyItemCustomerDetails.getEmail();
                String historyCustomerFirstName = historyItemCustomerDetails.getFirstName();
                String historyCustomerLastName = historyItemCustomerDetails.getLastName();
                String historyCustomerLoyaltyCardNo = historyItemCustomerDetails.getLoyaltyCardNumber();
                Boolean historyCustomerHasLoyalty = historyItemCustomerDetails.isHasLoyaltyCard();

                // get properties of historyItemProducts array
                for (Product product : historyItemProducts) {
                    List<CustomOption> productCustomOptions = product.getCustomOptions();
                    String productDescription = product.getDescription();
                    String productId = product.getId();
                    List<String> productImages = product.getImages();
                    List<String[]> productThumbs = product.getImageThumbs();
                    String productMerchantId = product.getMerchantId();
                    List<Variant> productOptionsAvailable = product.getOptionsAvailable();
                    List<Option> productOptions = product.getOptions();
                    List<Variant> productOptionAvailable = product.getOptionAvailable();
                    float productPrice = product.getPrice();
                    String productSubtitle = product.getSubtitle();
                    String productTitle = product.getTitle();

                    // get properties of productCustomOptions array
                    for (CustomOption customOption : productCustomOptions) {
                        int customOptionId = customOption.getOptionId();
                        int customOptionsSortOrder = customOption.getSortOrder();
                        String customOptionsTitle = customOption.getTitle();
                        String customOptionsType = customOption.getType();
                        List<CustomOptionValue> customOptionValues = customOption.getValues();

                        // get properties of customOptionValues array
                        for (CustomOptionValue customOptionValue : customOptionValues) {
                            int customOptionValueSortOrder = customOptionValue.getSortOrder();
                            String customOptionValueTitle = customOptionValue.getTitle();
                            String customOptionValueValueId = customOptionValue.getValueId();
                        }
                    }

                    // get properties of productOptionsAvailable array
                    for(Variant variant : productOptionsAvailable){
                        List<Combination> variantCombinations = variant.getCombinations();

                        // get properties of variantCombinations array
                        for(Combination combination : variantCombinations){
                            String combinationCode = combination.getCode();
                            String combinationValue = combination.getValue();
                        }
                    }

                    // get properties of productOptions array
                    for(Option option : productOptions){
                        String optionCode = option.getCode();
                        String optionLabel = option.getLabel();
                        List<OptionValue> optionValues = option.getValues();
                        String optionExtraInfo = option.getExtraInfo();
                        
                        // get properties of optionValues array
                        for(OptionValue optionValue:optionValues) {
                            String optionValueLabel = optionValue.getLabel();
                            String optionValueValue = optionValue.getValue();
                        }
                    }
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

#### orderHistoryObject Object

|field|format|example|
|---|---|---|
|orderHistoryCount|string|15|
|orderHistoryFrom|date string in format YYYYMMDD|20171201|
|orderHistoryTo|date string in format YYYYMMDD|20180101|
|orderHistoryOrders|array of OrderDetails objects|&nbsp;|


#### orderDetails object

|field|format|example|
|---|---|---|
|orderMerchantId|string|123|
|orderId|string|123|
|orderPan4|string|5546|
|orderPayWith|string|VISA|
|orderStatus|string|Complete|
|orderTimestamp|string|(unix timestamp)|1493130773|
|orderType|string|TODO|
|orderFinalPrice|Order object|&nbsp;|
|orderLocation|RezolveLocation object|&nbsp;|
|orderItems |array of HistoryItem objects|&nbsp;|


#### order object

<!-- TODO needs currency with price -->

|field|format|example|
|---|---|---|
|orderFinalPriceOrderId|string|123|
|orderFinalPriceFinalPrice|float|123.45|
|orderFinalPriceBreakdowns|array of PriceBreakdown objects|&nbsp;|


#### priceBreakdown object

<!-- TODO needs currency with price -->

|field|format|example|
|---|---|---|
|breakdownAmount|float|123.45|
|breakdownType|string|Subtotal|


#### rezolveLocation object

|field|format|example|
|---|---|---|
|orderLocationType|string| TODO |
|orderLatitude|double|66.123|
|orderLongitude|double|-140.889|


#### historyItem object

|field|format|example|
|---|---|---|
|historyItemAddressDetails|Address object|&nbsp;|
|historyItemCustomerDetails|CustomerDetails object|&nbsp;|
|historyItemProducts|array of Product objects|&nbsp;|


#### address Object

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


#### customerDetails Object

|field|format|example|
|---|---|---|
|transCustomerId|string|12345|
|transCustomerEmail|string|example@email.com|
|transCustomerFirstName|string|John|
|transCustomerLastName|string|Doe|
|transCustomerLoyaltyCardNo|string|ABC123DEF456|
|transCustomerHasLoyalty|boolean|true|


#### product Object

|field|format|example|
|---|---|---|
|productCustomOptions|array of customOption objects|&nbsp;|
|description|string|Make every beat count with Fitbit Charge 2 - a heart rate and fitness wristband that tracks activity, exercise and sleep, includes advanced fitness features and displays real-time stats on a large display.|
|productId|auto-populated string|123|
|productImages|array of image urls|http://domain.com/path/image.jpg|
|productThumbs|array of thumbnail image url arrays.|[ http://domain.com/path/thumb400.jpg, http://domain.com/path/thumb800.jpg, http://domain.com/path/thumb1600.jpg ]|
|productMerchantId|string|brookstone|
|productOptionsAvailable|array of Variant objects|&nbsp;|
|productOptions|array of Option objects|&nbsp;|
|productPrice|decimal|129.00|
|productSubtitle|string|Heart Rate and Fitness Wristband|
|productTitle|string|Fitbit Charge 2|

#### customOption object

|field|format|example|
|---|---|---|
|customOptionId|int|123|
|customOptionsSortOrder|int|123|
|customOptionsTitle|string|Material|
|customOptionsType|string|TODO|
|customOptionValues|array of CustomOptionValue objects|&nbsp;|


#### customOptionValue object

|field|format|example|
|---|---|---|
|customOptionValueSortOrder|int|123|
|customOptionValueTitle|string|cotton|
|customOptionValueValueId|string|100% Cotton|


#### variant Object

|field|format|example|
|---|---|---|
|array of Combination objects|array|&nbsp;|

#### combination Object
A combination object represents one unique combination of options for this product. For example, if a shirt has 4 sizes, and 3 colors, it should have 12 combination objects (assuming all combinations are available). The number of key/value pairs in a combination object will vary, and is dependent upon the number of options in the product. A product with size, color, and pattern, would have three key/value pairs in each combination.

|field|format|example|
|---|---|---|
|code|string|color|
|value|string|Red|


#### option Object

An option represents a choice. For example, Color or Size on a clothing item.

|field|format|example|
|---|---|---|
|optionLabel|string|Color|
|optionCode|string|color|
|optionExtraInfo|string displayed to the user, provides additional info about the option|Color may differ from shown.|
|optionValues|array of optionValue objects|&nbsp;|


#### optionValue Object
OptionValue objects are key/value pairs, each describing one choice within an option. For example, if the option is Color, you might have three optionValue objects, one for the red option, one for blue, and one for green.

|field|format|example|
|---|---|---|
|optionValueLabel|string|Red|
|optionValueValue|string|5|


