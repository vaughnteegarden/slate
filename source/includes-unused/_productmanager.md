## Module: ProductManager

**Topics**

* <a href="#usage-scenarios">Usage Scenarios</a>
* <a href="#method-productmanager-getcatgories">ProductManager.getCatgories</a>
* <a href="#method-productmanager-getcategory">ProductManager.getCategory</a>
* <a href="#method-productmanager-getparentcategory">ProductManager.getParentCategory</a>
* <a href="#method-productmanager-getproducts">ProductManager.getProducts</a>
* <a href="#method-productmanager-getproduct">ProductManager.getProduct</a>
* <a href="#method-productmanager-getcartproduct">ProductManager.getCartProduct</a>
* <a href="#method-productmanager-getproductsandcategories">ProductManager.getProductsAndCategories</a>

ProductManager is an aggregate of Session.

The ProductManager module offers methods specific to the merchant product catalog; getting catalogs and products.

### Usage Scenarios


**For Mall Browse**

* `getCategories` is used the first time you request categories from a merchant. It returns the merchant catalog starting at the catalog root. 
* Note that the Android version of `getCategories` returns both category and product info, while IOS returns only category info. IOS will need to follow with a `getProuducts` call if `hasProducts` is true. 
* `getProductsAndCategories` is then used by both Android and IOS to request subsequent paginated category and product lists as the consumer navigates


**For Scanning a Category Engagement**

* `getCategory` is used to initially fetch the desired category, (IOS use `getCategories` with an optional category id)
* IOS will need to follow with a `getProuducts` call if `hasProducts` is true. 
* `getProductsAndCategories` is used to request subsequent paginated category and product lists as the consumer navigates


**In Both Above Scenarios**

* `getProduct`  is used to get more information about a product, when displaying a product detail page.
* `getParentCategory` is a convenience method in the Android Rezolve SDK only, to get the parent category when navigating back up the category tree. IOS Developers should use `Category.parentId` to call `getCategories`, and use the returned object to call `getProductsAndCategories`.
* `getCartProduct` is a convenience method in the Android Rezolve SDK only, to get information about a product in the cart. IOS developers should use `getProduct`.


