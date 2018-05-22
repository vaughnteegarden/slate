

## Mall flow


<img src="images/JWT-mall-flow.png" style="margin:6px 0;"><br/>[ <a href="images/JWT-mall-flow.png" target="_blank">View full size</a> ]

Mall is the only method for finding products that does not require an ad scan. A consumer enters the mall by clicking on a "Mall" navigation option within the mobile app. The Mall showcases active merchants in an attractive layout that is condusive to casual browsing and subsequent purchasing. 

Once a merchant is selected, the consumer shifts into category/product browse mode. Once a product has been selected, purchasing follows one of the previous examples of Instant Buy Flow or Cart Buy Flow (minus the scan step).



#### 1. Get List of Merchants in the mall

```swift
import RezolveSDK
import Foundation

internal final class ApiClient {

    let rezolveSdk: RezolveSDK?

    func getMerchants() {

        self.rezolveSdk?.createSession(
            accessToken: accessToken,
            entityId: entityId,
            partnerId: partnerId, 
            callback: { rezolveSession in

                rezolveSession.merchantManager.getMerchants(
                    callback: { listOfMerchant in

                        listOfMerchant.forEach {
                            print($0.id)
                            print($0.name)
                            print($0.tagline)
                            print($0.banner)

                            $0.bannerThumbs.forEach { thumb in
                                print(thumb)
                            }

                            print($0.logo)
                            $0.logoThumbs.forEach { logo in
                                print(logo)
                            }

                            print($0.termsAndConditions)
                            print($0.contactInformation)
                        }
                    },
                    errorCallback: { _ in 
                        // Handle Errors
                    }
                )
        })
    }
}
```
```java
public class Merchants extends AppCompatActivity implements MerchantInterface {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

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

First, initialize `MerchantManager`, and call `GetMerchants`, providing an implementation of MerchantCallback as a parameter. This will return an array of Merchant objects. Parse each merchant object to get the `id`, `name`, `tagline`, `banner`, `bannerThumbs`, and `logoThumbs`.





### 2. Get list of first-level Categories for the selected Merchant
``` swift
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

				// CATEGORY_ID should be null
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
private final static String API_KEY = "your_api_key";
private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

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

```

Display your subcategories and products as returned by the `getCategories` call. 

For subsequent navigation in categories, use `getProductsAndCategories`.


### 3. If the consumer clicks a subcategory, call `getProductsAndCategories`. 
``` swift
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

As the consumer navigates the category tree, call `getProductsAndCategories` to pull a paginated lists of subcategories and products for that category.


### 4. If the consumer clicks a Product, call `getProduct` 
``` swift
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

If the consumer clicks a Product, call `getProduct` to fetch full product information. At this point, the user can either add the product to their cart, click "buy now", or press "back" to navigate to the category view. 