# ComputerWindow
*This module inherits from [ComputerObjectBase](./ComputerObjectBase.md).*

A panel created as a child of another panel which can be parent to any type of object.

## Tweak data values
```lua
{
    children = {},
    background_color = Color()
}
```
* `children`: A table containing module instances (created by running their `new(tweak_data)` method and providing a tweak table, see [ComputerObjectBase](./ComputerObjectBase.md)).
* `background_color`: The background color for the window. 

## Setup
### `ComputerWindow:init(tweak_data)`
Inserts a *ComputerRect* `drag_hitbox` and a *ComputerBitmap* `close_button` object to `self._tweak_data.children`. Also adds three events to the window: `open`, `close` and `attention`.

For information on setting up the tweak data, see [ComputerObjectBase](./ComputerObjectBase.md).
### `ComputerWindow:create(parent)`
Creates a *Panel* instance as a child of `parent` through the `Panel:panel(config)` Diesel method and passes `self._tweak_data.config` as the first argument. Afterwards, a modified HUDBGBox is created as the base for the contents and then the `create()` method is called on every child object, with their parent being the window panel.
## Events 
### `ComputerWindow:trigger_event(event_name, ...)`
Triggers the corresponding event on both itself and appropiate children following set filters. For more information, see [Events](../../events.md).
### `ComputerWindow:update(t, dt)`
Updates every frame and calls every child's `update()` method too.
## Values
### `ComputerWindow:get_value(key)`
Returns the requested key's value.
### `ComputerWindow:set_value(key, value)`
Sets a key to a certain value, then returns it.
## Other
### `ComputerWindow:is_active_window()`
Returns whether the current window object stack's top object is the instance's own object or not. If the window were a child of another, it would compare the currently active window to its parent.
### `ComputerWindow:is_visible(x, y)`
Returns whether the window is visible at position `(x, y)` or not, that is, it is not obscured by other windows.
### `ComputerWindow:mouse_variant(x, y)`
Returns the mouse variant specified for an object that contains position `(x, y)`. Defaults to "arrow".
### `ComputerWindow:is_open()`
Returns whether the window is open or not.

## Note on layers
You are free to use layers in your windows however you like. *ComputerGui* will automatically calculate the layers you are using in a particular window and take those into account when managing multiple windows at once to avoid clipping.