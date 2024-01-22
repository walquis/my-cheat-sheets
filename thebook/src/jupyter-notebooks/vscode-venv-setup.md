# Setting up a Python venv in VSCode for your notebook

If a `.venv` directory exists next to a Jupyter notebook, VSCode will notice it.  However, that's not quite enough; VSCode also needs to recognize that virtual environment as a Jupyter kernel.  This is done by installing `ipykernel` (the wrapper around IPython that enables using IPython as a kernel) in that virtual env.

Create a `.venv` virtual environment for VSCode and identify it as a Jupyter kernel:
```
python3 -m venv .venv
. .venv/bin/activate
pip install ipykernel
```

In VSCode, open the folder containing your Jupyter notebook(s).  NOTE: Don't just open a notebook without opening the containing folder; VSCode will NOT assume that you mean "use the folder with this notebook", and will get confused if no folder is open.

When you open the folder, and then the notebook in VSCode, VSCode will flash a message `detecting kernels...`.  This is a good sign.

In VSCode, Cmd-Shift-P, and search for "kernel".  Choose "Kernel: Select Notebook Kernel". Now that `ipykernel` is installed, the `.venv` containing it will show up as a kernel;  choose that.

At this point, whatever you `pip install` into that activated `.venv` environment should be visible for import with code in your Jupyter notebook. E.g....
```
import pandas as pd
```