### Method: ProductManager.getCatgories

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "12"
let CATEGORY_ID = Int32(70)

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

                session.productManager.getCategories(
                    merchantId: MERCHANT_ID, 
                    category: Category(id: CATEGORY_ID), 
                    callback: { responseCategory in

                        print(responseCategory.id)
                        print(responseCategory.parentId)
                        print(responseCategory.name)
                        print(responseCategory.image)
                        print(responseCategory.imageThumbs)
                        print(responseCategory.hasProducts)
                        print(responseCategory.hasCategories)
                        if responseCategory.hasCategories {                     
                            responseCategory.categories.forEach { subCategories in
                                print(subCategories.id)
                                print(subCategories.parentId)
                                print(subCategories.name)

                                // ...
                            }   
                        }

                }, errorCallback: {
                    print($0) // handle error
                })
            })
        }
    }
}
```
```java
// get root category and first level child categories using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getProductManager();

		// get categories
        String merchantId = "123";
        myProductManager.getCategories(merchantId, this);  // "this" is productInterface
    }

    @Override
    public void onGetCategoriesSuccess(Category category) {
        String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hascategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catparentId = category.getParentId();
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

Android method signature: `session.getCategories( merchant_id, [callback or interface] )`

IOS method signature: `session.getCategories( merchant_id, category_id (nullable), [callback or interface] )`

You must pass in the `id` of the `merchant` to whose categories you wish to get.

The method returns an array of `category` objects owned by a specific merchant.  Specifically, it returns the *root category* and *first level* of child categories*.

For IOS, if you supply the category_id, you will get results for a specific category, instead of the root category. Send category_id as NULL to get the root category.


#### category Object

|field|format|example|
|---|---|---|
|id|string|2|
|parent_id|string|1|
|name|String|Fitness Wear|
|has_products|boolean|true|
|has_categories|boolean|false|
|image|string|http://domain.com/path/image.jpg|
|imageThumbs|array of url strings|&nbsp;|
|categoryParentId|string|123|
|categories|array of category objects|&nbsp;|
|categoryPlacement|Object containing information on the Engagement placement, if any|&nbsp;|
|categoryPageResult|object of paginated category results|&nbsp;|
|pageResult|array of paginated DisplayProduct results|&nbsp;|

Notes:

If `has_categories` is `false`, the `categories` array should be empty.

If `has_products` is `true`, call `getProducts` with the category `id` to get a product list.



### Method: ProductManager.getCategory

```swift
// This method no longer exists on IOS. Use getCategories instead.
```
```java
// get a single category using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getProductManager();

        // get single category
		// note: in practice you would pass in category object returned by getCategories method
        Category category = new Category();  
        String merchantId = "123";
        myProductManager.getCategory(merchantId, category, this); // "this" is productInterface
    }

    @Override
    public void onGetCategorySuccess(Category category) {
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
This method is Android-only. IOS should use `getCategories` and supply an optional category_id, which performs the same function. 

Method signature: `session.getCategory( merchant_id, category, [callback or interface] )`

The method returns a `category` object.

<aside class="notice">
Note: IOS uses the `getCatalogs` method to fetch both singular and multiple catalogs. To get all catalogs, simply set the `catalogId` to `nil`. To get a singular catalog, specify a `catalogId`.
</aside>



### Method: ProductManager.getParentCategory

This is an Android-only method that is a convenience proxy for the `getCateogry` method, used to get the parent category when navigating back up the category tree. IOS developers, use `getCategory`. 

```java
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ProductInterface productInterface;

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getProductManager();

        // get the parent category of a category
		String merchantId = "123";
		Category category = new Category();
        myProductManager.getParentCategory( merchantId, category, this);
    }

    @Override
    public void onGetCategorySuccess(Category category) {
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

Method signature: `session.getCategory( merchant_id, category, [callback or interface] )`

The method returns a `category` object.



### Method: ProductManager.getProducts

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "12"
let CATEGORY_ID = Int32(102)

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

                let pageNavigation: PageNavigation = PageNavigation(count: 10, pageIndex: 0, sortBy: nil, sort: PageNavigationSort.ASC)

                session.productManager.getProducts(
                    merchantId: MERCHANT_ID, 
                    categoryId: CATEGORY_ID, 
                    pageNavigation: pageNavigation,
                    callback: { pageResult in

                    pageResult.embedded.forEach { displayProduct in

                        // Iterate over embbeded RezolveSDK.DisplayProduct

                        print(displayProduct.id)
                        print(displayProduct.name)
                        print(displayProduct.price)
                        print(displayProduct.image)
                        displayProduct.thumbs.forEach {
                            // prints thumbs
                            print($0)    
                        }
                    }

                }, errorCallback: { httpResponse in
                    // handle error
                    print($0) 
                })
            })
        }
    }
}
```
```java
// get products using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getProductManager();

        // get products
        String merchantId = "123";
        Category category = new Category();

        PageNavigationFilter pageNavigationFilter = new PageNavigationFilter();
        pageNavigationFilter.setItemsPerPage(10);
        pageNavigationFilter.setPageNumber(1);
        pageNavigationFilter.setSortBy("name");   // values: name, price
        pageNavigationFilter.setSortDirection("asc");  // values: asc, desc

        // updated
        myProductManager.getProducts( merchantId, category, pageNavigationFilter, this);
    }

    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
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

Method signature: `session.getProducts( merchantId, category, pageNavigationFilter, [callback or interface] )`

You must pass a `merchantId`, `category` object, and a `pageNavivationFilter` object.

The method returns a `pageResult` object, containing an array of `DisplayProduct` objects.


#### pageResult Object

pageResult is a generic object used for combining pagination info with an array of returned objects. In the case of getProducts, this is an array of DisplayProducts.

|field|format|example|explanation|
|---|---|---|---|
|links|object| |object containing First, Next, Previous, Last links|
|count|integer|10|items per page, as requested in pageNavigation.count|
|total|integer|50|total items in SDKEntity array|
|SDKEntity|array| |array of DisplayProduct objects|

#### links Object

The links object controls pagignation and sorting of paginated results.

|field|format|example|explanation|
|---|---|---|---|
|count|integer|10|number of items per page|
|page|integer|1|what page of results you are requesting|
|sortBy|string|productName|must be a field of the pageResult > SDKEntity object array (see below)|
|sort|enum|one of: asc, desc|


