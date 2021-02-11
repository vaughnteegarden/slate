## Detect SSP Engagements - Android

The Self-Serve Portal, or SSP, enables merchants to create Engagements. Engagements provide a way for the merchant to interact with consumers wherever they are - through print, audio/video, geolocation, and more. 

Engagements are composed of a Trigger (specially customized media) linked to a Target (the content the merchant wants the consumer to see). Triggers include watermarked images, watermarked audio, geozones, QR Codes, and touch links.  Targets include Information Pages, Acts, Products, Categories, and Urls. 

This section describes how to capture information from the Trigger when it is detected, and resolve that into a Target.

### 1. Prepare gradle

Implement the SSP module by adding it to your gradle dependencies.


```kotlin
implementation "com.rezolve.sdk:ssp-android:3.1.0"
```

### 2. Initialize SspActManager

```kotlin
var authParams: AuthParams = AuthParams(
        AUTH0_CLIENT_ID,
        AUTH0_CLIENT_SECRET,
        AUTH0_API_KEY,
        AUTH0_AUDIENCE,
        AUTH0_ENDPOINT,
        SSP_ENGAGEMENT_ENDPOINT,
        SSP_ACT_ENDPOINT
)

var httpConfig: HttpClientConfig = Builder()
        .connectTimeout(30, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .writeTimeout(30, TimeUnit.SECONDS)
        .build()

var httpClientFactory: HttpClientFactory = Builder()
        .setHttpClientConfig(httpConfig)
        .setAuthParams(authParams)
        .build()

var sspHttpClient: SspHttpClient = httpClientFactory.createHttpClient(SSP_ENDPOINT)

var sspActManager = SspActManager(sspHttpClient)
```

### 3. Set up the resolver configuration

The resolver is what decodes the information in the Trigger and looks up the Target

```kotlin
ResolverConfiguration.Builder(rezolveSDK)
	.enableBarcode1dResolver(true)
	.enableCoreResolver(true)
	.enableSspResolver(sspActManager, desiredImageWidth)
	.build(this);
```

### 4. Create a scan view.

Note: an example of Scan View can be seen in the Rezolve Sample App code, ScanActivity.java

```java
public class ScanActivity extends AppCompatActivity {

    private static final String TAG = ScanActivity.class.getSimpleName();
    private VideoScanManager videoScanManager = VideoScanManagerProvider.getVideoScanManager();
    private AudioScanManager audioScanManager = AudioScanManagerProvider.getAudioScanManager();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
		// ...
    }

    @Override
    protected void onDestroy() {
		// ...
    }

    @Override
    protected void onResume() {
		// ...
    }

    @Override
    protected void onPause() {
		// ...
    }
	// ...                                          
}
```

### 5. Register listeners for scan results:

When the phone detects trigger media, the listener will pick up the result and deduce what the target is. You must provide handling for all target types. Handling targets is covered in the next section.

```kotlin
ResolverResultListenersRegistry.getInstance().add(resolveResultListener)

private val resolveResultListener: ResolveResultListener = object : ResolveResultListener {
	override fun onProcessingStarted(uuid: UUID) {
		// ...
	}
	override fun onProcessingFinished(uuid: UUID) {
		// ...
	}
	override fun onProcessingUrlTriggerStarted(uuid: UUID, urlTrigger: UrlTrigger) {
			solveRezolveTrigger(urlTrigger.url)
	}
	override fun onContentResult(uuid: UUID, result: ContentResult) {
		if (result is ProductResult) {
			onProductResult(result.product, result.categoryId)  	// display product (old method)
		} else if (result is CategoryResult) { 
			onCategoryResult(result.category, result.merchantId) 	// display category (old method)
		} else if (result is SspActResult) {
			onSspActResult(result) 									// display SSP Act or info page - check below
		} else if (result is SspProductResultSspProductResult) {
			solveRezolveTrigger(result.rezolveTrigger)              // display product (new SSP method)  
		} else if (result is SspCategoryResult) {
			solveRezolveTrigger(result.rezolveTrigger)              // display category (new SSP method)  
		}
	}
	override fun onResolverError(uuid: UUID, error: ResolverError) {
		onScanError(error.rezolveError.errorType, error.message)
	}
}

private fun solveRezolveTrigger(url: String) {
	triggerManager.resolveTrigger(url, object : TriggerCallback() {
		override fun onCategoryResult(category: Category, merchantId: String) {
			onCategoryResult(category, merchantId) 						// display category (old method)
		}
		override fun onBadTrigger() {
			// ...
		}
		override fun onProductResult(product: Product, categoryId: String) {
			onProductResult(product, categoryId) 						// display product (old method)
		}
		override fun onScanError(rezolveError: RezolveError, scannedData: ScannedData?) {
			onScanError(rezolveError.errorType, rezolveError.message)
		}
	})
}
```














