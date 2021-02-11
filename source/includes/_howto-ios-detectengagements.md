## Detect SSP Engagements - IOS

The Self-Serve Portal, or SSP, enables merchants to create Engagements. Engagements provide a way for the merchant to interact with consumers wherever they are - through print, audio/video, geolocation, and more. 

Engagements are composed of a Trigger (specially customized media) linked to a Target (the content the merchant wants the consumer to see). Triggers include watermarked images, watermarked audio, geozones, QR Codes, and touch links.  Targets include Information Pages, Acts, Products, Categories, and Urls. 

This section describes how to capture information from the Trigger when it is detected, and resolve that into a Target.

### 1. Initialize ScanManager

```swift
guard let scanManager = rezolveSession?.getScanManager() else {
    return
}
```

### 2. Set up a ProductDelegate

```swift
scanManager.productResultDelegate = self
try? scanManager.startVideoScan(scanCameraView: self.view as! ScanCameraView, rectOfInterest: .frame)

extension ViewController: ProductDelegate {

    func onStartRecognizeImage() {
        // Suggestion: Show an interstitial loader
    }

    func onFinishRecognizeImage() {
        // Suggestion: Hide an interstitial loader
    }

    func onCategoryResult(merchantId: String, category: RezolveCategory) {
        // See "Mall" section "3. If the consumer clicks a subcategory, call `getProductsAndCategories`"
    }

    func onCategoryProductsResult(merchantId: String, category: RezolveCategory, productsPage: PageResult<DisplayProduct>) {
        // See "Mall" section "3. If the consumer clicks a subcategory, call `getProductsAndCategories`"
    }

    func onProductResult(product: Product) {
        // See "Mall" section "4. If the consumer clicks a Product, call `getProduct`"
    }

    func onError(error: String) {
        // Handle error gracefully
    }
}
```

### 3. Add a function to handle SSP Engagements

```swift
scanManager.productResultDelegate = self
try? scanManager.startVideoScan(scanCameraView: self.view as! ScanCameraView, rectOfInterest: .frame)

extension ViewController: ProductDelegate {

    // Functions from previous example hidden for brevity
    
    func onSspEngagementResult(engagement: ResolverEngagement, eventType: RezolveEventReport.RezolveEventReportType) {
        // Object `engagement` contains the basic structure of a SSP resolved engagement
        let id = engagement.engagementId
        let scanId = engagement.serviceId
        let path = engagement.payloadPath
        let payoffs = engagement.payoffs
        let payload = engagement.rezolveCustomPayload
    }
}
```

### 4. Inspect the payload

```swift
// Any of these can be `null`, but one should have content
let act = payload.act?.act      // Information for linked Act
let product = payload.product   // Information for linked Product
let category = payload.category // Information for linked Category
```
The non-null object item corresponds to the target set by the merchant. You must handle all target types. This will be covered in the following section. 