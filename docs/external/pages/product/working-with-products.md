---
tags: [product]
---

# Working with Products

Here you will find information about the product resource and its sub-resources. If you need further information please refer to the API specification.

<!-- theme: info -->
### Including Product Variants
> When retrieving the individual product via the API, you may pass an `?include=productVariants` query parameter that will return associated product variants inside the `included` root level object. 

## Important Fields within Product Resource

If the field contains no data, it will be `null`.