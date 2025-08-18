# Introduction
The ComputerGui class is a custom-made unit extension that adds an interactable screen (through mouse input) with window management logic to a select object inside an unit's `.model` file. It is designed to be highly customizable through Lua configuration, allowing you to make your own 2D object classes to later reuse in many different windows.

## Installation
1. Download the extension files and extract it to your map's root folder.
2. Add the following XML to your `main.xml` file, outside the `<level>` tag:
```xml
<AddFiles directory="assets">
    <!-- ComputerGui: window corners (can't rotate vanilla texture or else they escape monitor, Diesel limitation) -->
    <dds path="guis/textures/pd2/hud_corner_down_left"/>
    <dds path="guis/textures/pd2/hud_corner_down_right"/>
    <dds path="guis/textures/pd2/hud_corner_top_right"/>
</AddFiles>
<Hooks directory="hooks">
    <!-- ComputerGui -->
    <hook file="UnitNetworkHandler.lua" source_file="lib/network/handlers/unitnetworkhandler"/>
    <hook file="FPCameraPlayerBase.lua" source_file="lib/units/cameras/fpcameraplayerbase"/>
</Hooks>
<Elements directory="elements">
    <!-- ComputerGui -->
    <element name="ComputerSetValue"/>
    <element name="ComputerValueFilter"/>
</Elements>
```
3. Add the following XML to your `main.xml` file, inside the `<level>` tag:
```xml
<classes directory="classes">
    <class file="ComputerGui/ComputerGui.lua"/>
</classes>
```
You're now all set to use the extension.

## Unit setup
See [this page](https://wiki.modworkshop.net/books/payday-2-mod-creation/page/unit-file) for reference on `.unit` files. For this extension, the bare minimum that needs to be included in your `.unit` file's extensions is:
```xml
<extension name="damage" class="UnitDamage"/>
<extension name="computer_gui" class="ComputerGui">
    <var name="gui_object" value="timer_gui"/>
    <var name="camera_object" value="camera_object_name"/>
    <var name="tweak_data" value="tweak_data_name"/>
</extension>
```
* `UnitDamage`: A base game extension which is required for sequence managers.
* `gui_object`: Where the graphical interface will be created and displayed. This is the name of an object inside the model, which should be a plane (curved surfaces not tested, but would probably not work).
* `camera_object`: An empty where the camera will be locked when entering the interaction state.
* `tweak_data`: The `tweak_data.computer_gui` entry containing your workspace and application definitions. More information on this on the next section.

To actually start-up the screen, you'll also need some sort of interaction extension in your unit. You can find plenty of them in the base game. By default, they look for the `interact` sequence in your unit's [sequence manager](https://wiki.modworkshop.net/books/payday-2-mod-creation/page/sequence-manager-xml). This sequence should include a reference to the extension's `start` method:
```xml
<sequence editable_state="false" name="'interact'" triggable="false">
    <function extension="'computer_gui'" function="'start'"/>
</sequence>
```

## Tweak data configuration
Each map project you create needs its own tweak data definition in order to avoid conflicts with other maps making use of this extension. `ComputerGui.TWEAK_DATA_FILE` indicates the file inside your map where you edit the tweak table; you can change the path however you prefer. The extension will load this file with `dofile()` on initialization.

An empty configuration file, with no applications or workspaces defined, will resemble the following:
```lua
local MODULE_DIRECTORY = BeardLib.current_level._mod.ModPath .. "classes/ComputerGui/modules/"
local REQUIRED_MODULES = {
    "base/ComputerObjectBase.lua",
    "base/ComputerWindow.lua",
	"base/ComputerBitmap.lua",
    "base/ComputerText.lua",
    "base/ComputerRect.lua"
}
local modules = {}

for _, module in pairs(REQUIRED_MODULES) do
	dofile(MODULE_DIRECTORY .. module)
end
for _, module in pairs(modules) do
	dofile(MODULE_DIRECTORY .. module)
end

tweak_data.computer_gui = {}
```
* `MODULE_DIRECTORY`: The directory inside your map where module files are to be loaded from.
* `REQUIRED_MODULES`: Absolute dependencies *ComputerGui* needs in order to work.
* `modules`: A table containing module files, inside which, you define classes based on [ComputerObjectBase](modules/classes/ComputerObjectBase.md). ComputerGui comes with some extra modules as example material; feel free to modify them.
* `dofile(BeardLib...`: Module loader, which first loads *ComputerObjectBase* (required) and then any other modules specified in `modules`. 
* `tweak_data.computer_gui`: Tweak data table the extension will refer to when loading your configuration.

A very basic GUI definition, with no events, which adds a single application which, when opened, pops-up an "Access Denied" window:
```lua
tweak_data.computer_gui = {
    example = {
        workspace = {
            background_texture = "guis/textures/computergui/backgrounds/example",
        },
        applications = {
            {
                name = "computer_gui_app_example",
                icon = "guis/textures/computergui/backgrounds/application_icon_example",
                window = {
                    config = {
                        halign = "grow",
                        valign = "grow",
                        w = 340,
                        h = 84
                    },
                    background_color = Color.black,
                    children = {
                        ComputerBitmap:new({
                            config = {
                                name = "icon",
                                texture = "guis/textures/pd2/feature_crimenet_heat",
                                w = 64,
                                h = 64,
                                x = 10,
                                y = 10
                            },
                            events = {}
                        }),
                        ComputerText:new({
                            config = {
                                name = "text",
                                text = "Access denied.",
                                font = "fonts/font_medium_noshadow_mf",
                                render_template = "Text",
                                font_size = 40,
                                color = Color.white,
                                x = 85,
                                vertical = "center"
                            },
                            events = {}
                        })
                    }
                }
            }
        }
    }
}
```
See [ComputerObjectBase](./modules/classes/ComputerObjectBase.md) for a detailed rundown on how to write tweak data for modules.

If you plan on reusing the same panel for multiple applications, it is recommended to create a "preset" table for its configuration and then use the game's `deep_clone(table)` global method to use it to define the `window` value.
### Workspace values
```lua
workspace = {
    background_texture = "guis/textures/computergui/backgrounds/example",
    start_post_event = {
        sound_event_id = "highlight",
        clbk = "clbk_highlight_sound_end",
        flags = {
            "end_of_event"
        }
    }
}
```
* **`background_texture`**: The texture to use for the background of the entire interface.
* `start_post_event`: An event to play when the interface starts up. Refer to [Events/Playing Sounds](events.md)

**Bolded values** are required and will cause a crash if missing.
### Application values
```lua
applications = {
    {
        name = "app_name",
        icon = "path/to/icon/texture",
        window = {...}
    },
    {
        name = "app_two_name",
        icon = "path/to/icon/texture_two",
        window = {...}
    }
}
```
* `applications`: An array containing applications. For each item:
    * **`name`**: The localized name of the application which will be written alongside its desktop icon.
    * **`icon`**: The texture to use for the icon of the application.
    * **`window`**: Tweak data for creating a [ComputerWindow](modules/classes/ComputerWindow.md) object which will be displayed when the player clicks on the application icon.

**Bolded values** are required and will cause a crash if missing.
### Note on using textures
Base game textures load as you use them. For custom textures, you will need to add an [AddFiles](https://luffyyy.gitbook.io/beardlib/modules/addfilesmodule) module to your `main.xml` file.