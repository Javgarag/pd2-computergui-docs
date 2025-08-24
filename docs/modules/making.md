# Making modules
Any given module needs to be based on [ComputerObjectBase](./classes/ComputerObjectBase.md) and must contain the following methods:

## Required methods
### `init(tweak_data, subclass)`
*This method **must call the superclass method of the same name**.*

You can add any tweak data keys you may need to your custom module. Say, a timer length, or the color for a certain bitmap at a certain event trigger. You must still respect the base class' tweak data requirements.
### `create(parent_object, extension, parent)`
*This method **must call the superclass method of the same name.***

This method **must define `self._object` as a Diesel interface object.** This means a panel, a bitmap, a rect, a text, a gradient, a polyline, a video, *etc*. See [engine classes](https://github.com/Krimzin/pd2-class-members) for reference. You can go however crazy you wanna go here. **Do not add children modules (tweak data `children` table) inside your custom module; modules can only be parented to *ComputerWindow* objects.**
## Optional methods
### `update(t, dt)`
For frame-based updating as long as the computer extension is active.

## Helpful stuff
Use the extension's base [events](../events.md) as triggers for mouse clicks, hovers, window opening, *etc*. From there, you can trigger any extra interface event you want to add. 

### Making the interface adapt to mission events
To set the state of the interface with mission logic, see the [custom elements](../elements.md). Read values using the `self.extension.values` table.

### Making the mission respond to interface events
Interact with your mission logic by calling *GlobalEvents* with `managers.mission:call_global_event()`. Then, use [GlobalEventTrigger](https://wiki.modworkshop.net/books/beardlib-editor-reference/page/mission-elements-wip#bkmrk-globaleventtrigger) elements to respond to these events.

### Playing sounds through scripting
Use `ComputerGui:post_event(event, clbk, flags, stop_previous)`. Refer to [Events/Playing Sounds](../events.md) for info on arguments.