## Module: WalletManager

**Topics**

* <a href="#method-walletmanager-create">walletManager.create</a>
* <a href="#method-walletmanager-update">walletManager.update</a>
* <a href="#method-walletmanager-delete">walletManager.delete</a>
* <a href="#method-walletmanager-get">walletManager.get</a>
* <a href="#method-walletmanager-getall">walletManager.getAll</a>

The WalletManager is an aggregate of Session.

This module provides CRUD functions for payment cards.  The consumer may add one or more payment cards to their account.

### Method: walletManager.create

```swift
class WalletViewController: UIViewController {

	let API_KEY: String = "your_api_key"
    let ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        let paymentCard = PaymentCard()

        paymentCard.brand = "MASTER"
        paymentCard.type = "credit"
        paymentCard.cardHolderName = "John Doe"
        paymentCard.expiresOn = "12/18"
        paymentCard.pan = "9999999999999999"
        paymentCard.addressId = "123" // address.id
        paymentCard.shortName = "My Mastercard"

        self.mySession?.walletManager.create(
            paymentCard: paymentCard) { (remotePaymentCard: PaymentCard) in

                let paymentCardId: String = remotePaymentCard.id
        }
    }
}
```
```java
// create payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getWalletManager();

        // create a new payment card
        PaymentCard myCard = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.create(myCard, this);
    }

    @Override
    public void onWalletCreateSuccess(PaymentCard paymentCard) {
        // display success message
    }
}
```

Method signature: `session.walletManager.create( [callback or interface] )`

You must pass in a `paymentCard` object with the `id` field blank.

The SDK will encrypt the PAN (payment account number) using the session public key before transmission.

The method returns the created `paymentCard` object with `id` upon success.

#### paymentCard Object

|field|format|example|
|---|---|---|
|id|auto-assigned string|123|
|short_name|string|black amex|
|pan|string|gWkxi0kiMJnEagRpm5dXmggWOrblj9+wsasm8Ysjp|
|pan6|string|123456|
|pan4|string|1234|
|brand|string|visa|
|type|string|debit|
|name_on_card|string|John Curtis|
|expires_on|string|08/17|
|valid_from|string|08/14|
|address_id|string|123|

### Method: walletManager.update

```swift
class WalletViewController: UIViewController {

	let API_KEY: String = "your_api_key"
    let ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { 
         (remotePaymentCard: PaymentCard) in

            remotePaymentCard.shortName = "My Visa"

            self.mySession?.walletManager.update(paymentCard: remotePaymentCard) {
             (remotePaymentCard2: PaymentCard) in

                //handle result
            }
        }
    }
}
```
```java
// update payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getWalletManager();

        // update a payment card
        PaymentCard myCard2 = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.update(myCard, this);
    }

    @Override
    public void onWalletUpdateSuccess(PaymentCard paymentCard) {
        // display success message
    }
}
```

Method signature: `session.walletManager.update( paymentCard, [callback or interface] )`

You must pass in a valid `paymentCard` object with `id` populated.

The method returns the updated `paymentCard` object upon success.

### Method: walletManager.delete

```swift
class WalletViewController: UIViewController {

	let API_KEY: String = "your_api_key"
    let ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { (remotePaymentCard: PaymentCard) in

            self.mySession?.walletManager.delete(paymentCard: remotePaymentCard) { () in

                //handle callback
            }
        }
    }
}
```
```java
// delete payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getWalletManager();

        // delete a payment card
        PaymentCard myCard3 = new PaymentCard();
        myCard.setAddressId("123");
        myCard.setBrand("Bank of America");
        myCard.setExpiresOn("08/19");
        myCard.setNameOnCard("John Doe");
        myCard.setPan("4111111111111111");
        myCard.setShortName("My Amex");
        myCard.setType("Amex");
        myCard.setValidFrom("08/16");
        myWalletManager.delete(myCard, this);
    }

    @Override
    public void onWalletDeleteSuccess(HttpResponse httpResponse) {
        // display success message
    }
}
```

Method signature: `session.walletManager.delete( id, [callback or interface] )`

You must pass in the `paymentCard` object to delete.

### Method: walletManager.get

```swift
class WalletViewController: UIViewController {

	let API_KEY: String = "your_api_key"
    let ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.get(id: "123") { (remotePaymentCard: PaymentCard) in

            let paymentCardId: String = remotePaymentCard.id
        }
    }
}
```
```java
// get a payment card using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getWalletManager();

        // list a single payment card
        String card_id = "123";
        myWalletManager.get(card_id,this);
    }

    @Override
    public void onWalletGetSuccess(PaymentCard paymentCard) {
        // show Card info
        String cardId = paymentCard.getId();
        String addressId = paymentCard.getAddressId();
        String brand = paymentCard.getBrand();
        String expiresOn = paymentCard.getExpiresOn();
        String nameOnCard = paymentCard.getNameOnCard();
        String pan4 = paymentCard.getPan4();
        String pan6 = paymentCard.getPan6();
        String shortName = paymentCard.getShortName();
        String type = paymentCard.getType();
        String validFrom = paymentCard.getValidFrom();
    }
}
```

Method signature: `session.walletManager.get( id, [callback or interface] )`

You must pass in the `id` of the `paymentCard` to get.

The method returns a `paymentCard` object.

### Method: walletManager.getAll

```swift
class WalletViewController: UIViewController {

	let API_KEY: String = "your_api_key"
    let ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        self.mySession = ... // initialize session

        self.mySession?.walletManager.getAll() { 
         (listOfPaymentCard: Array<PaymentCard>) in

            //handle the results
        }
    }
}
```
```java
// get all payment cards using WalletInterface
public class Wallet extends AppCompatActivity implements WalletInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        WalletManager myWalletManager = RezolveSDK.getInstance(API_KEY, 
         ENVIRONMENT).getRezolveSession().getWalletManager();

        // list all payment cards
        myWalletManager.getAll(this);
    }

    @Override
    public void onWalletGetAllSuccess(List<PaymentCard> list) {
        // handle getAll response here
        for(PaymentCard paymentCard : list) {
            String cardId = paymentCard.getId();
            String addressId = paymentCard.getAddressId();
            String brand = paymentCard.getBrand();
            String expiresOn = paymentCard.getExpiresOn();
            String nameOnCard = paymentCard.getNameOnCard();
            String pan4 = paymentCard.getPan4();
            String pan6 = paymentCard.getPan6();
            String shortName = paymentCard.getShortName();
            String type = paymentCard.getType();
            String validFrom = paymentCard.getValidFrom();
        }
    }
}
```

Method signature: `session.walletManager.getAll( [callback or interface] )`

The method returns an array of `paymentCard` objects.





