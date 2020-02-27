# Lookup API

Look up a domain in the Wappalyzer database. Results are near-instant and site-wide and include historic data where available.

If Wappalyzer hasn't seen the domain before, it will be indexed automatically. Results are sent to an optional callback URL once they become available.


## Endpoint

`GET` `https://api.wappalyzer.com/lookup/v1/`


## Properties

| Property                 | Description 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	 	|
|:-------------------------|:---------------------------------------------------------|
| Execution                | Synchronous / Asynchronous (when call yields no results) |
| Request timeout          | 30s                                                      | 
| Rate limit               | 1 request / second                                       |


## Parameters

| Name           | Required | Description                                                                                                        | Example               |
|:-------------- |:-------- |:------------------------------------------------------------------------------------------------------------------ |:--------------------- |
| `url`          | Yes      | URL of the website                                                                                                 | `https://example.com` |
| `callback_url` | No       | If instant results are unavailable, A POST request will be made to the callback URL upon completion of the request | `https://example.com` |
| `denoise`      | No       | Exclude low confidence results (`true` (default) or `false`)                                                       | `false`               |


## Examples

**Example request**

``` sh
curl -H "x-api-key: wappalyzer.api.demo.key" \
"https://api.wappalyzer.com/lookup/v1/?url=https://example.com&callback_url=https://example.com"

```
**Example response (`200`)**

Results are grouped by month and may contain data from anywhere between six months ago and today.

``` json
[
  {
    "monthYear": "01-2020",
    "languages": [],
    "applications": [
      {
        "name": "Craft CMS",
        "categories": [
          "CMS"
        ],
        "versions": [],
        "hits": 0
      }
    ]
  }
]
```

The `hits` value is the number of times users of the [browser extension](https://www.wappalyzer.com/download) have visited the website that month. It can be used as an indicator of traffic.

**Example response (`202`)**

If instant results are unavailable and `callback_url` is specified, a `202` is returned and the domain will be indexed using the [Crawl API](/api/resources/crawl) as a fallback. The callback URL will be called upon completion of the request, typically minutes later.

``` json
{
  "status": "received"
}
```

If no callback URL is specified, a `404` response code will be returned instead. 

::: tip NOTE
A `404` response will still be counted towards the subscription quota. Provide a `callback_url` if possible.
:::

**Example callback response**

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
  ]
}
```