#### displayProduct Object

The displayProduct object is used when rending a series of products. It contains a subset of the product object, suitable for displaying so the user can choose a product to view.

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|categoryId|string|123|
|name|string|Fitbit Charge 2|
|price|decimal|129.00|
|imageThumbs|array of thumbnail image urls (as strings)|http://domain.com/path/image.jpg|
|image|main product image url (as a string)|http://domain.com/path/image.jpg|




### Method: ProductManager.getProduct

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "12"
let CATEGORY_ID = Int32(102)
let PRODUCT_ID = "6"

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(
                entityId: entityId, 
                partnerId: partnerId, 
                device: signUpRequest.device, 
                callback: { (session: RezolveSession) in

                session.productManager.getProduct(
                    merchantId: MERCHANT_ID, 
                    categoryId: CATEGORY_ID, 
                    productId: PRODUCT_ID, 
                    callback:  { product in

                        print(product.id)
                        print(product.merchantId)
                        print(product.title)
                        print(product.subtitle)
                        print(product.price)
                        print(product.description)

                        product.images.forEach {
                            print($0)
                        }

                        product.options.forEach { option in
                            print(option.label)
                            print(option.code)
                            print(option.extraInfo)
                            option.values.forEach { optionValue in
                                print(optionValue.value)
                                print(optionValue.label)
                            }
                        }

                        product.optionAvailable.forEach {
                            $0.combination.forEach { variant in
                                print(code)
                                print(value)
                                print(id)
                            }
                        } 

                        product.customOptions.forEach {
                            print($0.isRequire)
                            print($0.optionId)
                            print($0.sortOrder)
                            print($0.title)
                            print($0.optionType)

                            $0.values.forEach { value in 
                                print(value.sortOrder)
                                print(value.title)
                                print(valueId)
                            }

                            $0.valuesId.forEach { valueId in 
                                print(valueId)
                            }

                            print($0.value)
                        }

                        print(product.productPlacement)

                }, errorCallback: {

                    print($0) // handle error
                })
            })
        }
    }
}
```
```java
// get a single product using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        ENVIRONMENT).getRezolveSession().getProductManager();
		
        // get single product
        Product product = new Product();
		Category category = new Category();
        String merchantId = "123";
		
        myProductManager.getProduct(merchantId, category, product, this);
    }

    @Override
    public void onGetProductSuccess(com.rezolve.sdk.model.shop.Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        String description = product.getDescription();
        List<String> images = product.getImages();
        List<String[]> thumbs = product.getImageThumbs();
        String merchant_id = product.getMerchantId();
        float price = product.getPrice();
        List<Option> options = product.getOptions();
        List<Variant> optionsAvail = product.getOptionsAvailable();
        List<CustomOption> customOptions = product.getCustomOptions();


        // iterate to get values of options list
        for (Option option : options) {
            String optionLabel = option.getLabel();
            String extraInfo = option.getExtraInfo();
            String optionCode = option.getCode();
            List<OptionValue> optionValues = option.getValues();

            for(OptionValue optionValue: optionValues){
                String value = optionValue.getValue();
                String label = optionValue.getLabel();
            }
        }

        // iterate to get values of variant list
        for (Variant variant : optionsAvail) {
            List<Combination> combinations = variant.getCombinations();
            // iterate to get values of composition hashmap
            for (Combination combination : combinations ){
                String comboValue = combination.getValue();
                String comboCode = combination.getCode();
            }
        }

        //iterate to get values of CustomOptions list
        for (CustomOption customOption : customOptions){
            List<CustomOptionValue> customOptionValues = customOption.getValues();
            int customOptionId = customOption.getOptionId();
            int customOptionSortOrder = customOption.getSortOrder();
            String customOptiontitle = customOption.getTitle();
            Boolean customOptionIsRequired = customOption.isRequired();

            // iterate to get values of customOptionValues
            for( CustomOptionValue customOptionValue : customOptionValues ){
                String customOptionValueTitle = customOptionValue.getTitle();
                int customOptionValuesSortOrder = customOptionValue.getSortOrder();
                String customOptionValueId = customOptionValue.getValueId();
            }
        }

    }
}
```

Method signature: `session.getProduct( merchant_id, catalog_id, product_id, [callback or interface] )`

You must pass a `merchant_id`, `catalog_id`, and a `product_id`.

The method returns a <a href="#product-object">`product object`</a> .


#### product Object

The displayProduct object is used when rending a series of products. It contains a subset of the product object, suitable for displaying so the user can choose a product to view.

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|merchant_id|string|brookstone|
|title|string|Fitbit Charge 2|
|subtitle|string|Heart Rate and Fitness Wristband|
|description|string|Make every beat count with Fitbit Charge 2 - a heart rate and fitness wristband that tracks activity, exercise and sleep, includes advanced fitness features and displays real-time stats on a large display.
|price|decimal|129.00|
|images|array of image urls|http://domain.com/path/image.jpg|
|thumbs|array of thumbnail image url arrays.|[ http://domain.com/path/thumb400.jpg, http://domain.com/path/thumb800.jpg, http://domain.com/path/thumb1600.jpg ]|
|options|array of Option objects|&nbsp;|
|optionAvailable|array of Variant objects|&nbsp;|
|customOptions|array of customOption objects|&nbsp;|

#### option Object

An option represents a choice. For example, Color or Size on a clothing item.

|field|format|example|
|---|---|---|
|label|string|Color|
|optionCode|string|color|
|extraInfo|string displayed to the user, provides additional info about the option|Color may differ from shown.|
|optionValues|array of optionValue objects|&nbsp;|

#### optionValue Object
OptionValue objects are key/value pairs, each describing one choice within an option. For example, if the option is Color, you might have three optionValue objects, one for the red option, one for blue, and one for green.

|field|format|example|
|---|---|---|
|label|string|Red|
|value|string|5|

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


> Example data for a product with three sizes, one color:

```json
"options_available": [
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "S"
                }
            ]
        },
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "XS"
                }
            ]
        },
        {
            "combination": [
                {
                    "code": "colour",
                    "value": "Indigo"
                },
                {
                    "code": "size",
                    "value": "L"
                }
            ]
        }
    ]
