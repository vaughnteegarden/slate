## Module: ScanManager


```java
//
// contents of scan_activity.xml
//

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ScanActivity"
    android:padding="10dp">

	//this is the video scan UI component
    <com.rezolve.sdk.views.RezolveScanView
        android:id="@+id/scan_view"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:layout_centerHorizontal="true"
        android:layout_below="@id/start_video_scan">
    </com.rezolve.sdk.views.RezolveScanView>

	// this is the audio scan UI component
    <com.rezolve.sdk.views.RezolveAudioVizualizationView
        android:id="@+id/visualizer"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_below="@+id/scan_view"
        android:layout_marginTop="1dp"
        android:background="#000000"
        android:layerType="hardware" >
    </com.rezolve.sdk.views.RezolveAudioVizualizationView>
</RelativeLayout>

```
ScanManager is different than other Managers as it relies on a user interface component.

### Methods: ScanManager.startVideoScan, ScanManager.stopVideoScan

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
    @IBOutlet var scanCameraView: ScanCameraView?
    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()
        //self.mySession = ... // initialize session
        let scanManager = self.mySession?.getScanManager()
        scanManager?.productResultDelegate = self
        scanManager?.rezolveScanResultDelegate = self

        scanManager?.startVideoScan(scanCameraView: self.scanCameraView!)
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        scanManager?.stop()
    }
}

extension ScanManagerViewController : ProductDelegate, RezolveScanResultDelegate {

    // in response to a scan, the following methods may be called:
    //
    // resultDidFetched   - fires when a scannable asset has been recognized.
    // productDidFetched  - fires if the asset is recognized as a product link
    // errorHappened      - fires if asset is recognized, but cannot be decoded (for
    //                      example, due to network error)
    //

    public func resultDidFetched(result: RezolveScanResult) {
        // handle valid scan result
    }

    public func productDidFetched(product: Product) {
        // handle product
    }

    public func errorHappened(error: String) {
        // handle error result
    }
}
```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface, View.OnClickListener {

    private final static String API_KEY = "your_api_key";
    private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";

    private final static String TAG = ScanActivity.class.getSimpleName();
    private ScanManager scanManager;
    private RezolveScanView rezolveScanView;
    private RezolveAudioVizualizationView rezolveAudioVizualizationView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, ENVIRONMENT)
			.getRezolveSession().getScanManager(this, true);

        // use startVideo to begin watching for scannable media
        scanManager.startVideoScan(this, rezolveScanView);
        // when done, call stopVideoScan
        scanManager.stopVideoScan();
    }

    // in response to a scan, the following methods may be called:
    //
    // processingStarted  - fires when a scannable asset has been recognized.
    // onProductResult    - fires if the asset is recognized as a product link
    // onRezolveResult    - fires if the asset is some other type the SDK can handle,
    //                      like a tollway transponder barcode
    // onScanError        - fires if asset is recognized, but cannot be decoded (for
    //                      example, due to network error)
    // processingFinished - fires when asset recognition is finished
    //

    @Override
    public void processingStarted() {
        Log.d(TAG, "processingStarted");
    }

    // if scan media contains a product link, onProductResult fires and
    // returns a Product object
    @Override
    public void onProductResult(Product product) {
        // get product info
        String product_id = product.getId();
        String title = product.getTitle();
        String subtitle = product.getSubtitle();
        // ... etc
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


    // if scan media is another recognized format, onRezolveResult fires and
    // returns a RezolveScanResult object. Inspect the object to get the
    // payload.
    @Override
    public void onRezolveResult(RezolveScanResult result) {
        Log.d(TAG, "Scan success! "+result.mContent);
        String content = result.mContent;
        String description = result.mDescription;
        Bitmap bitmap = result.mBitmap;
        String payloadId = result.mPayloadId;
        String reportActionToken = result.mReportActionToken;
    }

    @Override
    public void onScanError(String errorTitle, String errorMsg) {
        // display scan error notification
    }

    @Override
    public void processingFinished() {
        Log.d(TAG, "processingFinished");
    }
}
```

Starts/stops watching the camera video stream to look for tagged media, qr codes, and barcodes.

Method signature: `session.scanManager.startVideoScan()`

Method signature: `session.scanManager.stopVideoScan()`

### Methods: ScanManager.startAudioScan, ScanManager.stopAudioScan

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()
        //self.mySession = ... // initialize session
        let scanManager = self.mySession?.getScanManager()
        scanManager?.productResultDelegate = self
        scanManager?.rezolveScanResultDelegate = self

        scanManager?.startAudioScan()
    }
}

extension ScanManagerViewController : ProductDelegate, RezolveScanResultDelegate {
    // in response to a scan, the same methods may be called as with .startVideoScan
}
```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface {
    // same vars as above

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)
                .getRezolveSession().getScanManager(this, true);

        // use startAudioScan to being listening for watermarked media
        scanManager.startAudioScan(this, rezolveAudioVizualizationView);
        // use stopAudioScan to cease listening.
        scanManager.stopAudioScan();
    }
    // the same response methods apply to AudioScan as VideoScan. See above.
}
```

Starts/stops listening to microphone audio stream for tagged media.

Method signature: `session.scanManager.startAudioScan()`

Method signature: `session.scanManager.stopAudioScan()`

### Method: ScanManager.destroy

```swift
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {
	// ...
    override func viewDidLoad() {
    	// ...
        scanManager?.destroy()
    }

}
```
```java
public class ScanActivity extends AppCompatActivity implements ScanManagerInterface {
    // same vars as above

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // set the content view
        setContentView(R.layout.scan_activity);
        // reference our scan_view
        rezolveScanView = (RezolveScanView)findViewById(R.id.scan_view);
        // initialize scan manager
        scanManager = RezolveSDK.getInstance(API_KEY, RezolveSDK.Env.SANDBOX)
                .getRezolveSession().getScanManager(this, true);

        // use stopAudioScan to cease listening.
        scanManager.destroy();
    }
    // destroy has no response methods
}
```

The destroy() method should always be called when done with scanning. The method destroys the initialized scan manager, and frees the resources it was consuming.

Method signature: `session.scanManager.destroy()`






