---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# General access to Linux computing

You may want to have more reliable (but sometimes slower) compute resources. If Linux is not a constraint (normally, it should not be), consider the Econ nodes at BioHPC. You will not be subject to crowding, and you may have access to larger resources (CPUs, memory) than for the Windows resources. See the available resources further along.

:::{tip}

Default cost is zero if using the "restricted" (ecco) cluster. Additional resources (compute cycles, storage) can be purchased. 

:::

Additional Linux compute resources are available for purchase both via BioHPC and CAC. 

## Available resources

See [https://biohpc.cornell.edu/lab/ecco.htm](https://biohpc.cornell.edu/lab/ecco.htm) for the general overview, and the table below for some more specifics. 
Some of the nodes are a bit old (they will run about as fast as CCSS-RS, but much slower than a recent desktop), but have a ton of memory, and lots of CPUs. For instance, `cbsuecco02` has 1024GB of memory. 

:::{note}

In the tables below, 

- `reservation` means the node is available through the  BioHPC "restricted" reservation system. See [Requesting exclusive access to an entire node](reserving) for details.
- `slurm` means the node is only available via the SLURM job scheduler. See [Job scheduler on BioHPC](slurm) for details.
- `flex` means a node may be available alternately as a `reservation` or a `slurm` node.

:::

```{code-cell} ipython3
:tags: ["remove-input"]
from IPython.display import HTML
import pandas as pd
# from jupyter_datatables import init_datatables_mode, render_datatable
import os
from itables import init_notebook_mode, show

init_notebook_mode(all_interactive=True)


def find_project_root(start_dir='.', file_to_find='favicon.ico'):
    """
    Find the project root directory by searching for a specific file.
    
    Args:
        start_dir (str): The directory to start the search from.
        file_to_find (str): The name of the file to search for.
        
    Returns:
        str: The absolute path of the project root directory.
    """
    current_dir = os.path.abspath(start_dir)
    
    while True:
        # Check if the file exists in the current directory
        if os.path.isfile(os.path.join(current_dir, file_to_find)):
            return current_dir
        
        # Check if we've reached the root directory
        parent_dir = os.path.dirname(current_dir)
        if parent_dir == current_dir:
            raise FileNotFoundError(f"Could not find '{file_to_find}' in the directory tree.")
        
        # Move up one directory
        current_dir = parent_dir

# Example usage
project_root = find_project_root()
#print(f"Project root directory: {project_root}")


nodes = pd.read_csv(os.path.join(project_root,"_data", "ecconodes.csv"))
# compute total cores as cores * CPUs
nodes['cores'] = nodes['cores per CPU'] * nodes['CPUs']
summary_table = nodes[["allocation", "cores", "RAM","local storage in TB"]]
summary = summary_table.groupby('allocation')[['cores', 'RAM',"local storage in TB"]].sum().reset_index()

# Convert the summary DataFrame to an HTML table
sumtable = summary.to_html(index=False, classes='table table-striped table-bordered table-sm')
HTML(sumtable)
```

**Details:**


```{code-cell} ipython3
:tags: ["remove-input","full-width"]

# reorder columns
columns = nodes.columns.tolist()  # Get the list of column names
columns.remove('model')  # Remove column from the list
columns.remove('cpu benchmark (system)')  # Remove column from the list
columns.append('model')  # Append  column to the end of the list
# override the order of columns - this may need to be adjusted if the column names change
columns = ['Nodename', 'allocation', 'cpu benchmark (single thread)', 'cores','RAM',  'local storage in TB', 'model','cores per CPU', 'CPUs', 'vintage' ]

# Reorder the columns
nodes = nodes[columns]

#table = nodes.to_html(index=False, classes='table table-striped table-bordered table-sm', escape=False, render_links=True)
# Render the HTML table in Jupyter Notebook
#HTML(table)
show(nodes, lengthMenu=[15, 25, 50], layout={"topStart": "search"}, classes="display compact")

```

- *local* disk space refers to the `/workdir` temporary workspace. All nodes have access to the shared home directory.


## Why choose `slurm` or `reservation`?

If you only need one CPU, the easiest and fastest is to use the SLURM job scheduler. It can accomodate up to 100 simultaneous (normal-sized) jobs. You are guaranteed at least 1 CPU (or as many as requested), but no more. (This is new, and not yet fully tested.)

If you need multiple CPUs and want to keep it simple, you may be better served by the `reservation` system. Asset owners can reserve a node for up to 7 days, and you are guaranteed all the CPUs and memory on that node. You also may want to use the `reservation` system if you need interactive (graphical) access (via VNC) to a node, the setup is facilitated by the reservation system.

