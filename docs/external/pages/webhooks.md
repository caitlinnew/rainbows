# Webhooks

## Subscribing

To subscribe to events please go to the Applications section in your account area.

## Format

The webhook payload sent is the same as the resource object provided by the API. For example, if an `order.*` webhook is fired the resource will be `OrderResource`.

The only difference is that within the top level `meta` object you will receive an `event` object with details of the webhook which will look like this:

`POST https://your-webhook-url.com/webhooks/handle`
```json
{
  "meta": {
    "event": {
      "id": "1ecdb3d8-4cc7-64b0-81e2-0242ac140007",
      "applicationId": "1ecd6935-c442-6b2c-a36b-0242ac120008",
      "type": "order.brand_accepted",
      "timestamp": 1653381780
    }
  },
  "jsonapi": {
    "version": "1.0"
  },
  "data": {}
}
```

## Events

Our currently supported events are:

- `order.brand_created` - A new order is created for the brand
- `order.brand_accepted` - Brand accepts the order
- `order.brand_rejected` - Brand rejects the order
- `order.billing_address_updated` - Billing address is updated
- `order.shipping_address_updated` - Shipping address is updated
- `order.shipping_labels_generated` - When shipping with Ankorstore
- `order.shipped` - Fired by either shipping with custom or with Ankorstore
- `order.shipment_received` - Retailer confirms reception of the order
- `order.shipment_refused` - Retailer refuses the order
- `order.brand_paid` - Ankorstore pays the brand for the order
- `order.cancelled` - Order is cancelled
- `order.brand_accepted_reverted` - Reverted Brand accepting the order 
- `order.shipping_labels_generated_reverted` - Reverted shipment labels generation, when shipping with Ankorstore 

## Signature

Our webhooks provide a signature so you can verify the payload hasn't been tampered with.
This signature can be found on the header `signature`.

This is how the signature is calculated:

```
$payloadJson = json_encode($payload);
$signature = hash_hmac('sha256', $payloadJson, $secret);
```

You can calculate the webhook's signature and compare it to the one present on the request.

## Retries

If the response status code is not a `2xx` then we will retry the webhook for 5 attempts with an exponential backoff.
