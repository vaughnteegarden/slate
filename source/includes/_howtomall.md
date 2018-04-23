

## Mall flow


<img src="images/JWT-mall-flow.png" style="margin:6px 0;"><br/>[ <a href="images/JWT-mall-flow.png" target="_blank">View full size</a> ]

Mall is the only method for finding products that does not require an ad scan. A consumer enters the mall by clicking on a "Mall" navigation option within the mobile app. The Mall showcases active merchants in an attractive layout that is condusive to casual browsing and subsequent purchasing. 

Once a merchant is selected, the consumer shifts into category/product browse mode. Once a product has been selected, purchasing follows one of the previous examples of Instant Buy Flow or Cart Buy Flow (minus the scan step).



#### 1. Get List of Merchants in the mall

```swift
import UIKit
import RezolveSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

              session.productManager.getMerchants(callback: { (listOfMerchant: Array<Merchant>) in


              }, errorCallback: { print($0) })

            }, errorCallback: { print($0) })
        })
    }
}
```
```java
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getProductManager();

        // get merchants
        // get merchants
        myProductManager.getMerchants(this, new ProductCallback() {
            @Override
            public void onGetMerchantsSuccess(List<Merchant> list) {
                for(Merchant merchant : list) {
                    String merchant_id = merchant.getId();
                    String name = merchant.getName();
                    String tagline = merchant.getTagline();
                    String banner = merchant.getBanner();
                    List<String> bannerThumb = merchant.getBannerThumbs();
                    List<String> logoThumbs = merchant.getLogoThumbs();
                }
            }
            @Override
            public void onFailure(HttpResponse httpResponse) {
                //handle error
            }
        });
    }


    @Override
    public void onGetMerchantsSuccess(List<Merchant> list) {
        // list merchants
        for(Merchant merchant : list) {
            String merchant_id = merchant.getId();
            String name = merchant.getName();
            String tagline = merchant.getTagline();
            String banner = merchant.getBanner();
            List<String> bannerThumb = merchant.getBannerThumbs();
            List<String> logoThumbs = merchant.getLogoThumbs();
        }
    }

    @Override
    public void onFailure(HttpResponse httpResponse) {
        // handle error
    }
}
```

First, initialize `productManager`, and call `GetMerchants`, providing an implementation of ProductCallback as a parameter. This will return an array of Merchant objects. Parse each merchant object to get the `id`, `name`, `tagline`, `banner`, `bannerThumbs`, and `logoThumbs`.





### 2. Get list of first-level Categories for the selected Merchant
``` swift
import UIKit
import RezolveSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

              session.productManager.getMerchants(callback: { (listOfMerchant: Array<Merchant>) in

                  var merchant = listOfMerchant.first!

                  session.productManager.getCategories(merchantId: merchant.id, categoryId: nil, callback: { category in


                  }, errorCallback: { print($0) })

              }, errorCallback: { print($0) })

            }, errorCallback: { print($0) })
        })
    }
}
```
```java
private final static String API_KEY = "your_api_key";
private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	ProductInterface productInterface;

	ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getProductManager();

	// get categories
	String merchantId = "123";
	myProductManager.getCategories(merchantId,this);
}

@Override
public void onGetCategoriesSuccess(Category category) {
	// parse Category object 
	String category_id = category.getId();
	String parentId = category.getParentId();
	String name = category.getName();
	Boolean hasCategories = category.hasCategories();
	Boolean hasProduct = category.hasProducts();
	String image = category.getImage();
	List<String> imageThumbs = category.getImageThumbs();
	String catparentId = category.getParentId();
	List<Category> children = category.getCategories();

	for (Category subcategory : children){
		subcategory.getId();
		// ... etc
	}
}

```

By looking at the Boolean `hasCategories`, we can tell if the category contains any subcategories. If so, we can use `ProductManager.getCategories` to fetch them.


### 3. If boolean hasProducts is true, get product info (onCategoryProductsResult)
``` swift
func onCategoryResult(category: RezolveCategory) -> Void {

  let pageNavigation: PageNavigation = PageNavigation(count: 10, pageIndex: 0, sortBy: nil, sort: PageNavigationSort.ASC)

  session.productManager.getProducts(merchantId: MERCHANT_ID, categoryId: category.id, pageNavigation: pageNavigation, callback: { (pageResult: PageResult<DisplayProduct>) in

  }, errorCallback: { print($0) })
}}
```
```java
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY,
        ENVIRONMENT).getRezolveSession().getProductManager();

        // get products
        myProductManager.getProducts(this, merchantId3, categoryId, count, page, 
        sort_by_field, sort_direction, this);
    }

    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
        Integer count = pageResult.getCount();
        Integer total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> displayProducts = pageResult.getProducts();

        for (Link link: links){
            Integer linkcount = link.getCount();
            Integer page = link.getPage();
            String sort = link.getSort();
            String sortBy = link.getSortBy();
        }

        for (DisplayProduct displayProduct : displayProducts){
            String id = displayProduct.getId();
            List<String> imageThumbs = displayProduct.getImageThumbs();
            String image = displayProduct.getImage();
            float price = displayProduct.getPrice();
            String name = displayProduct.getName();
            String categoryId = displayProduct.getCategoryId();
        }
    }
}
```

Similarly, we can check the Boolean "hasProducts" to determine if the category has products. If it does, we can fetch them using `ProductManager.getProducts`.

### 4. Repeat for navigation until ready to purchase

Repeat steps 2 & 3 above as the consumer browses through categories. Once the consumer selects a product and is ready to purchase, the process is the same as in the Instant Buy or Cart Buy flows. 