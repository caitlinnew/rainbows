# Product Stock Management

This API allows you to manage your product variants stock in both single- and bulk-operation mode.
There are 2 opposite options available for the stock update requests - set an explicit product quantity 
or mark the corresponding product as "always in stock".

## Set product variant stock explicit quantity

To set an explicit quantity for the particular product variant, you should specify the amount in the payload.
In case if the target product variant was previously marked as "always in stock", this option will be disabled and the stock
will be set to given value.

Example of the payload to set a product variant stock to the given value (single-operation mode):

`PATCH /api/v1/product-variants/1ed18988-6651-610e-8223-aa5cd9844f96/stock`
```json
{
  "data": {
    "type": "productVariants",
    "id": "1ed18988-6651-610e-8223-aa5cd9844f96",
    "attributes": {
      "stockQuantity": 123
    }
  }
}
```
More details can be found in the endpoint specification

## Set product variant as "always in stock"

To mark a particular product variant as "always in stock" and do not care about the stock amounts, you should include a 
flag `isAlwaysInStock` into the request payload. In case if the target product variant had explicit stock amount set previously,
it will be reset.

Example of the payload to set a product variant stock to the given value (single-operation mode):

`PATCH /api/v1/product-variants/1ed18988-6651-610e-8223-aa5cd9844f96/stock`
```json
{
  "data": {
    "type": "productVariants",
    "id": "1ed18988-6651-610e-8223-aa5cd9844f96",
    "attributes": {
      "isAlwaysInStock": true
    }
  }
}
```
More details can be found in the endpoint specification

## Update stocks of multiple product variants in single request (bulk-operation mode)

This API allows to update stocks of multiple product variants in single request. There is a specific endpoint which
accepts up to 50 operations per request. Validation, business rules and payload of each operation is identical to the 
single-operation mode, described above.

Example of the payload to update stocks of multiple product variants in single request:

`POST /api/v1/operations`
```json
{
  "atomic:operations": [
    {
      "op": "update",
      "data": {
        "type": "productVariants",
        "id": "1ed18988-3253-610e-8223-aa5cd9844001",
        "attributes": {
          "isAlwaysInStock": true
        }
      }
    },
    {
      "op": "update",
      "data": {
        "type": "productVariants",
        "id": "1ed18988-6651-610e-8223-aa5cd9844f96",
        "attributes": {
          "stockQuantity": 123
        }
      }
    }
  ]
}
```
More details can be found in the endpoint specification
