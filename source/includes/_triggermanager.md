## Module: TriggerManager

**Topics**

* <a href="#method-triggermanager-resolvetrigger">TriggerManager.rezolveTrigger</a>

The TriggerManager is an aggregate of Session.

Triggers are special items that the user can act upon. Image Engagements and Audio Engagements are a type of trigger, but these are handled by `ScanManager`. Touch Engagements are handled by `TriggerManager`. Touch Engagements are URLs that are typically rendered as a touchable link or button onscreen. 

This module provides a way to resolve touch triggers into actionable content, like products or categories. 

Touch Engagements, or touch triggers, always have the same format: 
`http://rzlv.co/[partnerId]/[merchantId]/[categoryId]/[productId]?ad=[adId]&placement=[placementId]`

To use touch triggers, the partner should watch for urls in their content stream that match this pattern, render them as a touchable link, and then when touched, pass the url to the `TriggerManager.resolveTrigger method`. 

### Method: triggerManager.resolveTrigger

``` swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {

    @IBOutlet var scanCameraView: ScanCameraView?

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let url = URL(string: "http://rzlv.co/1/2/3/8")

        self.mySession.triggerManager.resolve(
            url: url!,
            productDelegate: self,
            onRezolveTriggerStart: {},
            onRezolveTriggerEnd: {},
            errorCallback: { error in }
        )
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()

        scanManager?.stop()
    }
}

extension ViewController: ProductDelegate {


    func onStartRecognizeImage() {
		// will be renamed in the future to onStartRecognizeTrigger or similar
		// this fires when the trigger URL is sent for resolution by the Rezolve API. 
		// could be used to start a waiting animation.
    }

    func onFinishRecognizeImage() {
		// will be renamed in the future to onFinishRecognizeTrigger or similar
		// this fires when a product, category, or error result is returned by the Rezolve API. 
		// could be used to stop a waiting animation.
    }

    func onProductResult(product: Product) {
		// handle product result
    }

    func onCategoryResult(merchantId: String, category: RezolveCategory) {
		// handle category result
    }

    func onCategoryProductsResult(
      merchantId: String,
      category: RezolveCategory, productsPage: PageResult<DisplayProduct>) {
	  // will be ignored by trigger manager, touch triggers don't return this result type
    }

    func onError(error: String) {
		// handle error
    }
```
``` java
public class TriggerMgr extends AppCompatActivity implements TriggerInterface {


    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        TriggerManager triggerManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession().getTriggerManager();

        String triggerUrl = "http://rzlv.co/2/3/13/169?ad=20&placement=25";

        triggerManager.resolveTrigger( triggerUrl, this );

        // reserved for future functionality:
        // triggerManager.getPersistedTriggers();
        // triggerManager.listenUrl();
    }


    @Override
    public void onProductResult(Product product) {
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

    @Override
    public void onBadTrigger() {
        // called when provided url has bad syntax or is too short
        // handle error
    }

    @Override
    public void onError(HttpResponse httpResponse) {
        // called if httpError occurs
        // handle error... available methods are:
        // httpResponse.getResponseJson();
        // httpResponse.getErrorList();
        // httpResponse.getStatusCode();
        // httpResponse.getResponseJson();
    }
}
```
Method signature: `session.triggerManager.rezolveTrigger( triggerUrl, [callback or interface] )`

You must pass in a valid `triggerUrl` string.

The method returns a `product` or `category` object upon success. For details of the `product` or `category` object structure, please see <a href="#module-productmanager">Module: ProductManager</a>.