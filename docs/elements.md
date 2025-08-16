# Elements
*ComputerGui* comes packed with two custom elements to use in your mission logic.

## ComputerSetValue
Sets the value to a certain key inside an unit's *ComputerGui* extension.

* Unit: the unit with the extension.
* Key: the key for the value.
* Value: the **string value** to set the key to.

Combine the use of this element with [custom modules](./modules/making.md) to make your interface change as the mission progresses. 

## ComputerValueFilter
Filters on-executed element list execution based on the equivalence of a certain key to a value in an unit with the *ComputerGui* extension.

* Unit: the unit with the extension.
* Key: the key for the value.
* Value: the **string value** to filter against.

Combine the use of this element with [custom modules](./modules/making.md) to make your mission adapt to the interface's state.