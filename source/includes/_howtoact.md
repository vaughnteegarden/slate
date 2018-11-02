## Act

An Act is a special type of product that can be configured in the Rezolve Commerce Engine. Acts are used to ask questions of the consumer and to collect their contact information. 

There is no specific data structure within the SDK that represents an Act; it is a particular configuration of product that has no weight and no price. If you receive a product with no weight and no price, you should render it as an Act. You do this by omitting certain information when displaying the item. You must do this in four places:

- The Product detail view
- The Post-Purchase screen
- The My Activity list view
- The My Activity detail view

#### Product Detail View

Remove price, quantity, payment details, delivery details, order summary, and the add-to-cart icon. Change the "Instant Buy" text to "Act Now".

<img src="images/product-view.png" style="margin:6px 0;"><br/>[ <a href="images/product-view.png" target="_blank">View full size</a> ]

#### Post-Purchase Screen

Omit quantity, price, payment details, delivery details, order summary, and pick-up-location map.

<img src="images/post-purchase-view.png" style="margin:6px 0;"><br/>[ <a href="images/post-purchase-view.png" target="_blank">View full size</a> ]

#### My Activity List View

Omit the Order Total currency amounts from this screen.

<img src="images/my-activity-list.png" style="margin:6px 0;"><br/>[ <a href="images/my-activity-list.png" target="_blank">View full size</a> ]

#### My Activity Detail View

Change "Payment Successful" header to "Submitted". Remove "order" text from "Your order ref:". Omit quantity, price, payment details, delivery details, order summary, pick-up-location map, and QR code area.

<img src="images/my-activity-detail-view.png" style="margin:6px 0;"><br/>[ <a href="images/my-activity-detail-view.png" target="_blank">View full size</a> ]