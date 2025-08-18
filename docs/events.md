# Events
To make stuff happen as the user interacts with the interface, you will need to set up events. These are added as a table inside the initializer data. See the following configuration snippet:
```lua
ComputerBitmap:new({
    config = {...},
    events = {
        mouse_enter = {
            type = "callback".
            enabled = true,
            event = "clbk_highlight_add",
            post_event = {
                sound_event_id = "highlight",
                clbk = "clbk_highlight_sound_end",
                flags = {
                    "end_of_event"
                }
            }
        },
        mouse_exit = {
            type = "callback".
            enabled = true,
            event = "clbk_highlight_remove"
        },
        mouse_pressed = {
            type = "func",
            enabled = true,
            event = function(window, ...)
                log("Mouse was pressed down on object: " .. self:object():name() or "bitmap object")
            end
        },
        mouse_released = {
            type = "spawn",
            enabled = true,
            event = ComputerWindow:new(...)
        }
    },
    mouse_variant = "link"
})
```
This would create a bitmap object which is highly reactive to mouse interactions. The `mouse_variant` value is used to determine what texture rect the mouse texture should have when over the object.  Possible values are `arrow` (default), `link`, `hand` and `grab`; you can define more at `ComputerGui.mouse_variants`.

Event names are used as the key for a table where you actually define it. See below for details on possible `type` values and event names. Events are triggered on the *ComputerWindow* class first by the unit extension. The *ComputerWindow* class will then automatically pass the event to child objects. Events may have certain trigger filters; for more information on these, check out the table below or `ComputerWindow:trigger_event()`. 

You can make your own events by simply adding their name to the configuration and calling them by using the aforementioned method on the corresponding window; say, when a timer finishes, a video plays, *etc*. 

## Event list

| Event | Triggered on | Arguments |
| ----------- | ----------- | -------- |
| open | Opening the application by clicking on its desktop icon | - |
| close | Closing the application by clicking on its close button | - |
| attention | Trying to open the application while already opened by clicking on its desktop icon (doing this will also trigger `gained_focus` if its trigger conditions apply) | - |
| mouse_enter | Moving the mouse cursor inside an object while visible | mouse_x, mouse_y |
| mouse_exit | Moving the mouse cursor outside an object or over another object over the initial one, having previously placed the cursor inside the latter | mouse_x, mouse_y |
| mouse_pressed | Pressing down the mouse cursor's left button inside an object while visible | button, mouse_x, mouse_y |
| mouse_released | Releasing the mouse cursor's left button inside an object while visible, having previously placed the cursor inside the object while visible | button, mouse_x, mouse_y |
| gained_focus | Clicking inside of the application window while being unfocused | - |
| lost_focus | Clicking outside of the application window while being focused | - |

## Event types
* `func`: run a function as if it were a method of the current object (`self` is available for use)
* `callback`: use a callback to the specified method on the object's class
* `spawn`: spawn a new *ComputerWindow* defined through the specified configuration. The new window will lock the parent window until it is closed and its parent will be set to the parent window. A child window can spawn more child windows from itself; the base application window will pass events through all the windows, in order, until the last spawned child window is reached.

## Playing sounds
To play sounds on events, add a `post_event` table with the event ID you want to play (the corresponding soundbank should be loaded if it is a vanilla sound; disregard for custom sounds, more info on event IDs [here](https://modworkshop.net/mod/53326)). 

```lua
post_event = {
    sound_event_id = "highlight",
    clbk = "clbk_highlight_sound_end",
    flags = {
        "end_of_event"
    }
}
```
* `sound_event_id`: The event ID.
* `clbk`: An optional callback function.
* `flags`: A table of "flags" that will run the callback function. These are defined in-engine and are:
    * `end_of_event` 
    * `marker` 
    * `duration`

Diesel will call your callback function with the following arguments:
```lua
callback_func(instance, sound_source, event_type, cookie, label, identifier, position)
```
For further reference, see the [PAYDAY 2 LuaJit repository](https://github.com/steam-test1/Payday-2-LuaJIT-Complete), particularly, `VoiceBriefingManager:_sound_callback()`.