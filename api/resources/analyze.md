# Analyze API

Fetch and analyse a single web page in real-time.


## Endpoint

`GET` `https://api.wappalyzer.com/analyze/v1/`


## Properties

| Property        | Description                                                                                                                      |
|:----------------|:--------------------------------------------------------------------------------------------|
| Execution       | Synchronous                                                                                 | 
| Request timeout | 30s                                                                                         | 
| Rate limit      | 1 request / second on the Small plan<br>10 requests / second on the Medium and Large plans  |


## Parameters

| Name           | Required | Description                    | Example               |
|:-------------- |:-------- |:------------------------------ |:--------------------- |
| `url`          | Yes      | URL of the web page to analyse | `https://example.com` |

## Examples

**Example request**

``` sh
curl -H "x-api-key: wappalyzer.api.demo.key" \
"https://api.wappalyzer.com/analyze/v1/?url=https://example.com"
```

**Example response (success)**

``` json
{
  "status": "success",
  "applications": [
    {
      "name": "Craft CMS",
      "confidence": "100",
      "version": "",
      "icon": "CraftCMS.svg",
      "website": "https://craftcms.com",
      "categories": [
        {
          "1": "CMS"
        }
      ]
    }
  ]
]
```

**Example response (error)**

An [error type](/api/basics.html#error-types) and message is returned if the URL could not be fetched.

::: tip NOTE
Unsuccesful responses count towards the subscription quota.
:::

``` json
{
  "status": "error",
  "errors": [
    {
      "type": "RESPONSE_NOT_OK",
      "message": "Response was not ok"
    }
  ],
  "applications": []
}
```
