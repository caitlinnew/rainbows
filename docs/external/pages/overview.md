# Ankorstore API

Welcome to the initial release of the Ankorstore API. The documentation is a continual process so please provide feedback.

## API Specification/Schema

This API follows the [JSON:API](https://jsonapi.org/) schema (not to be confused with a generic JSON API or JSON Schema standard).

## What is currently supported

From a high level the API currently supports the following actions:

- **[Orders](https://ankorstore.stoplight.io/docs/ankorstore-api/cb282a9f5daef-working-with-orders)**

  - [List all your orders](https://ankorstore.stoplight.io/docs/ankorstore-api/da61cce6d6868-list-orders) with the ability to filter by either the `retailerId` or the orders `status`
  - [Retrieve a single order](https://ankorstore.stoplight.io/docs/ankorstore-api/4ba53fad90a76-get-order) where a lot more information is provided (such as shipping data, order items, etc.)
  - [Accept an order](https://ankorstore.stoplight.io/docs/ankorstore-api/7d5b85f757550-transition-an-order) **with or without** any modifications to quantities of order items
  - [Reject an order](https://ankorstore.stoplight.io/docs/ankorstore-api/7d5b85f757550-transition-an-order)
  - [Reset an order's generated shipping label](https://ankorstore.stoplight.io/docs/ankorstore-api/7d5b85f757550-transition-an-order)

- **[Products](https://ankorstore.stoplight.io/docs/ankorstore-api/f2d05f89b82c7-working-with-products)**

  - [List all your products](https://ankorstore.stoplight.io/docs/ankorstore-api/bff9e14e17a17-list-products)
  - [Retrieve a single product](https://ankorstore.stoplight.io/docs/ankorstore-api/841ae4704ad81-get-product)
  - [List product variants with their stock](https://ankorstore.stoplight.io/docs/ankorstore-api/a289bcf9f3669-list-product-variants-with-their-stock)
  - [Retrieve a product variant with its stock](https://ankorstore.stoplight.io/docs/ankorstore-api/ac68383e6a8e3-get-product-variant-with-stock)

- **Shipping**

  - [Generate a quote for shipping with your own carrier](https://ankorstore.stoplight.io/docs/ankorstore-api/96210e4b52980-ship-order-with-custom)
  - [Generate a quote for shipping with Ankorstore](https://ankorstore.stoplight.io/docs/ankorstore-api/9a16e40f85106-list-shipping-quotes)
  - [Confirm the shipping quote](https://ankorstore.stoplight.io/docs/ankorstore-api/1a88398eb1d4b-confirm-shipping-quote) and move the order into its shipping phase
  - [Schedule a pickup](https://ankorstore.stoplight.io/docs/ankorstore-api/3b0741b894462-schedule-pickup-for-order) for the order (if shipped with Ankorstore)

- **[Stock management](https://ankorstore.stoplight.io/docs/ankorstore-api/dba4536ebe2e5-product-stock-management)**

  - [Update product variant stock](https://ankorstore.stoplight.io/docs/ankorstore-api/0e68293971f11-update-product-variant-stock)
  - [Perform bulk operations](https://ankorstore.stoplight.io/docs/ankorstore-api/4074887383f06-bulk-operations) on different resources

- **[Webhooks](https://ankorstore.stoplight.io/docs/ankorstore-api/653df498eb9a8-webhooks)** sent by Ankorstore when a specific event occurs

## Recommendations on interacting with the API

It is recommended that you only use the order list endpoint to retrieve newly created orders or to get the status of top level information (e.g. such as new orders or if you have been paid yet). Please use the single order retrieval endpoint if you wish to fetch more information about each order.

## Testing

To test the API you can use our public sandbox environment

<!-- theme: info -->

> https://www.public.sandbox.ankorstore.com/

## How to get a public sandbox account?

### Already live on Ankorstore?

If your Brand is already live on Ankorstore, please try logging in with your credentials on Ankorstore public sandbox. If it does not work, please contact your Ankorstore Brand Development Manager and request an account. Once the account is created, you will receive an email with the sandbox link to set up your credentials.

### Not on Ankorstore yet?

In case you are not yet live with Ankorstore, please contact our Sales Team.

## Using the public sandbox environment

When you test the order management flow through the API you may want to create some dummy orders to test the API integration, to do that you can create any number of test retailer accounts on the sandbox in order to create test orders for your brand. When placing an order you can pay using the test Stripe credit card credentials below:

Card Number: `4242 4242 4242 4242`
Expiry: `04/42`
CVV: `424`

## Rate Limiting

When **authenticated** (passing a valid bearer token), the rate limits are:

- 600 requests per minute
- 24000 per hour
- 288000 per day

Meaning you could make a consistent 200 requests per minute, every minute, all day.

Or you could burst at up to 600 requests per minute for 40 minutes of an hour, for 12 hours of a day.

When **unauthenticated** for endpoints that do not require authentication the rates are:

- 120 requests per minute
- 4800 requests per hour
- 57600 requests per day

Api responses have the following headers to help you track and manage the rate limits:

- `X-RateLimit-Remaining`: The number of request remaining before encountering a `429 Too Many Requests` error.
- `X-RateLimit-Limit`: The number of requests allowed by the current window.

If you exceed these rate limits then an error status code of `429 Too Many Requests` will be returned, you will also receive the following headers:

- `Retry-After`: The number of seconds before more attempts become available, and you could try again.
- `X-RateLimit-Reset`: A timestamp for when more attempts become available, and you could try again, specified as seconds-since-epoch.

The special exception to these limits the authentication endpoint `/oauth/token` which is limited to 60 requests per hour.
You should not need to authenticate more than this, if you do please get in contact with us.
