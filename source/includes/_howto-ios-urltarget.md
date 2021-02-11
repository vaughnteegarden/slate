## Handling SSP URL Targets - IOS

```swift
let act = payload.act?.act      // Information for linked Act
let product = payload.product   // Information for linked Product
let category = payload.category // Information for linked Category

// Any of the above are not `null`, and a `customUrl` is detected, we need to redirect the user to this linked entity
let actCustomUrl = act?.customUrl
let productCustomUrl = product?.customUrl
let categoryCustomUrl = category?.customUrl
```

If you find a non-null `payload`, next check for a non-null `customUrl`

If a custom URL is found, you should simply redirect the user to the linked entity. 