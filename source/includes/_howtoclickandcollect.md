## Click and Collect

Click and Collect enables users to select products online, and pick them up in-store. The consumer may either pay online, or pay in-store.

Please note that your Resolve Commerce Engine must have Pick Up In Store enabled under the Advanced menu, for this option to apply. 

The Click and Collect flow is no different from in the Cart Buy or Instant Buy examples, it is only the user-chosen values that change. 

#### 1. Choices returned by PaymentOptionManager

```swift
let sampleCheckoutProduct = createCheckoutProductWithVariant(product: product)
let sampleMerchantID = "12"

rezolveSession?.paymentOptionManager.getPaymentOptionFor(checkoutProduct: sampleCheckoutProduct, merchantId: sampleMerchantID) { (result: Result<PaymentOption, RezolveError>) in
		switch result {
    case .success(let option):
      	{
          	// For this example we assume the user chooses the first option. In reality, we should display all options and provide the ability to choose.
          	let paymentMethod  = option.supportedPaymentMethods.first!
          	let shippingMethod = option.supportedDeliveryMethods.first!
        }
      	
    case .failure(let error):
      	// Handle error gracefully
    }
})
```
```java
paymentOptionManager.getProductOptions(checkoutProduct, merchantId, new PaymentOptionCallback() {
    @Override
    public void onProductOptionsSuccess(PaymentOption paymentOption) {
        setPaymentOption(paymentOption);
    }
    @Override
    public void onError(@NonNull final RezolveError error) {
    }
});

```

When you call `PaymentOptionManager.getProductOptions`, it returns a list of your payment and shipment options.

#### 2. Inspecting the returned options

```
{
  "type": "standard",
  "supported_shipping_methods": [
    {
      "execute": {
        "method_code": "flatrate",
        "manual_payment_label": "Pay on Collection",
        "extension_attributes": null,
        "display_name": "Fixed",
        "carrier_code": "flatrate"
      },
      "details": {
        "store_details": null
      }
    },
    {
      "execute": {
        "method_code": "storepickup",
        "manual_payment_label": "Pay on Collection",
        "extension_attributes": [
          {
            "value": "2",
            "code": "pickup_store"
          }
        ],
        "display_name": "Pickup In Store",
        "carrier_code": "storepickup"
      },
      "details": {
        "store_details": {
          "telephone": "01234456789",
          "pickup_store": 2,
          "opening_times": [
            
          ],
          "name": "Chao Yang Store",
          "location": {
            "longitude": 116.2573779,
            "latitude": 39.9390628
          },
          "email": "china2@storepickuptesting.com",
          "description": null,
          "address": {
            "street2": "",
            "street1": "14 Dongsuan Huan Beilu",
            "region": "Beijing",
            "post_code": "100026",
            "country": "\u00e4\u00b8\u00ad\u013a\u203a\u02dd",
            "city": "BEIJING"
          }
        }
      }
    },
    {
      "execute": {
        "method_code": "storepickup",
        "manual_payment_label": "Pay on Collection",
        "extension_attributes": [
          {
            "value": "1",
            "code": "pickup_store"
          }
        ],
        "display_name": "Pickup In Store",
        "carrier_code": "storepickup"
      },
      "details": {
        "store_details": {
          "telephone": "01234456789",
          "pickup_store": 1,
          "opening_times": [
            
          ],
          "name": "China Qingdao Shi Store",
          "location": {
            "longitude": 36.3867744,
            "latitude": 117.6466473
          },
          "email": "chinar@storepickuptesting.com",
          "description": null,
          "address": {
            "street2": "",
            "street1": "63 RENMIN LU",
            "region": "Shandong",
            "post_code": "266033",
            "country": "\u00e4\u00b8\u00ad\u013a\u203a\u02dd",
            "city": "QINGDAO SHI"
          }
        }
      }
    }
  ],
  "supported_payment_methods": [
    {
      "type": "union_pay",
      "data": {
        "supported_types": [
          
        ],
        "supported_shipping": [
          "storepickup",
          "flatrate"
        ],
        "supported_networks": [
          
        ],
        "requirements": {
          "rezolve_phone": true
        }
      }
    }
  ],
  "options": {
    
  },
  "id": "aa665b54-7166-4a53-a275-9b4f693770dc"
}
```

To the right is a sample of data returned by `PaymentOptionsManager.getProductOptions`. Look at the `supported_shipping_methods` node at the top of the file. If the list contains at least one child with `execute.method_code: storepickup` it means user can buy the product with Click and Collect. Every store where collecting the purchase is available will be listed as a separate item under `supported_shipping_methods`.

At this point the user should select a payment method. They are listed under the `supported_payment_methods` node. In some cases, like the example shown, there is one payment method available. In others, there might be more. For example, if `SupportedPaymentMethod has type: cash`, cash payment may only be allowed with Click and Collect and not in Delivery. To verify that we need `SupportedPaymentMethod` to be provided as an argument when creating a `DeliveryUnit`.

#### 3. Creating the Delivery Unit/Delivery Method

```swift
// Standard shipping example
let deliveryMethod = CheckoutShippingMethod(type: "flatrate", addressId: address.id)

// Click and Collect example
let deliveryMethod = CheckoutShippingMethod(type: "storepickup", pickupStore: store.pickupStore)
```
```java
// standard shipping example
DeliveryUnit deliveryUnit = new DeliveryUnit(supportedPaymentMethod, address.getId());

// Click and Collect example
DeliveryUnit deliveryUnit = new DeliveryUnit(supportedPaymentMethod, Integer.valueOf(supportedDeliveryMethod.getShippingMethod().getExtensionAttributes().get(0).getValue()));
```

Android calls this object `DeliveryUnit`, IOS uses `DeliveryMethod`

`DeliveryUnit` (Android) is created using the chosen `supportedPaymentMethod` and the address id of the pick up store. 

`DeliveryMethod` (IOS) has no need of an id for the object creation, so an empty `String` used to feel the `addressId` present in the constructor.

See the `extension_attributes` node for this store id. 

`"extension_attributes": [ { "value": "2", "code": "pickup_store" } ]`

Create the `DeliveryUnit`/`DeliveryMethod` as shown at right. 

`DeliveryUnit`/`DeliveryMethod` is then passed to create the `CheckoutBundleV2`, which is used to get totals before purchasing the product. 



