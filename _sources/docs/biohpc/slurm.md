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


```{code-cell} ipython3
:tags: ["remove-input","full-width"]
import os


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
```

(slurm)=
# Job scheduler on BioHPC


A SLURM cluster `cbsueccosl01` is maintained by Lars on behalf of Econ, on several nodes. Some are dedicated to the SLURM scheduler, others "borrowed"; the latter might not always be available. There are between 48 and 144 "slots" (cpus) available for compute jobs (see [Table](fulltable)). 

## Who can use

Everybody in the ECCO group can submit jobs.

## Current load

The most current status (as per the date and time noted) is:

```{code-cell} python3
:tags: ["remove-input","full-width"]


def print_file_as_markdown_code_block(file_path, language=''):
    """
    Read an external file and print its contents as a Markdown fenced code block.
    
    Args:
        file_path (str): Path to the file to be read
        language (str, optional): Language identifier for syntax highlighting
    """
    try:
        with open(file_path, 'r') as file:
            content = file.read()
            #print(f'```{language}')
            print(content)
            #print('```')
    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
    except IOError as e:
        print(f"Error reading file: {e}")

print_file_as_markdown_code_block(os.path.join(project_root,"_data", 'eccoload.txt'))
```




## Manually query the latest availability

To see availability at any point in time, type

```bash
sinfo --cluster cbsueccosl01
```
in a terminal window on the head node,[^quick] to obtain a result such as

[^quick]: See [Quick Start](onetimesetup-slurm) for how to simplify that command.

```bash
$ sinfo --cluster cbsueccosl01
CLUSTER: cbsueccosl01
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
regular*     up   infinite      3    mix cbsuecco[07-08],cbsueccosl03
regular*     up   infinite      1  alloc cbsueccosl04
regular*     up   infinite      2   idle cbsuecco01,cbsueccosl01
```

which shows that currently, 6 nodes are available for jobs, of which 2 are idle, three have some jobs running on them, but can still accept smaller jobs (`mix` means there are free CPUs), and one is completely used (`alloc`).


(fulltable=)
## List of nodes

The following table shows the allocated nodes. Nodes marked `flex` may not be available. Nodes marked `slurm` are always available.


```{code-cell} ipython3
:tags: ["remove-input","full-width"]
from IPython.display import HTML
import pandas as pd
# from jupyter_datatables import init_datatables_mode, render_datatable
import os
from itables import init_notebook_mode, show

init_notebook_mode(all_interactive=True)

# uses the earlier function find_project_root()
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