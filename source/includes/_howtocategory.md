

## Category Scan flow



<img src="images/JWT-shoppable-ad-flow-category-scan.png" style="margin:6px 0;"><br/>[ <a href="images/JWT-shoppable-ad-flow-category-scan.png" target="_blank">View full size</a> ]

Shoppable Ads can do more than link to a single product, it can link to a category of products in your Rezolve Commerce Engine. Scanning an ad that contains a category link will bring up a list of subcategories and products in that category.

Once a product has been selected, purchasing follows one of the previous examples of Instant Buy Flow or Cart Buy Flow (minus the scan step).



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
	
	// If category hasCategories, fetch children categories (onCategoryResult)
		session.productManager.getCategories(merchantId: MERCHANT_ID, categoryId: category.id, callback: { category in

		}, errorCallback: {

		  print($0) // handle error
		})
    }

    func onCategoryProductsResult(category: RezolveCategory, productsPage: PageResult<DisplayProduct>) -> Void {

	// If category hasProducts, get product info (onCategoryProductsResult)
		let pageNavigation: PageNavigation = PageNavigation(count: 10, pageIndex: 0, sortBy: nil, sort: PageNavigationSort.ASC)

		session.productManager.getProducts(merchantId: MERCHANT_ID, categoryId: category.id, pageNavigation: pageNavigation, callback: { (pageResult: PageResult<DisplayProduct>) in

		}, errorCallback: { print($0) })
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
        scanManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT)
          .getRezolveSession().getScanManager(this, true);

          //start video scan to acquire image
          scanManager.startVideoScan(this, rezolveScanView);
    }


    // if scan media contains a category link, onCategoryResult fires and
    // returns a Category object. Parse the category object to obtain 
    // a list of products in that category, and a list of subcategories of that category
    @Override
    public void onCategoryResult(Category category, String s) {
		String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catParentId = category.getParentId();
        List<Category> children = category.getCategories();
		
		// get category placement
        Placement.CategoryPlacement categoryPlacement = category.getCategoryPlacement();
        String categoryAdId = categoryPlacement.getAdId();
        String categoryPlacementId = categoryPlacement.getPlacementId();

        // optional:  get paginated subcategory results
        PageResult<Category> categoryPageResult = category.getCategoryPageResult();
        
        // optional: get paginated product results
        PageResult<DisplayProduct> pageResult = category.getProductPageResult();

        // get products from pageResult...
        Integer count = pageResult.getCount();
        Integer total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> displayProducts = pageResult.getItems();

        for (Link link: links){
            Integer linkcount = link.getCount();
            Integer page = link.getPage();
            String sort = link.getSort();
            String sortBy = link.getSortBy();
        }

        for (DisplayProduct displayProduct : displayProducts){
            String DPid = displayProduct.getId();
            List<String> DPimageThumbs = displayProduct.getImageThumbs();
            String DPimage = displayProduct.getImage();
            float DPprice = displayProduct.getPrice();
            String DPname = displayProduct.getName();
            String DPcategoryId = displayProduct.getCategoryId();
        }
    }
}

```

First, initialize `scanManager`, and enable the scan screen using `session.startVideo()`, and capture a watermarked image. The scanManager returns a `Category` object, which is then parsed to determine its contents.


### 2. Repeat for navigation until ready to purchase

Repeat step 1 above as the consumer browses through categories. Once the consumer selects a product and is ready to purchase, the process is the same as in the Instant Buy or Cart Buy flows. Note: a DisplayProduct does not contain full information on a product. You will need to call getProduct to display the detail view of the Product.