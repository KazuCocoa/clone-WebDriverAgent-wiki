This is list of most common endpoints with some query examples using `curl` with pre-set environment variables:
* `DEVICE_URL` set as device URL (eg. `http://localhost:8100`)
* `SESSION_ID` set as session id. Returned by start session command e.g. `D15E12F6-CA23-4CD4-89F9-E5C5EA6F4FAD`
* `JSON_HEADER='-H "Content-Type: application/json"'`

WebDriverAgent is intended to implement [WebDriver spec](https://w3c.github.io/webdriver/webdriver-spec.html) so we will not get much into details as you can simply read WebDriver spec.

# Checking service status
```
curl -X GET $JSON_HEADER $DEVICE_URL/status
```

# Session handling
### Starting session and launching application
If application is already installed on device you can start it using `bundleId` parameter:
```
curl -X POST $JSON_HEADER \
-d "{\"desiredCapabilities\":{\"bundleId\":\"com.apple.mobilesafari\"}}" \
$DEVICE_URL/session
```
It is also possible to request to install application before launching it by adding `app` parameter:
```
curl -X POST $JSON_HEADER \
-d "{\"desiredCapabilities\":{\"bundleId\":\"com.apple.mobilesafari\", \"app\":\"[host_path]/magicapp.app\"}}" \
$DEVICE_URL/session
``` 
### Querying current session
`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID`
### Removing session and killing application
`curl -X DELETE $JSON_HEADER $DEVICE_URL/session/$SESSION_ID`

# Application related queries
### Inspector
Open web browser at inspector endpoint [/inspector](http://localhost:8100/inspector)
### Go to home screen
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/homescreen`
### Get a screenshot
`curl -X GET $JSON_HEADER $DEVICE_URL/screenshot`
### Deactivate application for given time
`curl -X POST $JSON_HEADER -d "{\"duration\":3}" $DEVICE_URL/session/$SESSION_ID/deactivateApp`
### Change device orientation
Supported orientations are:
- PORTRAIT
- LANDSCAPE
- UIA_DEVICE_ORIENTATION_LANDSCAPERIGHT
- UIA_DEVICE_ORIENTATION_PORTRAIT_UPSIDEDOWN
```
curl -X POST $JSON_HEADER \
-d "{\"orientation\":\"LANDSCAPE\"}" \
$DEVICE_URL/session/$SESSION_ID/orientation`
```
### Source aka tree 
`curl -X GET $JSON_HEADER $DEVICE_URL/source`

# Searching for elements
Some of element search endpoints use property names listed [here](https://github.com/facebook/WebDriverAgent/wiki/Queries#querying-properties).

You can search for elements by:
* property with given value (`link text`)
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"link text\",\"value\":\"label=Apple\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

* property with given partial value (`partial link text`)
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"partial link text\",\"value\":\"label=App\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

* using `class name`
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"class name\",\"value\":\"XCUIElementTypeButton\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

* using `xpath`
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"xpath\",\"value\":\"//XCUIElementTypeButton[@name='Share']\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

* using `predicate string`
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"predicate string\",\"value\":\"isWDVisible=1\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

In same manner you can query subelements of given element with id by using `/element/:id/elements` endpoint.
* You can also list all cell for given table view or collection view:

`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID/uiaElement/:id/getVisibleCells`


# Interacting with elements
### Querying properties
All elements returned by search endpoints have assigned `element_id`. Given `element_id` you can query properties like `enabled`, `rect`, `size`, `location`, `text`, `displayed`, `accessible`, `name` e.g.:

`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID/element/5/displayed`

or using by using `element/5/attribute/:name` endpoint e.g.:

`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID/element/5/attribute/name`

### Tapping element
```
curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/element/5/click
```
### Typing text
```
curl -X POST $JSON_HEADER \
-d "{\"value\":[\"h\",\"t\",\"t\",\"p\",\":\",\"/\",\"/\",\"g\",\"i\",\"t\",\"h\",\"u\",\"b\",\".\",\"c\",\"o\",\"m\",\"\\n\"]}" \
$DEVICE_URL/session/$SESSION_ID/element/5/value
```
### Clearing text
```
curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/element/5/clear
```

# Alerts
### Get alert
`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID/alert_text`
### Accept alert
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/accept_alert`
### Dismiss alert
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/dismiss_alert`

# Touch ID
### Match TouchID
`curl -X POST $JSON_HEADER -d "{\"match\":1}" $DEVICE_URL/session/$SESSION_ID/simulator/touch_id`
### Do not match TouchID
`curl -X POST $JSON_HEADER -d "{\"match\":0}" $DEVICE_URL/session/$SESSION_ID/simulator/touch_id`

# Swipe
### Swipe Left
curl -X GET $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/element/5/swipeLeft
### Swipe Right
curl -X GET $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/element/5/swipeRight