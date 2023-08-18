---
tags: [jsonapi]
---

# Working with our API

<!-- theme: info -->

> #### API Specification
>
> Our [API specification](https://api-spec.ankorstore.com/) is based on the [JSON:API specification](https://jsonapi.org/). 
> Please give our [specification](https://api-spec.ankorstore.com/) a thorough read to understand how our request and response formats work.

## Headers

When making any request apart from retrieving an access token, the header `Accept: application/vnd.api+json` is required.

When sending JSON within the body of a request the header `Content-Type: application/vnd.api+json` is also required.

## Resource Data

In every response, the actual resource data can be found in the root level object `data`, this will either be an array or an object depending if its list based or not.

## Includes

Resources may have the ability to include data from other relations in the same request. For example, when fetching a single order you may include the retailer, the order's order items etc.

`?include=retailer,orderItems.productOption.product`

Includes also support nested relationships so you can pull every order items specific product variant and base product etc.

<!-- theme: info -->

> #### Making non-GET requests
>
> Includes are not just limited to `GET` requests, you may pass in these parameters for any other http method as well.

## Pagination

Ankorstore uses cursor based pagination. In the root level object for pagination supported endpoints you will find a `meta` object containing pagination information:

```yaml json_schema
  type: object
  description: Meta
  properties:
    meta:
      type: object
      description: Meta with Pagination Details
      properties:
        page:
          description: Cursor pagination details
          type: object
          properties:
            from:
              type: string
            to:
              type: string
            hasMore:
              type: boolean
            perPage:
              type: integer
```

As an example:

```json
{
  "meta": {
    "page": {
      "from": "63e9bacd-0288-4cf1-81ab-b34270c7f68a",
      "to": "747a1dcd-decc-4f8d-a9a9-61ff4d33d92e",
      "hasMore": true,
      "perPage": 15
    }
  }
}
```

You may manually construct the pagination query parameters yourself using `page[limit]` with `page[before]` or `page[after]` depending on which direction you wish to iterate over.

<!-- theme: info -->

> #### Effective pagination
>
> We recommend using the provided helper pagination links below instead of manually constructing the URLs yourself.

The response will also provide pagination links, so you do not need to construct these yourself:

```yaml json_schema
  type: object
  description: Links
  properties:
    links:
      description: Pagination navigation links. If a link does not exist then you cannot paginate any further in that direction.
      type: object
      properties:
        first:
          type: string
          format: url
        next:
          type: string
          format: url
        prev:
          type: string
          format: url
```

As an example:

```json
{
  "links": {
    "first": "https://www.ankorstore.com/api/v1/orders?include=&page%5Blimit%5D=15",
    "next": "https://www.ankorstore.com/api/v1/orders?include=&page%5Bafter%5D=1189606a-139e-4b4e-917c-b0c992498bad&page%5Blimit%5D=15",
    "prev": "https://www.ankorstore.com/api/v1/orders?include=&page%5Bbefore%5D=e04e17bb-9e70-4ecd-a8f5-4417d45b872c&page%5Blimit%5D=15"
  }
}
```

## Filters

An endpoint that supports filters requires each listed filter to be wrapped in a filter object. E.g. you may wish to only retrieve new orders:

`?filter[status]=ankor_confirmed`

You may also chain filters as well:

`?filter[status]=ankor_confirmed&filter[retailer]=1189606a-572e-4b4e-88da-b0c992498bad`

<!-- theme: info -->

> #### What filters can be used?
>
> The supported filters will be listed against each endpoint in the API documentation.
