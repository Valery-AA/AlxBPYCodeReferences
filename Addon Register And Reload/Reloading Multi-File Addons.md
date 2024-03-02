# Multi-File Reloading
## Reload a .py file

Any additional `.py` file within the addon folder that is not the `__init__` file requires to be manually reloaded,
if the files were to not be reloaded before reloading scripts, none of the changes made within those files will be reflected until the addon will be installed again

This allows a single file in the folder to be reloaded along with the main `__init__` file <br>
the file does not have to be a module as python files will be interpreted as modules by `importlib`, 
### Warning:
This should be used for each file and not in bulk under the same if statement (check [section](https://github.com/Valery-AA/AlxBPYCodeReferences/blob/main/Addon%20Register%20And%20Reload/Reloading%20Multi-File%20Addons.md#the-wrong-way-of-reloading))
```py

if ("PythonFileName" not in locals()):
    from ParentFolderName import PythonFileName
else:
    import importlib
    PythonFileName = importlib.reload(PythonFileName)

```
## Advanved Bulk Reloading of .py files

Sometimes addons grow in size past the read-able state, repeating the code above for even 5-10 times would get un-managable pretty quickly
for automating such task this method can be transformed into:
- retirve all the files within the addon folder
- repeat the above task for all available modules

```py
import os
import importlib

import bpy

addon_files = [file_name[0:-3] for file_name in os.listdir(__path__[0]) if (file_name[0:3] == "unique_addon_prefix") and (file_name.endswith(".py"))]

for file in addon_files:
    if (file not in locals()):
        import_line = f"from . import {file}"
        exec(import_line)
    else:
        reload_line = f"{file} = importlib.reload({file})"
        exec(reload_line)
```
with `os.listdir(__path__[0])` we obtain from `os.listdir` a list of everything within the module path which in this case, <br>
only the `__init__` file has a `__path__` variable defined and it will result to be the location of the addon folder, <br>
checks are required to ensure the only retrieved items are `.py` files <br>
### **Good practice:** <br>
the addon file names should start with a prefix unique to the addon to specify more in depth the correct files to be included <br>
### **Warning:** 
`whether a suffix is present or not, ensure the __init__ file is not within this list reguardless` 
<h2 id="the-wrong-way-of-reloading">The Wrong Way of Reloading</h2>
common methods will usually look like this:

```py
if ("bpy" in locals()):
    import importlib
    importlib.reload(file_one)
    importlib.reload(file_two)
    importlib.reload(file_three)

else:
    from . import file_one
    from . import file_two
    from . import file_three
```
**why is there issues with this method?** <br>
each file now depends on whether or not `bpy` is in `locals`, but what is `locals`? <br>
a `dictionary` of the available function/files/modules within that python context, <br>
checking in bulk does not account for the state of the singular files, <br>
`bpy` could be in `locals` but `file_three` might not be imported yet, so issues arise as files could be skipped from being imported or reloaded during the development process

more specifically the source of the issue arises not only from the `if statement` but also from the use of the `importlib.reload()` function as the function it self does not update the `locals()` dictionary, <br>
rather it creates an object of the file that was passed to it so that the `locals()` entry of the file can be updated to it by assigning that entry a new value <br> 
```py
PythonFileName = importlib.reload(PythonFileName)
```
