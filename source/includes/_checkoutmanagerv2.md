## Module: CheckoutManagerV2

**Topics**

* <a href="#method-checkoutbundle-createcartcheckoutbundle">CheckoutBundle.CreateCartCheckoutBundle</a>


CheckoutManagerV2 is an aggregate of Session.

CheckoutManagerV2 is a complete refactor of CheckoutManager, designed to simplify usage and reduce the number of methods require to accomplish checkout. It also lays the groundwork for Alternate Checkout, the ability to use non-credit-card checkout methods. The checkout module handles the shopping cart, and creates, modifies, and completes orders with payment.

This section also covers the use of CheckoutBundleV2 class, to create the CheckoutBundles used by CheckoutManagerV2. There are separate methods for single product "instant buy" purchase, and cart purchase.



### Method: CheckoutBundleV2.CreateCartCheckoutBundleV2

```swift

```
```java

```

CreateCartCheckoutBundleV2 creates a cart checkout bundle.

Method signature: `session.CheckoutBundleV2.createCartCheckoutBundleV2 ( merchantId,  optionId, cartId, phonebookId, addressId, supportedPaymentMethod, deliveryMethod )`

You must pass in the `merchantId`, `optionId`, `cartId`, and `phonebookId` all as strings, a SupportedPaymentMethod object, and a DeliveryMethod object.

Get SupportedPaymentMethod and DeliveryMethod using PaymentOptionsManager.

The method returns a `CartCheckoutBundle` object.






### Method: CheckoutBundleV2.CreateProductCheckoutBundleV2

```swift

```
```java

```

CreateCartCheckoutBundleV2 creates a product checkout bundle.

Method signature: `session.CheckoutBundleV2.createProductCheckoutBundleV2 ( merchantId,  optionId, checkoutProduct, phonebookId, addressId, supportedPaymentMethod, deliveryMethod )`

You must pass in the `merchantId`, `optionId`, and `phonebookId` all as strings, a SupportedPaymentMethod object, and a DeliveryMethod object.

Get SupportedPaymentMethod and DeliveryMethod using PaymentOptionsManager.

The method returns a `ProductCheckoutBundle` object.





### Method: CheckoutBundleV2.CreatePaymentRequest

```swift

```
```java

```

CreatePaymentRequest creates a payment request, tying together a payment card and CVV that will be used for current transaction.

Method signature: `session.CheckoutManagerV2.createPaymentRequest ( paymentCard, cvv )`

You must pass in a payment card object, and the `cvv` as a string.

The method returns a `PaymentRequest` object.




### Method: CheckoutManagerV2.CheckoutCartOption

```swift

```
```java

```

CheckoutCartOption queries the server to obtain an order id, total, and price breakdown for a particular Cart.

Method signature: `session.CheckoutManagerV2.checkoutCartOption ( cartCheckoutBundle, [callback or interface] )`

You must pass in a CartCheckoutBundle, and a callback or interface.

The method returns an `Order` object.




### Method: CheckoutManagerV2.CheckoutProductOption

```swift

```
```java

```

CheckoutProductOption queries the server to obtain an order id, total, and price breakdown for a particular Product.

Method signature: `session.CheckoutManagerV2.checkoutProductOption ( productCheckoutBundle, [callback or interface] )`

You must pass in a ProductCheckoutBundle, and a callback or interface.

The method returns an `Order` object.



### Method: CheckoutManagerV2.BuyCart

```swift

```
```java

```

BuyCart takes the prepared CheckoutBundle, Order Id, and Payment information, and submits it to make a purchase. 

Method signature: `session.CheckoutManagerV2.buyCart ( paymentRequest, cartCheckoutBundle, orderId, [callback or interface] )`

You must pass in a PaymentRequest object, a CartCheckoutBundle object, an order id, and a callback or interface.

The method returns an `OrderSummary` object.





### Method: CheckoutManagerV2.BuyProduct

```swift

```
```java

```

BuyCart takes the prepared CheckoutBundle, Order Id, and Payment information, and submits it to make a purchase. 

Method signature: `session.CheckoutManagerV2.buyProduct ( paymentRequest, productCheckoutBundle, orderId, [callback or interface] )`

You must pass in a PaymentRequest object, a ProductCheckoutBundle object, an order id, and a callback or interface.

The method returns an `OrderSummary` object.