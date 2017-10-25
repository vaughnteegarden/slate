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
// get transactions using UserActivityManager
public class userActivity extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.SANDBOX).getRezolveSession().getUserActivityManager();

        // get transactions from User Activity Manager
        myActivityManager.getOrders(this);

        // The following are reserved for future functionality:
        // myActivityManager.getActsHistory();
        // myActivityManager.getActById();
        // myActivityManager.getScansHistory();
        // myActivityManager.getScanById();
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

    @Override
    public void onGetOrdersSuccess(List<Transaction> list) {
        for(Transaction transaction : list){

            String transactionStatus = transaction.getStatus();
            String transactionTimestamp = transaction.getTimestamp();
            float transactionAmount = transaction.getAmount();
            String transactionOrderId = transaction.getOrderId();
            String transactionLastUpdated = transaction.getLastUpdated();
            TransactionItem transactionItem = transaction.getItem();

            // get properties of transactionItem  object
            Address transactionItemAddressDetails = transactionItem.getAddressDetails();
            CustomerDetails transactionItemCustomerDetails = transactionItem.getCustomerDetails();
            RezolveLocation transactionItemLocation = transactionItem.getLocation();
            List<CheckoutProduct> transactionItemProducts = transactionItem.getProducts();

            // get properties of transactionItemAddressdetails object
            String transAddressId = transactionItemAddressDetails.getId();
            String transAddressShortName = transactionItemAddressDetails.getShortName();
            String transAddressCity = transactionItemAddressDetails.getCity();
            String transAddressCountry = transactionItemAddressDetails.getCountry();
            String transAddressLine1 = transactionItemAddressDetails.getLine1();
            String transAddressLine2 = transactionItemAddressDetails.getLine2();
            String transAddressState = transactionItemAddressDetails.getState();
            String transAddressZip = transactionItemAddressDetails.getZip();

            // get properties of transactionItemCustomerDetails object
            String transCustomerId = transactionItemCustomerDetails.getCustomerId();
            String transCustomerEmail = transactionItemCustomerDetails.getEmail();
            String transCustomerFirstName = transactionItemCustomerDetails.getFirstName();
            String transCustomerLastName = transactionItemCustomerDetails.getLastName();
            String transCustomerLoyaltyCardNo = transactionItemCustomerDetails.getLoyaltyCardNumber();
            Boolean transCustomerHasLoyalty = transactionItemCustomerDetails.isHasLoyaltyCard();

            // get properties of transactionItemLocation object
            String transLocationType = transactionItemLocation.getLocationType();
            double transLatitude = transactionItemLocation.getLatitude();
            double transLongitude = transactionItemLocation.getLongitude();

            // get properties of transactionItemProducts array
            for(CheckoutProduct checkoutProduct : transactionItemProducts){
                int checkoutProductId = checkoutProduct.getId();
                float checkoutProductQty = checkoutProduct.getQty();
                List<ConfigurableOption> checkoutProductConfigurableOptions = checkoutProduct.getConfigurableOptions();

                // get name/value pairs of productConfigurableOptions array
                for(ConfigurableOption checkoutProductConfigurableOption : checkoutProductConfigurableOptions){
                    String checkoutProductConfigurableOptionCode = checkoutProductConfigurableOption.getCode();
                    int checkoutProductConfigurableOptionValue = checkoutProductConfigurableOption.getValue();
                }
            }
        }
    }
}
```

Method signature: `session.getOrders( [callback or interface] )`

The method returns an array of `transaction objects`.

#### transaction Object

|field|format|example|
|---|---|---|
|status|string|Complete|
|timestamp|long (unix timestamp)|1493130773|
|amount|decimal|129.00|
|orderId|string|abc1234|
|lastUpdated|long (unix timestamp)|1493130773|
|transactionItem|TransactionItem object|&nbsp;|

#### transactionItem object

|field|format|example|
|---|---|---|
|transactionItemAddressDetails|Address object|&nbsp;|
|transactionItemCustomerDetails|CustomerDetails object|&nbsp;|
|transactionItemLocation|RezolveLocation object|&nbsp;|
|transactionItemProducts|array of CheckoutProduct objects|&nbsp;|

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

#### rezolveLocation Object

|field|format|example|
|---|---|---|
|transLocationType|string||
|transLatitude|decimal|66.123|
|transLongitude|decimal|-140.889|

#### checkoutProduct Object

|field|format|example|
|---|---|---|
|checkoutProductId|integer|123|
|checkoutProductQty|decimal|123|
|checkoutProductConfigurableOptions|array of configurableOption objects (key/value pairs)|&nbsp;|

#### configurableOption Object

|field|format|example|
|---|---|---|
|checkoutProductConfigurableOptionCode|string|Color|
|checkoutProductConfigurableOptionValue|integer|123|

