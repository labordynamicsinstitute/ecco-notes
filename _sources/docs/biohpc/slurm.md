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

(slurm)=
# Job scheduler on BioHPC

A SLURM cluster `cbsueccosl01` is maintained by Lars on behalf of Econ, on several nodes. Some are dedicated to the SLURM scheduler, others "borrowed"; the latter might not always be available. There are between 48 and 144 "slots" (cpus) available for compute jobs. The following table shows the allocated nodes. This list is static: to see at any point in time the available nodes, type

```bash
sinfo
```
in a terminal window on the head node, to obtain a result such as

```bash
$ sinfo
CLUSTER: cbsueccosl01
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
regular*     up   infinite      1  drain* cbsueccosl03
regular*     up   infinite      6   idle cbsuecco[07,09-10,12],cbsueccosl[01,04]
```

which shows that currently, 6 nodes are available for jobs, one node is not accepting jobs (but running any jobs to completion). 


```{code-cell} ipython3
:tags: ["remove-input","full-width"]
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
# limit to flex and slurm nodes
nodes = nodes[nodes['allocation'].str.contains('flex|slurm',na=False)]
# compute total cores as cores * CPUs
nodes['cores'] = nodes['cores per CPU'] * nodes['CPUs']

# reorder columns
# override the order of columns - this may need to be adjusted if the column names change
columns = ['Nodename', 'allocation', 'cpu benchmark (single thread)', 'cores','RAM',  'local storage in TB', 'model','cores per CPU', 'CPUs', 'vintage' ]

# Reorder the columns
nodes = nodes[columns]

#table = nodes.to_html(index=False, classes='table table-striped table-bordered table-sm', escape=False, render_links=True)
# Render the HTML table in Jupyter Notebook
#HTML(table)
show(nodes, lengthMenu=[15, 25, 50], layout={"topStart": "search"}, classes="display compact")

```


## Who can use

Everybody in the ECCO group can submit jobs.
