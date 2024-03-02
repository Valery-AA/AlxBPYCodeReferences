TEMP

```py
import bpy

# Class Import/Reload
import os
import importlib

addon_files = [file_name[0:-3] for file_name in os.listdir(__path__[0]) if (file_name[0:3] == "Alx") and (file_name.endswith(".py"))]

for file in addon_files:
    if (file not in locals()):
        import_line = f"from . import {file}"
        exec(import_line)
    else:
        reload_line = f"{file} = importlib.reload({file})"
        exec(reload_line)

# Class Queue
import inspect

bpy_class_object_list = tuple(bpy_class[1] for bpy_class in inspect.getmembers(bpy.types, inspect.isclass))
alx_class_object_list = tuple(alx_class[1] for file in addon_files for alx_class in inspect.getmembers(eval(file), inspect.isclass) if issubclass(alx_class[1], bpy_class_object_list) and (not issubclass(alx_class[1], bpy.types.WorkSpaceTool)))

AlxClassQueue = alx_class_object_list

def register():
    for AlxClass in AlxClassQueue:
        try:
            bpy.utils.register_class(AlxClass)
        except:
            bpy.utils.unregister_class(AlxClass)
            bpy.utils.register_class(AlxClass)

def unregister():
    for AlxClass in AlxClassQueue:
        bpy.utils.unregister_class(AlxClass)
```