## Purpose

This query type is WebDriverAgent's layer over native XCTest lookup function calls defined in [XCUIElementQuery](https://developer.apple.com/documentation/xctest/xcuielementquery?language=objc) class with some additional features, like intermediate chain items indexing and tail-based indexing. Search by direct children and descendant elements is supported. 

## Direct search requests

* ```XCUIElementTypeWindow/XCUIElementTypeButton[3]``` - select the third child button of the first child window element
* ```XCUIElementTypeWindow``` - select all the children windows
* ```XCUIElementTypeWindow[2]``` - select the second child window in the hierarchy. Indexing starts at 1
* ```XCUIElementTypeWindow/XCUIElementTypeAny[3]``` - select the third child (of any type) of the first child window
* ```XCUIElementTypeWindow[2]/XCUIElementTypeAny``` - select all the children of the second child window
* ```XCUIElementTypeWindow[2]/XCUIElementTypeAny[-2]``` - select the second last child of the second child window
* One may use '*' (star) character to substitute the universal 'XCUIElementTypeAny' class name
* ```XCUIElementTypeWindow[`name CONTAINS[cd] "blabla"`]``` - select all windows, where name attribute starts with "blabla" or "BlAbla"
* ```XCUIElementTypeWindow[`label BEGINSWITH "blabla"`][-1]``` - select the last window, where label text begins with "blabla"
* ```XCUIElementTypeWindow/XCUIElementTypeAny[`value == "bla1" OR label == "bla2"`]``` - select all children of the first window, where value is "bla1" or label is "bla2"
* ```XCUIElementTypeWindow[`name == "you're the winner"`]/XCUIElementTypeAny[`visible == 1`]``` - select all visible children of the first window named "you're the winner"
* ```XCUIElementTypeWindow/XCUIElementTypeTable/XCUIElementTypeCell[`visible == 1`][$type == XCUIElementTypeImage AND name == 'bla'$]/XCUIElementTypeTextField``` - select a text field, which is a direct child of a visible table cell, which has at least one descendant image with identifier 'bla'

## Indirect descendant search requests

* ```**/XCUIElementTypeCell[`name BEGINSWITH "A"`][-1]/XCUIElementTypeButton[10]``` - select the 10-th child button of the very last cell in the tree, whose name starts with 'A'.
* ```**/XCUIElementTypeCell[`name BEGINSWITH "B"`]``` - select all cells in the tree, where name starts with 'B'
* ```**/XCUIElementTypeCell[`name BEGINSWITH "C"`]/XCUIElementTypeButton[10]``` - select the 10-th child button of the first cell in the tree, whose name starts with 'C' and which has at least ten direct children of type XCUIElementTypeButton.
* ```**/XCUIElementTypeCell[`name BEGINSWITH "D"`]/**/XCUIElementTypeButton``` - select the all descendant buttons of the first cell in the tree, whose name starts with 'D'

## General rules and recommendations

Predicate string should be always enclosed into \` or $ characters inside square brackets. Use \`\` or $$ to escape a single \` or $ character inside predicate expression.

Single backtick means the predicate expression is applied to the current children. It is the direct alternative of [matchingPredicate:](https://developer.apple.com/documentation/xctest/xcuielementquery/1500471-matchingpredicate?language=objc) query selector.

Single dollar sign means the predicate expression is applied to all the descendants of the current element(s). It is the direct alternative of [containingPredicate:](https://developer.apple.com/documentation/xctest/xcuielementquery/1500956-containingpredicate?language=objc) query selector.

Predicate expression should be always put before the index, but never after it. All predicate expressions are executed in the same exact order, which is set in the chain query.

It is not recommended to set explicit indexes for intermediate chain elements, because it slows down the lookup speed.

Double star and slash `**/` defines the next following item as the descendant of the previous chain item, rather than its child.

The query result is similar to what XCTest's _children..._ and _descendants..._ selector calls of XCUIElement class instances produce when combined into a chain.