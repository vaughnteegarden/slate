## Module: MerchantManager

MerchantManager is an aggregate of Session. 

This class provides the ability to retrieve merchants, and info about them.


### Method: getMerchants

```swift
TODO
```
```java
public class Merchants extends AppCompatActivity implements MerchantInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        MerchantInterface merchantInterface;

        MerchantManager merchantManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getMerchantManager();

        // get merchants
        merchantManager.getMerchants(this, this);
    }

    @Override
    public void onGetMerchantsSuccess(List<Merchant> list) {
        for(Merchant merchant : list) {
            String merchant_id = merchant.getId();
            String name = merchant.getName();
            String tagline = merchant.getTagline();
            String banner = merchant.getBanner();
            List<String> bannerThumbs = merchant.getBannerThumbs();
            List<String> logoThumbs = merchant.getLogoThumbs();
        }
    }
}
```
Method signature: `merchantManager.getMerchants( [callback or interface] )`

Android users must also pass in context as the first argument.

The method returns an array of `Merchant` objects.

#### merchant Object

|field|format|example|
|---|---|---|
|id|string|123|
|name|string|Brookstone|
|tagline|string|A World of Innovation|
|banner|string|http://domain.com/path/banner.jpg|
|bannerThumbs|array of url strings for thumbnail images|&nbsp;|
|logoThumbs|array of url strings for logo images|&nbsp;|

How to use the returned images: the `banner` is typically used as a header background, and the image represented by `logo` is overlaid on top. The banner and logo come in a variety of thumbnailed sizes, depending on the original image side.




### Method: getShippingMethods

Gets shipping methods for a specific merchant.

```swift
TODO
```
```java
public class Merchants extends AppCompatActivity implements MerchantInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        MerchantInterface merchantInterface;

        MerchantManager merchantManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getMerchantManager();

        // get shipping methods for a merchant
        String merchantId = "123";
        merchantManager.getShippingMethods(merchantId,this);

    }

    @Override
    public void onGetShippingMethodsSuccess(List<Shipping> list) {
        for (Shipping shipping : list ) {
            ShippingDetails shippingDetails = shipping.getShippingDetails();
				StoreDetails storeDetails = shippingDetails.getStoreDetails();
					String description = storeDetails.getDescription();
					String email = storeDetails.getEmail();
					String name = storeDetails.getName();
					String pickupStore = storeDetails.getPickupStore();
					String telephone = storeDetails.getTelephone();
					RezolveLocation location = storeDetails.getLocation();
						double latitude = location.getLatitude();
						double longitude = location.getLongitude();
            		StoreAddress storeAddress = storeDetails.getStoreAddress();
						String storeCity = storeAddress.getCity();
						String storeCountry = storeAddress.getCountry();
						String storePostCode = storeAddress.getPostCode();
						String storeRegion = storeAddress.getRegion();
						String storeStreet1 = storeAddress.getStreet1();
						String storeStreet2 = storeAddress.getStreet2();
            ShippingMethod shippingMethod = shipping.getShippingMethod();
				String carrierCode = shippingMethod.getCarrierCode();
				String displayName = shippingMethod.getDisplayName();
            	List<ExtensionAttribute> extensionAttributes = shippingMethod.getExtensionAttributes();
					for (ExtensionAttribute extensionAttribute : extensionAttributes) {
						String extensionCode = extensionAttribute.getCode();
						String extensionValue = extensionAttribute.getValue();
					}
            	String methodCode = shippingMethod.getMethodCode();
        }
    }
}
```

Method signature: `merchantManager.getShippingMethods( merchantId, [callback or interface] )`

You must pass a merchantId and a callback or interface.

The method returns an array of `Shipping` objects.

#### Shipping Object

|field|format|example|
|---|---|---|
|shippingDetails|object|&nbsp;|
|shippingMethod|object|&nbsp;|

#### ShippingDetails Object

|field|format|example|
|---|---|---|
|storeDetails|object|&nbsp;|

#### StoreDetails Object

|field|format|example|
|---|---|---|
|description|string|5th and Main|
|email|string|email@domain.com|
|name|string|Barnaby's 152|
|pickupStore|string|abc123|
|telephone|string|+1-444-333-2222|
|location|RezolveLocation object|&nbsp;|
|storeAddress|StoreAddress object|&nbsp;|

#### StoreAddress Object

|field|format|example|
|---|---|---|
|storeCity|string|Berville|
|storeCountry|string|US|
|storePostCode|string|33445|
|storeRegion|string|Washington|
|storeStreet1|string|123 South St|
|storeStreet2|string|Suite 2|

#### ShippingMethod Object

|field|format|example|
|---|---|---|
|carrierCode|string|123|
|displayName|string|Ground|
|extensionAttributes|array of ExtensionAttribute objects|&nbsp;|
|methodCode|string|123|

#### ExtensionAttribute Object

|field|format|example|
|---|---|---|
|code|string|123|
|value|string|abc|