This article contains several practical suggestions about how to create effective element lookup queries using location strategies supported by WebDriverAgent. All examples are written using a pseudo-language.

## Select The Most Effective Lookup Strategy

```
By.xpath('//XCUIElementTypeTable[@name="table"]') -> By.accessibilityId('table')
By.xpath('//XCUIElementTypeTable[@name="table"]') -> By.predicate('type == "XCUIElementTypeTable" AND name == "table"')
By.xpath('//XCUIElementTypeTable[@name="table"]/XCUIElementTypeCell[@visible="true"]') -> By.classChain('**/XCUIElementTypeTable[`name == "table"`]/XCUIElementTypeCell[`visible == 1`]')
```

This is the list of available location strategies sorted by their performance (the first one is the fastest one):

1. Class Name
1. Accessibility Id
1. Link Text
1. Predicate
1. Class Chain
1. XPath

Always try to use the strategy, which is closer to the top of this list. XPath strategy can sometimes be very slow, because it is not natively supported by XCTest and WDA requires additional efforts to implement it, which seriously affects lookup time. Use XPath locators only if there is no other alternative, for example if some special functions or axes are used in the query.

## Limit The Search Scope

```
dstElement =  driver.findElement(By.xpath('//XCUIElementTypeTable[@name="table"]//XCUIElementTypeTextField[@visible="true"]')) -> tableEl = driver.findElement(By.accessibilityId('table')); dstElement = tableEl.findElement(By.predicate('type == "XCUIElementTypeTextField" AND visible == 1'))
```

The more UI elements in the current search scope you have the longer lookup times you get. By default the search scope is the source of the whole page (expressions like `driver.findElement(s)`). By limiting it to the source of the particular element (e. g. `tableEl.findElement(s)`) one can decently optimize lookup performance especially if multiple lookups are going to be executed there.
This strategy can also help to avoid XPath locators or improve lookup times for them if they are executed in limited scope.

## Do Not Search For Multiple Element If Only One Element Is Expected To Be Matched

```
dstElement = driver.findElements(By.xpath('//XCUIElementTypeTable'))[0] -> dstElement = driver.findElement(By.className('XCUIElementTypeTable'))
```

Usually `findElements` takes more time to complete than `findElement`, because it is not necessary to scan through the whole source to find all matches, but rather return after the first one is detected.

## Avoid Generic Matchers

```
By.xpath('//*[@*="1"]/parent::*') -> By.xpath('//XCUIElementTypeButton[@name="1"]/parent::XCUIElementTypeCell')
```

Generic matchers like asterisk `*` in combination with `findElements` call may require to scan though all UI element attributes, which is quite ineffective from performance perspective. 