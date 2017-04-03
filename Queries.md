This is list of most common endpoints with some query examples using `curl` with pre-set environment variables:
* `DEVICE_URL` set as device URL (eg. `http://localhost:8100`)
* `SESSION_ID` set as session id. Returned by start session command e.g. `D15E12F6-CA23-4CD4-89F9-E5C5EA6F4FAD`. If you want to use WebDriverAgent without launching an app you can use the default session ID reported by the `/status` endpoint.
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

If you want to use WebDriverAgent without launching an app you can use the default session ID reported by the `/status` endpoint.

### Querying current session
`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID`
### Removing session and killing application
`curl -X DELETE $JSON_HEADER $DEVICE_URL/session/$SESSION_ID`

# Application related queries
### Inspector
Open web browser at inspector endpoint [/inspector](http://localhost:8100/inspector)
### Go to home screen
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/wda/homescreen`
### Get a screenshot
`curl -X GET $JSON_HEADER $DEVICE_URL/screenshot`
### Deactivate application for given time
`curl -X POST $JSON_HEADER -d "{\"duration\":3}" $DEVICE_URL/session/$SESSION_ID/wda/deactivateApp`
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
-d "{\"using\":\"predicate string\",\"value\":\"wdVisible==1\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

Valid attribute names for predicate search are property names defined in https://github.com/facebook/WebDriverAgent/blob/master/WebDriverAgentLib/Routing/FBElement.h protocol. Also, their shortcuts without 'wd' prefixes are supported ('wdVisible' -> 'visible').

In same manner you can query subelements of given element with id by using `/element/:id/elements` endpoint.


* using `class chain`
```
curl -X POST $JSON_HEADER \
-d "{\"using\":\"class chain\",\"value\":\"XCUIElementTypeWindow[`value BEGINSWITH 'blabla'`]/*[2]/*/XCUIElementTypeButton[-1]\"}" \
$DEVICE_URL/session/$SESSION_ID/elements
```

This query is similar to xpath, but can only include indexes, predicates and valid class names. Only search by direct children elements of the current element is supported. Examples of such requests:
1. _XCUIElementTypeWindow/XCUIElementTypeButton[3]_: select the third child button of the first child window element
1. _XCUIElementTypeWindow_: select all the children windows
1. _XCUIElementTypeWindow[2]_: select the second child window in the hierarchy. Indexing starts at 1
1. _XCUIElementTypeWindow/XCUIElementTypeAny[3]_: select the third child (of any type) of the first child window
1. _XCUIElementTypeWindow[2]/XCUIElementTypeAny_: select all the children of the second child window
1. _XCUIElementTypeWindow[2]/XCUIElementTypeAny[-2]_: select the second last child of the second child window
 One may use '*' (star) character to substitute the universal 'XCUIElementTypeAny' class name
1. _XCUIElementTypeWindow[`name CONTAINS[cd] "blabla"`]_: select all windows, where name attribute starts with "blabla" or "BlAbla"
1. _XCUIElementTypeWindow[`label BEGINSWITH "blabla"`][-1]_: select the last window, where label text begins with "blabla"
1. _XCUIElementTypeWindow/XCUIElementTypeAny[`value == "bla1" OR label == "bla2"`]_ select all children of the first window, where value is "bla1" or label is "bla2"
1. _XCUIElementTypeWindow[`name == "you're the winner"`]/XCUIElementTypeAny[`visible == 1`]_ select all visible children of the first window named "you're the winner"
Predicate string should be always enclosed into ` characters inside square brackets. Use `` to escape a single ` character inside predicate expression. Predicate expression should be always put before the index, but never after it.


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
`curl -X GET $JSON_HEADER $DEVICE_URL/session/$SESSION_ID/alert/text`
### Accept alert
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/alert/accept`
### Dismiss alert
`curl -X POST $JSON_HEADER -d "" $DEVICE_URL/session/$SESSION_ID/alert/dismiss`

# Touch ID
### Match TouchID
`curl -X POST $JSON_HEADER -d "{\"match\":1}" $DEVICE_URL/session/$SESSION_ID/wda/touch_id`
### Do not match TouchID
`curl -X POST $JSON_HEADER -d "{\"match\":0}" $DEVICE_URL/session/$SESSION_ID/wda/touch_id`