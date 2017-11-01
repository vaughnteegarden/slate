

## Shoppable Ads, Category Scan flow



<img src="images/ShoppableAdFlowInstantBuy.png" style="margin:6px 0;"><br/>[ <a href="images/ShoppableAdFlowInstantBuy.png">View full size</a> ]

Shoppable Ads can do more than link to a single product, it can link to a category of products in your Rezolve Commerce Engine. Scanning an ad that contains a category link will bring up a list of subcategories and products in that category.

Once you have navigated to a product, purchasing follows one of the previous two examples (either Instant Buy or Cart Buy).



#### 1. Scan a category shoppable ad,  get a getCatalog response

```swift
class ViewController: UIViewController, ProductDelegate {

    var session: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

                self.session = session
                self.session?.getScanManager().productResultDelegate = self
                self.session?.getScanManager().startVideoScan(scanCameraView: self.view as! ScanCameraView)

            }, errorCallback: { print($0) })
        }
    }


    func onError(error: String) -> Void {

      // handle error
    }

    func onStartRecognizeImage() -> Void {

      // suggestion: show a loading indicator
    }

    func onFinishRecognizeImage() -> Void {

      // suggestion: alert user with some sound
    }

    func onProductResult(product: Product) -> Void {

    }

    func onCategoryResult(category: RezolveCategory) -> Void {

    }

    func onCategoryProductsResult(category: RezolveCategory, productsPage: PageResult<DisplayProduct>) -> Void {

    }
}
```

```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface, View.OnClickListener {

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


    // if scan media contains a category link, onCategoryResult fires and
    // returns a Category object
    @Override
    public void onCategoryResult(Category category, String s) {
        String categoryId = category.getId();
        String categoryParentId = category.getParentId();
        String categoryName = category.getName();
        List<String> categoryImageThumbs = category.getImageThumbs();
        String categoryImage = category.getImage();
        List<Category> categoryChildren = category.getCategories();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
    }
}

```

First, initialize `scanManager`, and enable the scan screen using `session.startVideo()`, and capture a watermarked image. The scanManager returns a `Category` object, which is then parsed to determine its contents.



### 2. If boolean hasCategories is true, extract subcategories using getCategories
``` swift
func onCategoryResult(category: RezolveCategory) -> Void {

  session.productManager.getCategories(merchantId: MERCHANT_ID, categoryId: category.id, callback: { category in


  }, errorCallback: {

      print($0) // handle error
  })
}
```
```java
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();


        myProductManager.getCategories(merchantId,this);

    }

    @Override
    public void onGetCategoriesSuccess(Category category) {
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

}
```

By looking at the Boolean `hasCategories`, we can tell if the category contains any subcategories. If so, we can use `ProductManager.getCaategories` to fetch them.


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

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY,
        RezolveSDK.Env.PRODUCTION).getRezolveSession().getProductManager();

        // get products
        myProductManager.getProducts(merchantId3, categoryId, count, page, 
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