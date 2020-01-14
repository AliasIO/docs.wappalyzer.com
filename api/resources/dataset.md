# Dataset API

Purchasing a dataset requires two requests: one to obtain a quote and one to confirm the order. Both requests take a variable amount of time to complete and require a callback URL to handle the response.

Transactional emails including order confirmations, receipts and invoices, will be send to the email address associated with the subscription in addition to the callbacks.


## Endpoint: request quote

`POST` `https://api.wappalyzer.com/dataset/v1/`


## Properties

| Property        | Description                                                                                                                      |
|:----------------|:-------------------------------------------------------- |
| Execution       | Asynchronous                                             |
| Request timeout | 20s                                                      |
| Rate limit      | 1 request / 10 seconds (burstable to 1 request / second) | 


## Request body (JSON)

| Name               | Required | Description                                                                    | Example               |
|:------------------ |:-------- |:------------------------------------------------------------------------------ |:--------------------- |
| `technologies`     | Yes      | Array of technology names (refer to the full list)                             | `["Craft CMS"]`       |
| `languages`        | No       | Array of language tags to filter by                                            | `["en-US"]`           |
| `tlds`             | No       | Array of top-level domains to filter by                                        | `[".com"]`            |
| `matchLanguageTld` | No       | Match language and top-level domain (yields fewer results)                     | `true`                |
| `subset`           | No       | Limit results to *n* top traficked websites                                    | `1000`                |
| `callback_url`     | Yes      | A POST request will be made to the callback URL upon completion of the request | `https://example.com` |


## Examples

**Example request**

``` sh
curl -H "x-api-key: your.api.key" \
-d '{ "technologies": ["Craft CMS"], "callbackUrl": "https://example.com" }' \
"https://api.wappalyzer.com/dataset/v1/"
```

**Example response (`202`)**

``` json
{
  "orderRef": "ABCDEFGHIJK"
}
```

**Example callback response**

``` json
{
  "status": "new"
  "description": "Dataset, 1,000 records",
  "amount": 70,
  "currency": "AUD",
  "records": {
    "counts": [
      "Craft CMS": 1000
    ],
    "subset": 1000
  },
  "query": {
    "technologies": [
      "Craft CMS"
    ]
    "languages": [
      "en-US"
    ],
    "tlds": [
      ".com"
    ],
    "matchLangTld": true
  },
  "dataset": {
    "url": null
  }
}
```


## Endpoint: confirm order

`POST` `https://api.wappalyzer.com/dataset/v1/{orderRef}`

To confirm a purchase, use the order reference `orderRef` returned by the previous API call to make a second request. Within minutes, the completed order details will be sent to the callback URL, including a link to download the dataset.

::: warning NOTE
The quoted amount will be charged to the credit card associated with your subscription.
:::


## Properties

| Property        | Description                                                                                                                      |
|:----------------|:-------------------------------------------------------- |
| Execution       | Asynchronous                                             |
| Request timeout | 20s                                                      |
| Rate limit      | 1 request / 10 seconds (burstable to 1 request / second) |


## Examples

**Example request**

``` sh
curl -H "X-Api-Key: your.api.key" -X POST \
https://api.wappalyzer.com/dataset/v1/ABCDEFGHIJK
```

**Example response (`202`)**

``` json
{
  "orderStatus": "paid"
}
```

**Example callback response**

``` json
{
  "status": "complete"
  "description": "Dataset, 1,000 records",
  "amount": 70,
  "currency": "AUD",
  "records": {
    "counts": [
      "Craft CMS": 1000
    ],
    "subset": 1000
  },
  "query": {
    "technologies": [
      "Craft CMS"
    ]
    "languages": [
      "en-US"
    ],
    "tlds": [
      ".com"
    ],
    "matchLangTld": true
  },
  "dataset": {
    "url": "https://www.wappalyzer.com/files/wappalyzer-abcd-210101-ABCDEFGHIJK.zip"
  }
}
```
