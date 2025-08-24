# ComputerObjectBase
Shared base class that any object module requires in order to function. Overriding methods from this class is possible as with any other PAYDAY 2 class; use `{Module name}.super.{ComputerObjectBase method}(self, ...)` to refer back to this class.
## Tweak data values
*Default values:*
```lua
{
    config = {},
    events = {},
    mouse_variant = "arrow",
    update_enabled = true
}
```
* `config`: A table containing the values that will be passed to Diesel to create interface objects. A malformed configuration table will crash the game with an *access violation*. You can use computed properties here to inherit config values from parent objects (see below).
 to be contained in *ComputerWindow* instances.
* `events`: The events table. See [Events](../../events.md).
* `mouse_variant`: Determines what texture rect the mouse texture should have when over the object.  Possible values are `arrow`, `link`, `hand` and `grab`; you can define more at `ComputerGui.mouse_variants`.
* `update_enabled`: Determines if the `update()` method should be ran every frame. If the method doesn't exist, the update will be ignored for that object.

*ComputerWindow* instances have some more entries in addition to the previous values. See their [tweak data configuration guide](./ComputerWindow.md).

## Methods
### `ComputerObjectBase:create()`
Adds a reference to its parent and the extension instance, then computes `config` properties with `self:compute_properties()`. Modules **need** to refer to this method when running their `create()` method before doing any additional creation.
### `ComputerObjectBase:post_create()`
Sets up the events after all the objects are created for proper callbacks.
### `ComputerObjectBase:setup_events()`
Sets up possible events of type `callback` or `spawn`. 
### `ComputerObjectBase:compute_properties()`
Runs function-type properties inside `config` by passing `self` as the first argument and sets those properties to the functions' return values. This can be useful to inherit parent object values. An example of a computed property would be:
```lua
w = function(self)
    return self._parent._tweak_data.config.w - 35
end
```
### `ComputerObjectBase:trigger_event(event_name, ...)`
Triggers the corresponding event on itself and passes any extra arguments to the corresponding handler.
### `ComputerObjectBase:set_update_enabled(enabled)`
Toggle the execution of the object's `update()` method on every frame.
### `ComputerObjectBase:update_enabled()`
Returns whether this object's update is enabled or not.
### `ComputerObjectBase:is_visible(x, y)`
Returns whether the object is visible at position `(x, y)` or not, that is, it is not obscured by windows and it itself is visible (through `Object:visible()`).
### `ComputerObjectBase:mouse_variant()`
Returns the provided `mouse_variant`.
### `ComputerObjectBase:object()`
Returns the Diesel object.
### `ComputerObjectBase:children()`
Returns the instance's children instances.
### `ComputerObjectBase:parent()`
Returns the instance's parent object.
## Values
### `self._parent`
Returns the parent *ComputerWindow*.