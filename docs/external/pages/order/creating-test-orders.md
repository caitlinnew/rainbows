---
tags: [order, testing]
---

# Creating Test Orders

When using the public sandbox, a subsection of the API will be available to create test orders.

This endpoint `/api/testing/orders/create` can be called to create a new test order with a new retailer, and a couple of new products.

You can also pass in options to specify the exact retailer or products themselves. To find out more, you can view the [endpoint specification here](a45de155779ff-create-test-order-sandbox-only).

## Finding the Retailer Id

There is an option to specify the `retailerId` to use the same retailer for every test order, to find this, login as that retailer you created and open up the developer tools. You will see it logged to the console.
