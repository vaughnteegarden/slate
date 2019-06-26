## Merchant and Product Search

Search is available to aid in the presentation and usability of large malls and/or large product catalogs. You can search for merchants, or for products.

### Merchant Search

```swift

```
```java
// set up parameters for merchant search
String query = "";
MerchantSearchOrderBy merchantSearchOrderBy = MerchantSearchOrderBy.LOCATION;
SearchDirection searchDirection = SearchDirection.ASC;
Integer offset = 0;
Integer limit = 50;
RezolveLocation rezolveLocation = new RezolveLocation();
rezolveLocation.setLatitude(40.6726499);
rezolveLocation.setLongitude(-73.9502622);
MerchantSearchData merchantSearchData = new MerchantSearchData(query,merchantSearchOrderBy,searchDirection,offset,limit,rezolveLocation);

// set up merchant search interface and handle results
MerchantSearchInterface merchantSearchInterface = new MerchantSearchInterface() {
    @Override
    public void onSearchMerchantsSuccess(MerchantSearchResult merchantSearchResult) {
        int page = merchantSearchResult.getPage();
        int total = merchantSearchResult.getTotal();
        List<Merchant> merchants = merchantSearchResult.getMerchants();

        //iterate over merchants array
        for (Merchant merchant : merchants) {
            String banner = merchant.getBanner();
            List<String> bannerThumbs = merchant.getBannerThumbs();
            Double distance = merchant.getDistance();
            String id = merchant.getId();
            String infoEmail = merchant.getInfoEmail();
            String infoName = merchant.getInfoName();
            String infoPhone = merchant.getInfoPhone();
            String logo = merchant.getLogo();
            List<String> logoThumgs = merchant.getLogoThumbs();
            String name = merchant.getName();
            String partnerId = merchant.getPartnerId();
            String partnerName = merchant.getPartnerName();
            String priority = merchant.getPriority();
            List<DisplayStore> stores = merchant.getStores();
            String tagline = merchant.getTagline();
            List<TermsAndConditions> termsAndConditionsList = merchant.getTermsAndConditionsList();

            // get list of store locations associated with this merchant
            for(DisplayStore displayStore:stores){
                 int storeId = displayStore.getId();
                 RezolveLocation storeLocation = displayStore.getLocation();
                 String storeName = displayStore.getName();
            }

            // get terms of conditions for merchant
            for(TermsAndConditions termsAndConditions : termsAndConditionsList){
                String termsCheckboxText = termsAndConditions.getCheckboxText();
                String termsContent = termsAndConditions.getContent();
                String termsId = termsAndConditions.getId();
                String termsName = termsAndConditions.getName();
                String termsStoreId = termsAndConditions.getStoreId();
            }
        }
    }

    @Override
    public void onError(@NonNull RezolveError rezolveError) {
        // handle error gracefully
    }
};

// initiate merchant search
merchantManager.searchMerchants(this,merchantSearchData,merchantSearchInterface);
```

Merchant search offers the following features:

* The ability to list all merchants, sorted by distance to the end user.
* The ability to enter a merchant name query, and sort results by Score or Distance.
* Ability to order results ascending or descending.

Note that to return merchants sorted by distance, each merchant in your ecosystem must configure at least one "Store" in the Merchant Portal, supplying latitude and longitude. Which ever merchant has a store closest to the user will be returned first.  


To search for a merchant you must supply the following parameters:

* Query string - Optional. Leave blank to get all merchants. If more than one term is supplied, terms are matched on an AND basis. 
* Offset - For pagination, offset is the number of pages of results to skip. Setting this to zero gets the first page of results.
* Limit - For pagination, the number of results to return per page.
* Sort By - Should merchants be sorted by LOCATION or by search SCORE.
* Sort Direction - ASC or DESC
* Location - Optional. The latitude and longitude of the consumer phone. Merchant list cannot be sorted by distance without this parameter.

See code samples to the right.

### Product Search

```swift

```
```java
// set up parameters for product search
String query = "book";
String merchantId2 = "123";
ProductSearchOrderBy productSearchOrderBy = ProductSearchOrderBy.SCORE;
SearchDirection searchDirection = SearchDirection.DESC;
ProductType productType = ProductType.ALL;
Integer offset = 0;
Integer limit = 50;
RezolveLocation rezolveLocation = new RezolveLocation();
rezolveLocation.setLatitude(40.6726499);
rezolveLocation.setLongitude(-73.9502622);
ProductSearchData productSearchData = new ProductSearchData(query,merchantId2,productSearchOrderBy,searchDirection,productType,offset,limit,rezolveLocation);

// set up product search interface and handle results
ProductSearchInterface productSearchInterface = new ProductSearchInterface() {
    @Override
    public void onSearchProductsSuccess(ProductSearchResult productSearchResult) {
        Integer page = productSearchResult.getPage();
        Integer total = productSearchResult.getTotal();
        List<DisplayProduct> displayProducts = productSearchResult.getDisplayProducts();

        // iterate over list of display proucts
        for(DisplayProduct displayProduct:displayProducts){
            String productId = displayProduct.getId();
            String merchantId = displayProduct.getMerchantId();
            String categoryId = displayProduct.getCategoryId();
            String categoryName = displayProduct.getCategoryName();
            String image = displayProduct.getImage();
            List<String> imageThumbs = displayProduct.getImageThumbs();
            float price = displayProduct.getPrice();
            Placement productPlacement = displayProduct.getProductPlacement();
            // get product placement details
            String adId = productPlacement.getAdId();
            String placementId = productPlacement.getPlacementId();
        }
    }

    @Override
    public void onError(@NonNull RezolveError rezolveError) {
        // handle error gracefully
    }
};

// initiate product search
productManager.searchProducts(this,productSearchData, productSearchInterface);
```

Product search offers the following features:

* Search products across all merchants in your ecosystem 
* Search the products of a single merchant (by specifying a merchant id)
* Sort results by search SCORE or PRICE
* Sort direction ASC or DESC

To search for products you must supply the following parameters:

* Query string - Optional. Leaving blank will return all products. If more than one term is supplied, terms are matched on an AND basis. 
* Merchant Id - Optional. If supplied, restricts product search to the specified merchant.
* Offset - For pagination, offset is the number of pages to skip. Setting this to zero gets the first page of results.
* Limit - For pagination, the number of results to return per page.
* Sort By - Sort by search SCORE or PRICE
* Sort Direction - ASC or DESC
* Product Type - Can be PRODUCTS, ACTS, or ALL.
* Location - Optional. The latitude and longitude of the consumer phone.

See code samples to the right.