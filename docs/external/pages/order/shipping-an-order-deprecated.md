---
tags: [order]
---

# Shipping an Order

| :zap: This documentation is deprecrated |
| --------------------------------------- |

When shipping with either method `ankorstore` or `custom` the brand must provide the parcel data it intends to ship the products in. This parcel information is required when generating a quote with either shipping method:

- `/v1/orders/{id}/ship/custom`
- `/v1/orders/{id}/ship/ankorstore`

The brand can generate many quotes between both providers or a single one with different parcel configurations before they decide to **confirm** the quote. An example flow:

```mermaid
sequenceDiagram

participant O as Order
participant SWC as Ship Custom
participant SWA as Ship Ankor
participant SC as Ship Confirm

O->>SWC: Generate quote
SWC-->>O: Unhappy with refund
O->>SWA: Generate quote
SWA-->>O: Cannot generate quote for retailers ship to address
Note over SWA,O: Sometimes AK shipping providers (UPS) cannot ship to certain addresses etc.
SWA-->>O: Unhappy with shipping cost fee
Note over SWA,O: A fee can be created as a contribution of the brand depending on the shipping costs
O->>SWC: Generate new custom quote
Note over O,SWC: Quote needs to be regenerated.
Note over O,SWC: Confirm endpoint uses last generated quote.
SWC->>SC: Brand ships with custom
```

## Shipping Methods

Within the `shippingOverview` object, there is a field that holds the supported shipping methods for an order:

```json
{
  "status": "brand_confirmed",
  ...
  "shippingOverview": {
    "availableShippingMethods": ["custom", "ankorstore"]
  }
}
```

Please check this before attempting to ship with ankorstore as it may not be available.

<!-- theme: warning -->

> #### Parcels
>
> On either shipping method, if the brand has previously generated a quote and is now generating a new one the full list of parcels must be provided. The quote endpoint **cannot** be called with an empty parcel list to re-use the old parcel data.

<!-- theme: danger -->

> #### Confirming a quote
>
> When confirming a quote only the **last generated quote** is used - this is the one in the order resource `latestQuote`. The `/v1/orders/{id}/ship/confirm` endpoint does not currently accept a quote ID.

Apart from `shippingMethod` that is at the order resource level, all information regarding shipping and tracking is stored within the `shippingOverview` object. This includes the `latestQuote` generated and the `shippingCostsOverview`.

## Shipping Costs Overview

<!-- theme: warning -->

> We advise you to pay attention on the shippingCostsOverview object, especially when type=fee as the amount referenced here will be subtracted from the brandTotalAmount.

In the `latestQuote` object, an object called `shippingCostsOverview` might appear depending on the shipping method selected.
This object includes the cost of the shipping and the type of the cost :

Fee payload example :

```json
{
  "shippingCostsOverview": {
    "amount": 5160,
    "currency": "EUR",
    "type": "fee"
  }
}
```

Refund payload example :

```json
{
  "shippingCostsOverview": {
    "amount": 1060,
    "currency": "EUR",
    "type": "refund"
  }
}
```

If the type is **fee**, it means that this amount will be subtracted from brand net amount as a contribution to shipping costs. If the type is **refund**, the amount will be refunded to the brand.

<!-- theme: info -->

> #### Want more information about shipping contribution?
>
> To find out more about Ankorstore's contribution to your shipping costs, please [visit our FAQs.](https://knowledgecommunity.force.com/faq/s/article/shippingpolicy?language=en_US)

## Shipping with the brands own carrier

The quote action will return an amount that will be refunded back to the brand based on the shipping grid refund matrix.

The confirm action is synchronous, meaning when the action is called the order resource will have its status changed to `shipped` upon returning a response. It is up to brand to provide tracking details for the retailer when calling this action.

```mermaid
sequenceDiagram

  participant O as Order
  participant SWC as Ship Custom
  participant SC as Ship Confirm
  participant SH as Shipped

  O->>SWC: Generate quote
  SWC-->>O: Quote OK
  O->>SC: Confirm quote
  SC->>SH: Status
  SC-->>O: OK
```

## Shipping with Ankorstore (asynchronous)

When calling the confirm action for the order's Ankorstore shipping quote, the returned order resource's status will still be `brand_confirmed`. A job in the background will generate the shipping labels for the order. It is this background processing that moves the status to `shipping_labels_generated`.

<!-- theme: info -->

> #### How will I know when the labels have been generated?
>
> Right now, the order must be polled periodically. Checking every minute would be suitable to detect the status change and then pull the label PDF data within [shipping overview](spec/v1.yaml/components/schemas/orderShippingOverview).

```mermaid
sequenceDiagram

  participant O as Order
  participant SWA as Ship Ankor
  participant SC as Ship Confirm
  participant BG as Async Processing
  participant C as Carrier
  participant SH as Shipped

  O->>SWA: Generate quote
  SWA-->>O: Quote OK
  O->>SC: Confirm quote
  SC-->>O: OK status=brand_confirmed
  SC-->>BG: Dispatch generate labels job
  Note over SC,BG: Async worker job
  BG-->>O: Labels generated, status=shipping_labels_generated
  Note over BG,O: (Async worker)
  C-->>SH: Parcels scanned, status=shipped
  Note over C,SH: (External service)
```

At step 6 (shipping labels generated) the download URL's for each packages label will be available within [shipping overview](spec/v1.yaml/components/schemas/orderShippingOverview). These labels are in PDF format and will also be visible in order details page on Ankorstore.

### Schedule a Pickup

If the brand is not taking the parcels to the local drop-off point for the carrier the brand can schedule a pickup for this order. A pickup can only be scheduled on a working day (monday to friday). For full information please refer to the API documentation.

<!-- theme: info -->

> #### Pickup is not accepted
>
> For now, Ankorstore does not know in advance which date/time configuration is available, if the requested pickup is denied please try a different date or time frame.
