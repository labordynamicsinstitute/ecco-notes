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
import re
import pandas as pd
from datetime import datetime


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

# Load and process nodes data for slot calculation
nodes = pd.read_csv(os.path.join(project_root,"_data", "ecconodes.csv"))
# limit to flex and slurm nodes
nodes = nodes[nodes['allocation'].str.contains('flex|slurm',na=False)]
# compute total cores as cores * CPUs

# Adjust cores calculation based on hyperthreading (HT)
nodes['cores'] = nodes['cores per CPU'] * nodes['CPUs']
nodes.loc[nodes['HT'] == True, 'cores'] *= 2

# Parse eccoload.txt for nodes presently in the cluster
def parse_eccoload(file_path):
    allocated_nodes = []
    try:
        with open(file_path, 'r') as file:
            content = file.read()
            lines = content.splitlines()
            for line in lines:
                if 'cbsuecco' in line:
                    parts = line.split()
                    nodelist = parts[-1]
                    
                    if '[' in nodelist:
                        base_name = nodelist.split('[')[0]
                        ranges_part = nodelist.split('[')[1].rstrip(']')
                        
                        for range_part in ranges_part.split(','):
                            range_part = range_part.rstrip(']')
                            
                            if '-' in range_part:
                                try:
                                    start, end = map(int, range_part.split('-'))
                                    for i in range(start, end + 1):
                                        if i < 10:
                                            allocated_nodes.append(f"{base_name}0{i}")
                                        else:
                                            allocated_nodes.append(f"{base_name}{i}")
                                except ValueError as e:
                                    print(f"Error parsing range: {range_part} - {e}")
                            else:
                                try:
                                    num = int(range_part)
                                    if num < 10:
                                        allocated_nodes.append(f"{base_name}0{num}")
                                    else:
                                        allocated_nodes.append(f"{base_name}{num}")
                                except ValueError:
                                    allocated_nodes.append(f"{base_name}{range_part}")
                    else:
                        allocated_nodes.append(nodelist)
    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
    except IOError as e:
        print(f"Error reading file: {e}")
        
    return allocated_nodes

# Calculate current and total available cores
eccoload_path = os.path.join(project_root, "_data", "eccoload.txt")
allocated_node_names = parse_eccoload(eccoload_path)
allocated_nodes = nodes[nodes['Nodename'].isin(allocated_node_names)]
current_total_cores = allocated_nodes['cores'].sum()
max_possible_cores = nodes['cores'].sum()

# Get current date and time
current_date_time = datetime.now().strftime("%B %d, %Y at %I:%M %p")

#print(f"Project root directory: {project_root}")
```

(slurm)=

# Job scheduler on BioHPC

A SLURM cluster `cbsueccosl01` is maintained by Lars on behalf of Econ, on several nodes. Some are dedicated to the SLURM scheduler, others "borrowed"; the latter might not always be available.

```{code-cell} ipython3
:tags: ["remove-input","full-width"]
from IPython.display import Markdown, display
display(Markdown(f"As of {current_date_time}, there are **{current_total_cores} \"slots\"** (cpus) available for compute jobs (out of a maximum possible {max_possible_cores}) - see [Table](#fulltable)."))
```

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

For more details, see the [SLURM Queue](slurm-queue.md) page.

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

(queues)=
## Queues

The [List of nodes](fulltable) shows various `partitions`. These are the job queues on SLURM. 

- `slow` is the default
- all jobs submitted using [`srun`](srun) (rather than [`sbatch`](sbatchexample)) will be treated as interactive and sent to the interactive partition which has a limit of one CPU per job. 
- `lgmem` partition requires at least 256GB of RAM to be requested, and will then route to the node with the largest memory. Note that this is a very slow (old) node, so don't do this if you don't need it. 
- There are no time limits on any partitions, default RAM per job is 4 GB.
- In order to submit to a specific partition, 
  - use the `-p` option with `sbatch`, e.g. `sbatch -p lgmem run.sh`. 
  - specify the partition in the `SBATCH` file with `#SBATCH --partition lgmem`.
  - If you don't specify a partition, it will be sent to the default (`slow`).

(fulltable)=
## List of nodes

The following table shows the allocated nodes. Nodes marked `flex` may not be available, because an owner has [reserved](reserving) them. Nodes marked `slurm` are always available. 

:::{note}

`HT` means "hyper-threading", and effectively [multiplies the number of cores by 2](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html), but may not always lead to performance improvement. MATLAB ignores hyper-threading, and will only use the physical number of cores listed in the `cores` column. The various queues can be requested, but most jobs should use the `default` queue. 

:::

```{code-cell} ipython3
:tags: ["remove-input","full-width"]
from IPython.display import HTML
# from jupyter_datatables import init_datatables_mode, render_datatable
from itables import init_notebook_mode, show

init_notebook_mode(all_interactive=True)

# reorder columns
# override the order of columns - this may need to be adjusted if the column names change
columns = ['Nodename', 'allocation', 'partition','cores','RAM',  'local storage in TB', 'model','cores per CPU', 'CPUs', 'HT','cpu benchmark (single thread)', 'vintage' ]

# Reorder the columns
nodes_display = nodes[columns]

#table = nodes_display.to_html(index=False, classes='table table-striped table-bordered table-sm', escape=False, render_links=True)
# Render the HTML table in Jupyter Notebook
#HTML(table)
show(nodes_display, lengthMenu=[15, 25, 50], layout={"topStart": "search"}, classes="display compact")

```

## Size of the cluster


```{code-cell} ipython3
:tags: ["remove-input","full-width"]

# Compute total RAM for flex and slurm nodes
total_ram = nodes['RAM'].sum()

# Display the results (cores already calculated earlier)
print(f"Total cores possible across all SLURM nodes: {max_possible_cores}")
print(f"Total RAM possible across all SLURM nodes: {total_ram} GB")
```


```{code-cell} ipython3
:tags: ["remove-input","full-width"]

# Compute total RAM for currently allocated nodes
alloc_total_ram = allocated_nodes['RAM'].sum()

# Display the results (cores already calculated earlier)
print(f"Total cores currently available across all SLURM nodes: {current_total_cores}")
print(f"Total RAM currently available across all SLURM nodes: {alloc_total_ram} GB")
```
