## Purpose

Predicate queries are natively supported by XCTest and enable quick location of elements based on their attribute values. 

## Syntax

Follow the rules described in [Predicate Format String Syntax](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Predicates/Articles/pSyntax.html) article. 

## Available attributes

WebDriverAgent currently defines the following element attributes:

1. `name` - The actual value of element's accessibility identifier or element's label if the first one is not set
1. `value` - Element's value. It is always of type string (or `nil` if the value is not set) and contains the value of the corresponding element. Depending on the element's type this could be: a text or a placeholder text in the text field; a label of a text label; `'1'` or `'0'` if this is a checkbox or a switch
1. `label` - Element's label value or `nil` if it is not present
1. `rect` - Element's rectangle as a dictionary with the following keys: x, y, width, heigth
1. `type` - Type string. All the possible element types are enumerated on [this](https://developer.apple.com/documentation/xctest/xcuielementtype) XCTest tutorial page.
1. `enabled` - Whether the element is enabled or not (`1`/`0`)
1. `visible` - Whether the element is displayed or not (`1`/`0`)
1. `accessible` - Whether the element is accessible or not (`1`/`0`)
1. `accessibilityContainer` - Whether the element is an accessibility container or not (`1`/`0`)

All these attribute names can be used in predicate queries also with `wd` prefixes, for example `wdName`.

## Examples

`type == 'XCUIElementTypeButton' AND value BEGINSWITH[c] 'bla' AND visible == 1` - find elements of type XCUIElementTypeButton whose value starts with `Bla`/`bla`/`BLA` and which are visible
`type IN {'XCUIElementTypeIcon','XCUIElementTypeImage'} AND visible == 1` - find all visible icons and images
`type == 'XCUIElementTypeCell' AND rect.width > 100` - find all cells whose width is greater than 100
`type == 'XCUIElementTypeCheckBox' AND (visible == 1 OR enabled == 1)` - find all check boxes, which are visible or enabled
