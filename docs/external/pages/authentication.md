---
tags: [authentication]
---

# Authentication

The Ankorstore API uses OAuth2 authentication, in order to get a `client_id` and a `client_secret` please login at https://www.ankorstore.com/ and generate a new application in the [integrations](https://www.ankorstore.com/account/integrations) area of your account.

<!-- theme: warning -->
> #### Testing Environment
>
> We recommend using the testing environment to start off with, you can find more information about it in the [overview page](/docs/ankorstore-api/pages/overview.md).

To generate a token pass in your `client_id` and `client_secret`:

```json http
{
  "method": "post",
  "headers": {
    "accept": "application/json",
    "Content-Type": "application/x-www-form-urlencoded"
  },
  "baseUrl": "https://www.ankorstore.com",
  "url": "/oauth/token",
  "body": "grant_type=client_credentials&client_id={{client_id}}&client_secret={{client_secret}}&scope=*"
}
```

<!-- theme: info -->
> #### Failing to authenticate with Postman?
>
> Please enable the setting `Follow Authorization Header`
>
> ![Postman Follow Authorization Header Setting](https://www.ankorstore.com/images/docs/postman-follow-authorization-header.png) "Postman Follow Authorization Header Setting")


You will receive an `access_token`:

```json
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiI5NWZmZDJkYy0zYjE5LTQzMWQtYjJkYi03ZTNmMWQxMDA1MWUiLCJqdGkiOiI3NzExN2Y3ODQ2YWVkMjQyYTVmYWVlZDZmOGRjMjcyNmQyNjIzYTZmODlmMjZkMTVlNDk0NDE1ZDAyY2E3MDU5M2M4YTU3ZjllN2Y4OTZjOSIsImlhdCI6MTY0OTI1OTg4MS4yMjQ0NjIsIm5iZiI6MTY0OTI1OTg4MS4yMjQ0NjcsImV4cCI6MTY0OTI2MzQ4MS4yMTM2NjcsInN1YiI6IjE1OTUiLCJzY29wZXMiOlsiKiJdfQ.E3CirFN_hgQZmxG10VSXZPs1sYml8cnsYXgzt7Zzr3sG6YT-soEvDmlsiJyMXjiG_duVfsFwut2JXPfL9N2NZ0LTdWtPddz6rxTRF4o-gId8Z62ROOJJvHVNmObl_ZW6_6fKMPgLX_bIkFO4twh6_DfXE7ju40iybwBmPMtzYaf8_i03truuIy7T1oSldvsEgrdMSl4uYfeAj24nnW7IYsMZujBHBhSZxlDVIzGb1ZaK2L34pXwwqrIXg_572R22s9e5dyejHBBjWC0gbUvo1F2RL5Um4UEtUe6_ImvPXQAB4DY55TK_JwIIiEAAxbUJ1gnpRB9d-Zift2akFhwoESMOqd3JGsatj0Poync-NwUmZGO8bI3sjP2jcsKfTSwMNap4B2vaerW_NOP3BX-3pqUTSqSOD2jFtKwE3Etxgqub38DBQ-9p7w9pVSxBcTWb583t25zCLInE9PXxpBtYswictIAlziXFzZpsGgZiyi9aXN_oMttAknOuYsEfz8SsZxJp9qnjiprUcfGHnjOvDum6rK4xhKsFrGEw2UmUkO9QTEi7Co_GclaMUOrZgLD5eF5dVCMS7cbNZESF3O30Knn1fAwXV7iGJeHEmEZHKueEOernNVyRWCoSq7K6qQZmzNJJ06IJXUYIsao6e9wL0w47NmbyhbrHKfleiRPKRxw"
}
```

This access token has an expiry, in which you will receive an `401 Unauthenticated` response. To generate a new one you can re-call the `/oauth/token` endpoint described above.

To authenticate with every other endpoint pass in the header `Authorization: Bearer {token}`. You can test if your token is working with this endpoint (this is a not a JSON:API based endpoint):

```json http
{
  "method": "get",
  "headers": {
    "accept": "application/json",
    "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiI5NWZmZDJkYy0zYjE5LTQzMWQtYjJkYi03ZTNmMWQxMDA1MWUiLCJqdGkiOiI3NzExN2Y3ODQ2YWVkMjQyYTVmYWVlZDZmOGRjMjcyNmQyNjIzYTZmODlmMjZkMTVlNDk0NDE1ZDAyY2E3MDU5M2M4YTU3ZjllN2Y4OTZjOSIsImlhdCI6MTY0OTI1OTg4MS4yMjQ0NjIsIm5iZiI6MTY0OTI1OTg4MS4yMjQ0NjcsImV4cCI6MTY0OTI2MzQ4MS4yMTM2NjcsInN1YiI6IjE1OTUiLCJzY29wZXMiOlsiKiJdfQ.E3CirFN_hgQZmxG10VSXZPs1sYml8cnsYXgzt7Zzr3sG6YT-soEvDmlsiJyMXjiG_duVfsFwut2JXPfL9N2NZ0LTdWtPddz6rxTRF4o-gId8Z62ROOJJvHVNmObl_ZW6_6fKMPgLX_bIkFO4twh6_DfXE7ju40iybwBmPMtzYaf8_i03truuIy7T1oSldvsEgrdMSl4uYfeAj24nnW7IYsMZujBHBhSZxlDVIzGb1ZaK2L34pXwwqrIXg_572R22s9e5dyejHBBjWC0gbUvo1F2RL5Um4UEtUe6_ImvPXQAB4DY55TK_JwIIiEAAxbUJ1gnpRB9d-Zift2akFhwoESMOqd3JGsatj0Poync-NwUmZGO8bI3sjP2jcsKfTSwMNap4B2vaerW_NOP3BX-3pqUTSqSOD2jFtKwE3Etxgqub38DBQ-9p7w9pVSxBcTWb583t25zCLInE9PXxpBtYswictIAlziXFzZpsGgZiyi9aXN_oMttAknOuYsEfz8SsZxJp9qnjiprUcfGHnjOvDum6rK4xhKsFrGEw2UmUkO9QTEi7Co_GclaMUOrZgLD5eF5dVCMS7cbNZESF3O30Knn1fAwXV7iGJeHEmEZHKueEOernNVyRWCoSq7K6qQZmzNJJ06IJXUYIsao6e9wL0w47NmbyhbrHKfleiRPKRxw"
  },
  "baseUrl": "https://www.ankorstore.com",
  "url": "/api/v1/me",
}
```

If successful you will receive a 200 response:

```json
{
  "data": {
    "type": "user",
    "id": 1234,
    "email": "api@ankorstore.com",
    "first_name": "Test",
    "last_name": "Account",
    "created_at": "2020-01-28T11:26:35+00:00",
    "updated_at": "2022-03-15T15:23:09+00:00"
  }
}
```
