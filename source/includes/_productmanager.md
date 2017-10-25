## Module: ProductManager

Shop is an aggregate of Session.

The Shop module offers methods specific to e-commerce; getting catalogs, products, and creating orders.

### Method: getMerchants

```swift
import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getMerchants() { (listOfMerchant: Array<Merchant>) in

          listOfMerchant.forEach() { (merchant: Merchant) in

              let merchantId: String = merchant.id
              let title: String = merchant.title
          }
      }
  }
}

```
```java
// get merchants using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get merchants
        myProductManager.getMerchants(this);
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
}
```

Method signature: `session.getMerchants( [callback or interface] )`

The method returns an array of `merchant` objects.

#### merchant Object

|field|format|example|
|---|---|---|
|id|string|brookstone|
|title|string|Brookstone|
|logo_url|string|http://domain.com/path/logo.jpg|
|image_url|string|http://domain.com/path/image.jpg|

How to use the returned images: the `image_url` is typically used as a header background, and the image represented by `logo_url` is overlayed on top.



### Method: getCatgories

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getCatalogs(merchantId: "123", 
       catalogId: nil) { (listOfCatalog: Array<Catalog>) in

          listOfCatalog.forEach() { (catalog: Catalog) in

              let catalogId: String = catalog.id
          }
      }
  }
}

```
```java
// get root category and first level child categories using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

		// get categories
        String merchantId = "123";
        myProductManager.getCategories(merchantId,this);
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
    }
}
```

Method signature: `session.getCategories( merchant_id, [callback or interface] )`

You must pass in the `id` of the `merchant` to whose categories you wish to get.

The method returns an array of `category` objects owned by a specific merchant.

This method returns the *root category* and *first level of child categories*.

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

Notes:

If `has_categories` is `false`, the `categories` array should be empty.

If `has_products` is `true`, call `getProducts` with the category `id` to get a product list.

This method takes a category id, and returns *that category*, and *all its child categories*.

### Method: getCategory

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getCatalogs(merchantId: "123", 
       catalogId: "A") { (listOfCatalog: Array<Catalog>) in

          listOfCatalog.forEach() { (catalog: Catalog) in

              let catalogId: String = catalog.id
          }
      }
  }
}

```
```java
// get a single category using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
         RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get single category
        String categoryId = "123";
        String merchantId = "123";
        myProductManager.getCategory(merchantId, categoryId, this);
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
    }
}
```

Method signature: `session.getCategory( merchant_id, categgory_id, [callback or interface] )`

The method returns a `category` object.

<aside class="notice">
TODO
Note: IOS uses the `getCatalogs` method to fetch both singuler and multiple catalogs. To get all catalogs, simply set the `catalogId` to `nil`. To get a singular catalog, specify a `catalogId`.
</aside>

### Method: getProducts

```swift
TODO

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      let pageNavigation = PageNavigation(count: 10,
                                          pageIndex: 0,
                                          sortBy: nil,
                                          sort: PageNavigationSort.ASC)

      self.mySession?.productManager.getProducts(
          merchantId: "123",
          catalogId: "A",
          pageNavigation: pageNavigation) { (productPageResult: PageResult<DisplayProduct>) in

              productPageResult.embedded.forEach() { (displayProduct: DisplayProduct) in

                  let productId = displayProduct.id
              }
          }
      }
  }
}

```
```java
// get products using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get products
        Integer count = 16;
        Integer page = 1;
        String sort_by_field = "title";
        String sort_direction = "ASC";
        String merchantId3 = "123";
        String categoryId = "123";
        myProductManager.getProducts(merchantId3, categoryId, count, page, 
        sort_by_field, sort_direction, this);
    }

    @Override
    public void onGetProductsSuccess(PageResult<DisplayProduct> pageResult) {
        Integer count = pageResult.getCount();
        Integer total = pageResult.getTotal();
        Link[] links = pageResult.getLinks();
        List<DisplayProduct> products = pageResult.getProducts();

        for (Link link: links){
            Integer linkcount = link.getCount();
            Integer page = link.getPage();
            String sort = link.getSort();
            String sortBy = link.getSortBy();
        }

        for (DisplayProduct displayProduct : products){
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

Method signature: `session.getProducts( merchant_id, category_id, pageNavigation, [callback or interface] )`

You must pass a `merchant_id`, `category_id`, and a `pageNavivation` object.

The method returns a `pageResult` object.


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
|sort|enum|one of: ASC, DESC|


#### displayProduct Object

The displayProduct object is used when rending a series of products. It contains a subset of the product object, suitable for displaying so the user can choose a product to view.

|field|format|example|
|---|---|---|
|id|auto-populated string|123|
|categoryId|string|123|
|name|string|Fitbit Charge 2|
|price|decimal|129.00|
|imageThumbs|array of thumbnail image urls (as strings)|http://domain.com/path/image.jpg|
|image|main product imaage url (as a string)|http://domain.com/path/image.jpg|


### Method: getProduct

```swift
TODO 

import UIKit
import RezolveSDK

class ProductViewController: UIViewController {

  let API_KEY: String = "your_api_key"

  var mySession: RezolveSession?

  override func viewDidLoad() {
      super.viewDidLoad()

      self.mySession = ... // initialize session

      self.mySession?.productManager.getProduct(merchantId: "123", catalogId: "A", 
       productId: "ABC") { (product: Product) in
          let productId: String = product.id
          let title: String = product.title
      }
  }
}
```
```java
// get a single product using ProductInterface
public class Products extends AppCompatActivity implements ProductInterface {

    private final static String API_KEY = "your_api_key";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ProductManager myProductManager = RezolveSDK.getInstance(API_KEY, 
        RezolveSDK.Env.SANDBOX).getRezolveSession().getProductManager();

        // get single product
        Product product = new Product();
        String productId = "123abc";
        String merchantId = "123";
        String categoryId = "123";
        myProductManager.getProduct(merchantId, categoryId3, productId, this);
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
        List<Variant> optionsAvail = product.getOptionAvailable();
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
|sortOrder|integer|Items will be sorted sequentially by this integer. Exaple: 1 would come first, 2 next, etc.|
|title|string|Ring Size|
|isRequired|boolean|TRUE|
|customOptionValues|array of customOptionValue objects|&nbsp:|

#### customOptionValue Object
|field|format|example|
|---|---|---|
|id|string|123|
|title|string|Size 7|
|sortOrder|integer|Items will be sorted sequentially by this integer. Exaple: 1 would come first, 2 next, etc.|








