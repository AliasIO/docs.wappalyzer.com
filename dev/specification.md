# Specification

A long list of [regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) is used to identify technologies on web pages. Wappalyzer inspects HTML code, as well as JavaScript variables, response headers and more.
src/apps.json

Patterns (regular expressions) are kept in [`src/apps.jon`](https://github.com/AliasIO/Wappalyzer/blob/master/src/apps.json). The following is an example of an application fingerprint.

**Example**

``` json
"Example": {
  "cats": [ 
    "1" 
  ],
  "cookies": { 
    "cookie_name": "Example"
  },
  "js": {
    "Example.method": ""
  },
  "excludes": "Example",
  "headers": { 
	"X-Powered-By": "Example" 
  },
  "html": "<link[^>]example\\.css",
  "implies": "PHP\\;confidence:50",
  "meta": { 
	"generator": "(?:Example|Another Example)"
  },
  "script": "example-([0-9.]+)\\.js\\;confidence:50\\;version:\\1",
  "url": ".+\\.example\\.com",
  "website": "https://example.com",
}
```


## JSON fields

Find the JSON schema at schema.json.


### Properties (required)

| Field     | Type   | Description                                                                                                | Example                 |
|:--------- |:------ |:---------------------------------------------------------------------------------------------------------- |:----------------------- |
| `cats`    | Array  | One or more category IDs. Refer to the [list of category IDs](https://www.wappalyzer.com/categories/list). | `[1, 6]`                |
| `icon`    | String | Application icon filename.                                                                                 | `"WordPress.svg"`       |
| `website` | String | URL of the application's website.                                                                          | `"https://example.com"` |


### Implies and excludes (optional)

Field   Type   Description   Example
implies   String | Array   The presence of one application can imply the presence of another, e.g. WordpPress means PHP is also in use.   "PHP"
excludes   String | Array   Opposite of implies. The presence of one application can exclude the presence of another.   "Apache"


### Patterns (optional)

| Field     | Type            | Description                                                                                  | Example                             |
|:--------- |:--------------- |:-------------------------------------------------------------------------------------------- | ----------------------------------- |
| `cookies` | Object          | Cookies.                                                                                     | `{ "cookie_name": "Cookie value" }` |
| `js`      | Object          | JavaScript methods (case sensitive). Avoid short patterns to prevent matching minified code. | `{ "jQuery.fn.jquery": "" }`        |
| `headers` | Object          | HTTP response headers.                                                                       | `{ "X-Powered-By": "^WordPress$" }` |
| `html`    | String \| Array | HTML source code.  Patterns must include an HTML opening tag to avoid matching plain text.   | `"<a [^>]*href=\"index.html"`       |
| `url`     | String          | Full URL of the page.                                                                        | `"^https?//.+\\.wordpress\\.com"`   |
| `meta`    | Object          | HTML meta tags, e.g. generator.                                                              | `{ "generator": "^WordPress$" }`    |
| `script`  | String \| Array | URLs of JavaScript files included on the page.                                               | `"jquery\\.js"`                     |


### Patterns

Patterns are essentially JavaScript regular expressions written as strings, but with some additions.


## Quirks and pitfalls

* Because of the string format, the escape character itself must be escaped when using special characters such as the dot (`\\.`). Double quotes must be escaped only once (`\"`). Slashes do not need to be escaped (`/`).
* Flags are not supported. Regular expressions are treated as case-insensitive.
* Capture groups (`()`) are used for version detection. In other cases, use non-capturing groups (`(?:)`).
* Use start and end of string anchors (`^` and `$`) where possible for optimal performance.
* Short or generic patterns can cause applications to be identified incorrectly. Try to find unique strings to match.


## Tags

Tags (a non-standard syntax) can be appended to patterns (and implies and excludes, separated by `\\;`) to store additional information.

| Tag          |  Description                                                                                                                                              | Example                                          |
|:------------ |:--------------------------------------------------------------------------------------------------------------------------------------------------------- |:------------------------------------------------ |
| `confidence` | Indicates a less reliable pattern that may cause false positives. The aim is to achieve a combined confidence of 100%. Defaults to 100% if not specified. | `"js": { "Mage": "\\;confidence:50" }`           |
| `version`    | Gets the version number from a pattern match using a special syntax.                                                                                      | `"script": "jquery-([0-9.]+)\.js\\;version:\\1"` |


## Version syntax

Application version information can be obtained from a pattern using a capture group. A condition can be evaluated using the ternary operator (`?:`).

| Example   | Description                                                       | 
|:--------- |:----------------------------------------------------------------- |
| `\\1`     | Returns the first match.                                          |
| `\\1?a:`  | Returns a if the first match contains a value, nothing otherwise. |
| `\\1?a:b` | Returns a if the first match contains a value, b otherwise.       |
| `\\1?:b`  | Returns nothing if the first match contains a value, b otherwise. |
| `foo\\1`  | Returns foo with the first match appended.                        |