```


#### customOption Object

|field|format|example|
|---|---|---|
|id|integer|123|
|sortOrder|integer|Items will be sorted sequentially by this integer. Example: 1 would come first, 2 next, etc.|
|title|string|Ring Size|
|isRequired|boolean|TRUE|
|customOptionValues|array of customOptionValue objects|&nbsp:|

#### customOptionValue Object
|field|format|example|
|---|---|---|
|id|string|123|
|title|string|Size 7|
|sortOrder|integer|Items will be sorted sequentially by this integer. Exaple: 1 would come first, 2 next, etc.|




### Method: ProductManager.getCartProduct

This is an Android-only method that is a convenience proxy for the `getProduct` method. IOS developers, use `getProduct`. 

```java
public class Products2 extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ProductInterface productInterface;

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getProductManager();

        // get the parent category of a category
		Category category = new Category();
        String merchantId = "123";
        myProductManager.getParentCategory( merchantId, category, this);
    }

    @Override
    public void onGetCategorySuccess(Category category) {
        String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catParentId = category.getParentId();
        List<Category> children = category.getCategories();
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

Method signature: `session.getParentCategory( merchantId, category, [callback or interface] )`

You must pass a `merchantId`, and a `category` object.

The method returns a category object.





### Method: ProductManager.getProductsAndCategories

```swift
import UIKit
import RezolveSDK

let MERCHANT_ID = "12"
let CATEGORY_ID = Int32(70)

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: SDK_ENV)

        let signUpRequest = createSingUpRequest()

        sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in

            sdk.createSession(entityId: entityId, partnerId: partnerId, device: signUpRequest.device, callback: { (session: RezolveSession) in

                session.productManager.getProductsAndCatgories(
                    merchantId: MERCHANT_ID, 
                    category: Category(id: CATEGORY_ID), 
                    pageNavigationFilter: PageNavigationFilter(
                        count: 10, 
                        pageIndex: 0, 
                        sortBy: nil, 
                        sort: PageNavigationSort.DESC
                    ),
                    callback: { responseCategory in

                        print(responseCategory.id)
                        print(responseCategory.parentId)
                        print(responseCategory.name)
                        print(responseCategory.image)
                        print(responseCategory.imageThumbs)
                        print(responseCategory.hasProducts)
                        print(responseCategory.hasCategories)
                        if responseCategory.hasCategories {                     
                            responseCategory.categories.forEach { subCategories in
                                print(subCategories.id)
                                print(subCategories.parentId)
                                print(subCategories.name)

                                // ...
                            }   
                        }


                        if let resultOfCategory = pageResultOfCategory {

                            resultOfCategory.embedded.forEach { embeddedCategory in
                                print(embeddedCategory.id)
                                print(embeddedCategory.parentId)
                                print(embeddedCategory.name)
                                print(embeddedCategory.image)
                                print(embeddedCategory.imageThumbs)
                                print(embeddedCategory.hasProducts)
                                if embeddedCategory.hasCategories {
                                    // ..
                                }
                            }
                        }

                        if let resultOfProduct = pageResultOfProduct {
                            resultOfProduct.embedded.forEach { embeddedProduct in

                                print(embeddedProduct.id)
                                print(embeddedProduct.merchantId)
                                print(embeddedProduct.title)
                                print(embeddedProduct.subtitle)
                                print(embeddedProduct.price)
                                print(embeddedProduct.description)

                                embeddedProduct.images.forEach {
                                    print($0)
                                }

                                embeddedProduct.options.forEach { option in
                                    print(option.label)
                                    print(option.code)
                                    print(option.extraInfo)
                                    option.values.forEach { optionValue in
                                        print(optionValue.value)
                                        print(optionValue.label)
                                    }
                                }

                                embeddedProduct.optionAvailable.forEach {
                                    $0.combination.forEach { variant in
                                        print(code)
                                        print(value)
                                        print(id)
                                    }
                                } 

                                embeddedProduct.customOptions.forEach {
                                    print($0.isRequire)
                                    print($0.optionId)
                                    print($0.sortOrder)
                                    print($0.title)
                                    print($0.optionType)

                                    $0.values.forEach { value in 
                                        print(value.sortOrder)
                                        print(value.title)
                                        print(valueId)
                                    }

                                    $0.valuesId.forEach { valueId in 
                                        print(valueId)
                                    }

                                    print($0.value)
                                }

                                print(embeddedProduct.productPlacement)
                            }
                        }

                }, errorCallback: {
                    print($0) // handle error
                })
            })
        }
    }
}
```
```java
public class Products2 extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ProductInterface productInterface;

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getProductManager();

		merchantId = "123";
		Category category = new Category();
														
        // first pageNavigationFilter is for categories
		PageNavigationFilter pageNavigationFilter = new PageNavigationFilter();
        pageNavigationFilter.setItemsPerPage(10);
        pageNavigationFilter.setPageNumber(1);
        pageNavigationFilter.setSortBy("name");   // values: name, price
        pageNavigationFilter.setSortDirection("asc");  // values: asc, desc
		// second pageNavigationFilter is for products
		PageNavigationFilter pageNavigationFilter2 = new PageNavigationFilter();
        pageNavigationFilter2.setItemsPerPage(20);
        pageNavigationFilter2.setPageNumber(1);
        pageNavigationFilter2.setSortBy("price");   // values: name, price
        pageNavigationFilter2.setSortDirection("asc");  // values: asc, desc
        
		// get products and categories in one call
        myProductManager.getProductsAndCategories(merchantId, category, pageNavigationFilter, pageNavigationFilter2, this);
    }

    @Override
    public void onGetProductsAndCategoriesSuccess(Category category) {
        String category_id = category.getId();
        String parentId = category.getParentId();
        String name = category.getName();
        Boolean hasCategories = category.hasCategories();
        Boolean hasProduct = category.hasProducts();
        String image = category.getImage();
        List<String> imageThumbs = category.getImageThumbs();
        String catParentId = category.getParentId();
        List<Category> children = category.getCategories();
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

Method signature: `session.getProductsAndCategories( merchantId, category, pageNavigationFilter, pageNavigationFilter2 [callback or interface] )`

The first pageNavigationFilter applies to the categories, the second to the products.

You must pass a `merchantId`, and a `category` object.

The method returns a category object.