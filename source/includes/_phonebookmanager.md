## Module: PhonebookManager

The PhonebookManager is an aggregate of Session.

This module provides CRUD functions for consumer phone numbers. The consumer may add one or more phone numbers to their account.

### Method: phonebookManager.create

``` swift
TODO
```
``` java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        Phone phone = new Phone();
        phone.setName("Home phone");
        phone.setPhone("555-555-1212");

        myPhonebookManager.create(phone, this);
    }

    @Override
    public void onPhonebookCreateSuccess(Phone phone) {
        String phoneId = phone.getId();
        String phoneName = phone.getName();
        String phoneNumber = phone.getPhone();
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```
Method signature: `session.phonebookManager.create( phone, [callback or interface] )`

You must pass in a valid `phone` object with the `id` field blank.

The method returns the created `phone` object with `id` upon success.

#### phone Object

|field|format|example|
|---|---|---|
|phoneId|auto-assigned string|123abc|
|phoneName|string|Home phone|
|phonePhone|string|+1(555)555-1212|

### Method: phonebookManager.update

```swift
TODO
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        String newNumber = "555-555-1313";
        phone.setId("123");
        phone.setName("HomePhone");
        phone.setPhone(newNumber);
        myPhonebookManager.update(phone,this);
    }

    @Override
    public void onPhonebookUpdateSuccess(Phone phone) {
        String phoneId = phone.getId();
        String phoneName = phone.getName();
        String phoneNumber = phone.getPhone();
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

Method signature: `session.phonebookManager.update( phone, [callback or interface] )`

You must pass in a valid `phone` object with `id` populated.

The method returns the updated `phone` object upon success.


### Method: phonebookManager.delete

```swift
TODO
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        Phone phone = new Phone();
        phone.setName("Home phone");
        phone.setPhone("555-555-1212");

        myPhonebookManager.delete(phone,this);
    }

    @Override
    public void onPhonebookDeleteSuccess(HttpResponse httpResponse) {
        // display post-deletion confirmation
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}

```

Method signature: `session.phonebookManager.delete( phone, [callback or interface] )`

You must pass in the `phone` object to delete.



### Method: phonebookManager.get

```swift
TODO
```
```java
public class phoneBook extends AppCompatActivity implements PhonebookInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        PhonebookManager myPhonebookManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getPhonebookManager();

        myPhonebookManager.get(this);
    }

    @Override
    public void onPhonebookGetSuccess(List<Phone> list) {
        for (Phone phone:list) {
            String phoneId = phone.getId();
            String phoneName = phone.getName();
            String phoneNumber = phone.getPhone();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

Method signature: `session.phonebookManager.get( [callback or interface] )`

The method takes no parameters.

The method returns an array of `Phone` objects.

