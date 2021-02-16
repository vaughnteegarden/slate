## Handling SSP Product Targets - IOS 

```swift
func onSspEngagementResult(engagement: ResolverEngagement, eventType: RezolveEventReport.RezolveEventReportType) {
    let id = engagement.engagementId
    let scanId = engagement.serviceId
    let path = engagement.payloadPath
    let payoffs = engagement.payoffs
    let payload = engagement.rezolveCustomPayload

    // The logic of parsing, is that you might find a `customUrl` in the structure not being `null`.
    // Hence, we will need to examine it and redirect the user to the linked Web Page, Product or Category
    if let act = payload.act?.act {
        if let customUrl = act.customUrl {
            // Parse act.customUrl
            return
        }
    } else if let product = payload.product?.customUrl {
        if let customUrl = product.customUrl {
			// To Parse product.customUrl 
			// Check if url begins with "https://rzlv.co"
			// Check if url path is in format "/[int]/[int]/[int]/[int]"
			// If both the above are true, you have a valid RezolveTrigger. Use Trigger Manager to look up the product. 
			// If the url does not meet the above rules, it is not a RezolveTrigger, it is a regular url. Redirect the user to the url. 
            return
        }
    } else if let category = payload.category?.customUrl {
        if let customUrl = category.customUrl {
            // Parse category.customUrl
            return
        }
    }
}
```

To handle a product result, get the product custom URL and test it. 

If the url is a RezolveTrigger, similar in format to `https://rzlv.co/1/2/3/4`, use <a href="#trigger-manager">Trigger Manager</a> to fetch the product. See code sample for test advice.
    
If the url is NOT a RezolveTrigger, assume it is a regular url, and you should handle it as you see fit. The typical behavior is a simple redirect to the url, to be opened in a browser. 



## Handling SSP Category Targets - IOS

```swift
func onSspEngagementResult(engagement: ResolverEngagement, eventType: RezolveEventReport.RezolveEventReportType) {
    let id = engagement.engagementId
    let scanId = engagement.serviceId
    let path = engagement.payloadPath
    let payoffs = engagement.payoffs
    let payload = engagement.rezolveCustomPayload

    // The logic of parsing, is that you might find a `customUrl` in the structure not being `null`.
    // Hence, we will need to examine it and redirect the user to the linked Web Page, Product or Category
    if let act = payload.act?.act {
        if let customUrl = act.customUrl {
            // Parse act.customUrl
            return
        }
    } else if let product = payload.product?.customUrl {
        if let customUrl = product.customUrl {
			// Parse product.customUrl 
            return
        }
    } else if let category = payload.category?.customUrl {
        if let customUrl = category.customUrl {
            // To Parse category.customUrl 
			// Check if url begins with "https://rzlv.co"
			// Check if url path is in format "/[int]/[int]/[int]"  (note: one fewer parameter than a product)
			// If both the above are true, you have a valid RezolveTrigger. Use Trigger Manager to look up the category. 
			// If the url does not meet the above rules, it is not a RezolveTrigger, it is a regular url. Redirect the user to the url. 
            return
        }
    }
}
```

To handle a category result, get the category custom URL and test it. 

If the url is a RezolveTrigger, similar in format to `https://rzlv.co/1/2/3/4`, use <a href="#trigger-manager">Trigger Manager</a> to fetch the category. See code sample for test advice.
    
If the url is NOT a RezolveTrigger, assume it is a regular url, and you should handle it as you see fit. The typical behavior is a simple redirect to the url, to be opened in a browser. 