## Handling SSP Product Targets - Android

```kotlin
((SspProductResult)result).getSspProduct().getRezolveTrigger());
```
When a trigger is resolved (see Step 5 of <a href="#detect-ssp-engagements-android">How To Detect SSP Engagements - Android</a>), one of the possible result `ContentResult` types is `SspProductResult`. To retrieve the product, extract a `RezolveTrigger` using the sample code shown. You can then use TriggerManager to retrieve the product. See <a href="#trigger-manager">TriggerManager</a>. 

## Handling SSP Category Targets - Android

```kotlin
((SspCategoryResult)result).getSspCategory().getRezolveTrigger());
```
When a trigger is resolved (see Step 5 of <a href="#detect-ssp-engagements-android">How To Detect SSP Engagements - Android</a>), one of the possible result `ContentResult` types is `SspCategoryResult`. To retrieve the category, extract a `RezolveTrigger` using the sample code shown. You can then use TriggerManager to retrieve the category. See <a href="#trigger-manager">TriggerManager</a>. 

## Handling SSP URL Targets - Android

```kotlin
if (TriggerManager.isRezolveTrigger(url)) {
   // handle url trigger
} else {
   // handle custom url
}
```

The last type of target you need to handle is a custom URL. Custom URLs can be associated with Products or Categories. To detect a custom URL, add a simple if/else clause that checks if the URL you receive is a RezolveTrigger.  If not, then it is a custom URL, and you should handle it as you see fit. The typical behavior is to redirect the user to the URL, but the use case is up to you. 