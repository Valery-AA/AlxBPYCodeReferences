# Editing Keymaps

### Function To Handle Editing

The Concept is simple and so is the process, <br>
here is a ready made function to handle it, directly copying it or using it to learn is to the reader discretion

```
def AlxEditKeymaps(KeyconfigSource="Blender", ConfigSpaceName="", ItemidName="", OperatorID="", MapType="", Key="", UseShift=False, UseCtrl=False, UseAlt=False, TriggerType="PRESS", Active=False):
    """KeyconfigSource : [Blender, Blender addon, Blender user]"""
    if (KeyconfigSource != "") and (ConfigSpaceName != ""):
        WindowManager = bpy.context.window_manager
        DefaultKeyConfigs = WindowManager.keyconfigs[KeyconfigSource]
        DefaultKeyMaps = DefaultKeyConfigs.keymaps

        try:
            for KeymapItem in DefaultKeyMaps[ConfigSpaceName].keymap_items:
                if (OperatorID == ""):
                    if (KeymapItem is not None) and (KeymapItem.idname == ItemidName):
                        if (MapType != ""):
                            KeymapItem.map_type = MapType
                        if (Key != ""):
                            KeymapItem.type = Key
                        KeymapItem.shift = UseShift
                        KeymapItem.ctrl = UseCtrl
                        KeymapItem.alt = UseAlt
                        KeymapItem.value = TriggerType
                        KeymapItem.active = Active

                if (OperatorID != ""):
                    if (KeymapItem is not None) and (KeymapItem.properties is not None) and (KeymapItem.idname == ItemidName) and (KeymapItem.properties.name == OperatorID):
                        if (MapType != ""):
                            KeymapItem.map_type = MapType
                        if (Key != ""):
                            KeymapItem.type = Key
                        KeymapItem.shift = UseShift
                        KeymapItem.ctrl = UseCtrl
                        KeymapItem.alt = UseAlt
                        KeymapItem.value = TriggerType
                        KeymapItem.active = Active
        except:
            print("KEYMAP FAILED: " + ItemidName + " " + OperatorID)
```

####  How does it work and why?
To access an existing keybind you need:
- The source of where the keybinds are stored i.e. Blender Default, Addon or User created
- The name of the space in the keymapconfigs not the keymap space_type, <br> this can be viewed by simply opening the keymap preferences, it will be the earliest relative parent dropdown which will be the name used in the keymapconfigs
- The bl_idname or equivalent used in the preferences for example "object.mode_set" or the bl_idname in case is an addon keymap\

##### Keypoints
- The Editing has to be done inside a try/except condition
```
try:
    Edit The Keymaps Here
except:
    both "pass" or a specific print to know what went from can be used
```
the reason is, if the keymap search fails (no keymaps were found) it will return an error and stop the script meaning anything after wont run if in a function or even worst in case of single file addons, it will stop the rest of the file from running
<br>
- The Search is better done through a __keymapitems__ loop
```
for KeymapItem in DefaultKeyMaps[ConfigSpaceName].keymap_items:
                    if (KeymapItem is not None) and (KeymapItem.idname == ItemidName):
                        change the keymap
```
This is not recommended but i find it to be the best solution, the methods that are normally recommended do not work with __id_names__ that are made to call __properties<span>.name__ id_names, for example __wm.call_menu__ will not be matched properly as there is no subscript to access in the search the __properties.name__ without a for loop and would result in the wrong keymap being accessed
<br>
- The Keymap will retain any of it's previous options if they are not altered
  meaning if the editing is not done with the "stock" function above the changes can be done singularly without needing to re-input rest of the values that the keymap had before
<br>
- Regardless of method used always remember to check for __keymap.properties is None__ as there are some keymaps that will fail if searched due to variants that can posses a property
example:
```
if (KeymapItem is not None) and (KeymapItem.idname == ItemidName) and (KeymapItem.properties is None)
```