## Module: UserActivityManager

Activity is an aggregate of Session.

The activity module handles order history and other historical data.

### Method: getOrders

```swift
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
public class Activities extends AppCompatActivity implements UserActivityInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        UserActivityManager myActivityManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getUserActivityManager();

        // get transactions from User Activity Manager
        myActivityManager.getOrders(this);
    }

    @Override
    public void onGetOrdersSuccess(List<Transaction> list) {
        for(Transaction transaction : list){
            Map<String, String> deliverySettings = transaction.getDeliverySettings();
            float finalPrice = transaction.getFinalPrice();
            Map<String, Double> geoLoc = transaction.getGeoLoc();
            List<TransactionItem> items = transaction.getItems();
            Map<String, String> loyaltySettings = transaction.getLoyaltySettings();
            String merchant_id = transaction.getMerchantId();
            String pan4 = transaction.getPan4();
            String payWith = transaction.getPayWith();
            String status = transaction.getStatus();
            String timestamp = transaction.getTimestamp();
            String type = transaction.getType();

            for (String key : geoLoc.keySet()) {
                String label = key;
                Double value = geoLoc.get(key);
            }

            for (TransactionItem transactionItem : items) {
                transactionItem.getId();
                transactionItem.getImageUrl();
                transactionItem.getPrice();
                transactionItem.getQty();
                transactionItem.getTitle();
            }

            for (String key : loyaltySettings.keySet()) {
                String label = key;
                String value = loyaltySettings.get(key);
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
|id|string|80695c|
|payWith|string|123123123|
|type|string|scan|
|status|string|processing|
|timestamp|long (unix timestamp)|1493130773|
|finalPrice|decimal|129.00|
|deliveryAddressId|String|123|
|pan4|string|4111|
|geoLoc|object|&nbsp;|
|items|array of transactionItem objects|&nbsp;|

#### geoLoc Object

|field|format|example|
|---|---|---|
|lat|double|51.5237737|
|long|double|-0.1585369|

#### transactionItem object

|field|format|example|
|---|---|---|
|id|string|38389992|
|price|decimal|129.00|
|qty|decimal|1|
|title|string|Fitbit Charge 2|
|imageUrl|string|http://domain.com/path/image.jpg|







