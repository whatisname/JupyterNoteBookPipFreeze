# JupyterNoteBookPipFreeze
Equivelent way of doing pip freeze in Jupyter Notebook. If you don;t want to freeze the entire Jupyter server requirements into your project, you can use the following method to only freeze related environments into your doc.

Credit to Alex P. Miller in this [stack overflow post](https://stackoverflow.com/a/49199019).

# usage
Copy following code into your working notebook then run. "requirements.txt" will be written into the same directory.

```python
# equivelent of "pip freeze > requirements.txt" without freezing entire environment
# Reference: https://stackoverflow.com/a/49199019

import pkg_resources
import types

def get_imports():
    for name, val in globals().items():
        if isinstance(val, types.ModuleType):
            # Split ensures you get root package, 
            # not just imported function
            name = val.__name__.split(".")[0]

        elif isinstance(val, type):
            name = val.__module__.split(".")[0]

        # Some packages are weird and have different
        # imported names vs. system/pip names. Unfortunately,
        # there is no systematic way to get pip names from
        # a package's imported name. You'll have to add
        # exceptions to this list manually!
        poorly_named_packages = {
            "PIL": "Pillow",
            "sklearn": "scikit-learn"
        }
        if name in poorly_named_packages.keys():
            name = poorly_named_packages[name]

        yield name

imports = list(set(get_imports()))

# The only way I found to get the version of the root package
# from only the name of the package is to cross-check the names 
# of installed packages vs. imported packages
requirements = []
for m in pkg_resources.working_set:
    if m.project_name in imports and m.project_name!="pip":
        requirements.append((m.project_name, m.version))

with open('requirements.txt', 'w') as f:
    for r in requirements:
        print("{}=={}".format(*r))
        f.write("{}=={}\n".format(*r))
    
```

# Problems
ONLY support freezing one notebook into, not entire project.

