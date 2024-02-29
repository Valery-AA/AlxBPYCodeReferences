# Multi-File Reloading
### Reload a .py file

This allows a single file in the folder to be reloaded along with the main __init__.py file
the file does not have to be a module as python files will be interpreted as modules, this should be used for each file and not in bulk under the same if statement _(check [section](https://github.com/Valery-AA/AlxBPYCodeReferences/blob/main/Addon%20Register%20And%20Reload/Reloading%20Multi-File%20Addons.md#the-wrong-way-of-reloading))_
```py

if ("PythonFileName" not in locals()):
    from ParentFolderName import PythonFileName
else:
    import importlib
    PythonFileName = importlib.reload(PythonFileName)

```

<h3 id="the-wrong-way-of-reloading">The Wrong Way of Reloading</h3>
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
why is there issues with this method?
each file now depends on whether or not bpy is in locals, but what is locals?
a dictionary of the available function/files/modules within that python context meaning that checking in bulk like this doesn't account for the state of the singular files, as bpy could be in locals but file_three might not be imported yet, so issues arise with files going out of date.
more specifically the source of the issue arises not only from the **if statement** but also from the use of the `importlib.reload()` function **as the function it self does not update the locals() dictionary**, but rather creates an object of the file that was passed to it so that the locals() entry of the file can be updated to it with `PythonFileName = importlib.reload(PythonFileName)`
