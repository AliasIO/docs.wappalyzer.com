# Crawl API

Fetch and analyse a domain. The crawler fetches up to 25 pages in real-time and returns the combined results.

This API is asynchronous: results are sent to a callback URL after the domain has been indexed.


## Endpoint

`GET` `https://api.wappalyzer.com/crawl/v1/`


## Properties

| Property        | Description        |
|:----------------|:-------------------|
| Execution       | Asynchronous       | 
| Request timeout | 6s                 |  
| Rate limit      | 1 request / second |


## Parameters

| Name           | Required | Description                                                                    | Example               |
|:-------------- |:-------- |:------------------------------------------------------------------------------ |:--------------------- |
| `url`          | Yes      | URL of the first web page to analyze                                           | `https://example.com` |
| `callback_url` | Yes      | A POST request will be made to the callback URL upon completion of the request | `https://example.com` |


## Examples

**Example request**

``` sh
curl -H "x-api-key: wappalyzer.api.demo.key" \
"https://api.wappalyzer.com/crawl/v1/?url=https://example.com&callback_url=https://example.com"
```

**Example response (`202`)**

``` json
{
  "status": "received"
}
```

**Example callback response (success)**

The callback URL will receive a POST request when results become available.

``` json
{
  "url": "https://example.com",
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
  ],
  "status": "success"
}
```

**Example callback response (error)**

An [error type](/api/basics.html#error-types) and message is returned if no URLs could be fetched.

``` json
{
  "url": "https://example.com",
  "applications": [],
  "status": "error",
  "errors": [
    {
      "type": "RESPONSE_NOT_OK",
      "message": "Response was not ok"
    }
  ]
}
```
