---
tags: [order]
---

# Interacting with Orders

When a brand receives a new order, it arrives in the `ankor_confirmed` status. The brand can then perform certain transitions using a single endpoint:

`POST /api/v1/orders/{id}/-actions/transition`

This endpoint will accept different payloads depending on what transition is provided. This page will detail each transition that may be performed and when.

All the implementation details and fields can be found in the endpoint documentation.

<!-- theme: danger -->

> #### No going back!
>
> Once an order is accepted or rejected it cannot be reversed. If this was a mistake the brand can contact Ankorstore through the normal channels to revert the order to `ankor_confirmed`.

If an order is rejected you need to provide a detailed reject reason. Otherwise, after the order is accepted the brand then needs to ship it. Please see [shipping an order](/docs/ankorstore-api/pages/order/shipping-an-order.md) for further details.

## Accepting an Order

Can be performed when the order is in `ankor_confirmed`.

In order to accept an order without any item modifications a simple payload detailing the transition will suffice:

```json
{
    "data": {
        "type": "brand-validates"
    }
}
```

The order will move from `ankor_confirmed` to `brand_confirmed`.

## Accepting an Order with modifications

Can be performed when the order is in `ankor_confirmed`.

Sometimes you may wish to modify the order before accepting it if for example you do not have the items in stock or further communication has taken place with the retailer.

The order items can **only be modified at this stage**. The rules of the payload are as follows:

 - To leave an item's quantity as is, do not include it in the payload below
 - To update an item's quantity, include it in the payload below with the new quantity (cannot be above the current quantity)
 - To remove an item entirely, set the item's quantity to 0.

You cannot remove all items, please reject the order in this scenario.

```json
{
    "data": {
        "type": "brand-validates",
        "attributes": {
            "orderItems": [
                {
                    "id": "a470c8d6-1bda-4612-b0bd-3ea2a81a9e89"
                    "type": "order-items"
                    "attributes": {
                        "quantity": 12
                    } 
                },
                {
                    "id": "0ca13de1-8e4b-4e67-a147-185cc5f6f57f"
                    "type": "order-items"
                    "attributes": {
                        "quantity": 0
                    } 
                }
            ]
        }
    }
}
```

The order will move from `ankor_confirmed` to `brand_confirmed`.

## Rejecting an Order

Can be performed when the order is in `ankor_confirmed`.

To reject an order you must provide a reason listed below for the `rejectType` field.

- `BRAND_ALREADY_HAS_CUSTOMER_IN_THE_AREA` - Brand already has a customer in the area
- `BRAND_CANNOT_DELIVER_TO_THE_AREA` - Brand cannot make a delivery to the target area
- `BRAND_HAS_EXCLUSIVE_DISTRIBUTOR_IN_THE_REGION` - The brand already has an exclusive distributor in the target region
- `BUYER_NOT_A_RETAILER` - The buyer does not seem to be a retailer
- `ORDER_ITEMS_PRICES_INCORRECT` - The prices of items in this order were incorrect
- `PAYMENT_ISSUES_WITH_RETAILER` - Brand had payment issues in the past with this retailer
- `PREPARATION_TIME_TOO_HIGH` - The time needed for the order preparation is excessively high
- `PRODUCT_OUT_OF_STOCK` - Order cannot be processed because one or multiple ordered products are not in the stock
- `PURCHASE_NOT_FOR_RESALE` - This purchase does not seem to be for resale
- `RETAILER_AGREED_TO_DO_CHANGES_TO_ORDER` - The retailer agreed so that they can do changes to the order
- `RETAILER_NOT_GOOD_FIT_FOR_BRAND` - The retailer who made an order does not fit the brand's criteria
- `RETAILER_VAT_NUMBER_MISSING` - The retailer VAT number is missing


- `OTHER` - If no reason above is suitable you can use value, but you **MUST** provide a `rejectReason` which accepts a plain text string of 1000 characters.

### Example rejection

```json
{
    "data": {
        "type": "brand-rejects",
        "attributes": {
            "rejectType": "ORDER_ITEMS_PRICES_INCORRECT"
        }
    }
}
```

### Example rejection with OTHER

```json
{
    "data": {
        "type": "brand-rejects",
        "attributes": {
            "rejectType": "OTHER",
            "rejectReason": "a different reason"
        }
    }
}
```

The order will move from `ankor_confirmed` to `cancelled`.


## Resetting an order's generated shipping labels

Can be performed when the order is in `shipping_labels_generated`.

To reject an order you must provide a reason listed below for the `resetType` field.

- `BRAND_NEED_MORE_LABELS_TO_SHIP` - Brand needs more shipping labels to ship
- `BRAND_PUT_WRONG_WEIGHT_DIMENSIONS` - Brand put wrong parcel weight and/or dimensions
- `BRAND_SHIPS_WITH_DIFFERENT_CARRIER` - Brand wants to ship with a different carrier
- `PROBLEM_DURING_SHIPPING_LABEL_GENERATION` - There was a problem during the shipping labels generation
- `RETAILER_ASKED_DELIVERY_ADDRESS_CHANGE` - The retailer asked for a delivery address change
- `SHIPPING_ADDRESS_MISMATCHES_PICKUP_ADDRESS` - The shipping address mismatches the pickup address


- `OTHER` - If no reason above is suitable you can use this value, but you **MUST** provide a `reason` which accepts a plain text string of 300 characters maximum.

### Example reset shipping labels

```json
{
    "data": {
        "type": "brand-resets-shipping-labels-generation",
        "attributes": {
            "resetType": "BRAND_PUT_WRONG_WEIGHT_DIMENSIONS"
        }
    }
}
```

### Example reset shipping labels with OTHER

```json
{
    "data": {
      "type": "brand-resets-shipping-labels-generation",
        "attributes": {
            "rejectType": "OTHER",
            "reason": "a different reason"
        }
    }
}
```

The order will move from `shipping_labels_generated` to `brand_confirmed`.
