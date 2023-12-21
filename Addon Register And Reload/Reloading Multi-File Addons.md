# Multi-File Reloading
### Reload a .py file

This allows a single file in the folder to be reloaded along with the main __init__.py file
the file does not have to be a module a plain python file will work
```

if ("PythonFileName" not in locals()):
    from ParentFolderName import PythonFileName
else:
    import importlib
    PythonFileName = importlib.reload(PythonFileName)

```