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

:::{note}

Since September 2026, there is also a `bioslurm` cluster which is available to subscribers, see [the bioslurm page](https://biohpc.cornell.edu/lab/userguide.aspx?a=bioslurm). Wherever these pages mention `cbsueccosl01`, you can replace `bioslurm` with some limitations.
:::


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

For more details, see the [SLURM Queue](slurm-queue.md) page. For explanation of the "Partition", see [Queues](queues) section below. 

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
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
slow*          up   infinite      3    mix cbsuecco[01,07-08]
fast           up   infinite      3    mix cbsuecco[09-10,14]
fast           up   infinite      1  alloc cbsuecco13
lgmem          up   infinite      1    mix cbsuecco02
interactive    up   infinite      2   idle cbsueccosl[03-04]
scavenge       up   infinite      2   idle cbsuecco[11-12]

```

which shows that currently, the 2 nodes in the interactive partition (queue) and the 2 nodes in the `scavenge` partition are idle (no jobs running), seven have some jobs running on them, but can still accept smaller jobs (`mix` means there are free CPUs), and one is completely used (`alloc`).

:::{note}

`sinfo` and `squeue` only list the partitions you are allowed to submit to. Contributed nodes also belong to an owner partition reserved for the group that paid for them, which you will not see unless you are a member of that group, or unless you add the `-a` option (`sinfo -a --cluster cbsueccosl01`). See [the `scavenge` partition](scavenge) below.

:::

(queues)=
## Queues

The [List of nodes](fulltable) shows various `partitions`. These are the job queues on SLURM. 

- `slow` is the default
- all jobs submitted using [`srun`](srun) (rather than [`sbatch`](sbatchexample)) will be treated as interactive and sent to the interactive partition which has a limit of one CPU per job. 
- `lgmem` partition requires at least 256GB of RAM to be requested, and will then route to the node with the largest memory. Note that this is a very slow (old) node, so don't do this if you don't need it. 
- `scavenge` lets any ECCO user run on nodes that were paid for by one research group, for as long as that group is not using them. Jobs in this partition can be interrupted at any moment, so they need to be restartable: see [the `scavenge` partition](scavenge) below.
- There are no time limits on any partitions, default RAM per job is 4 GB.
- In order to submit to a specific partition, 
  - use the `-p` option with `sbatch`, e.g. `sbatch -p lgmem run.sh`. 
  - specify the partition in the `SBATCH` file with `#SBATCH --partition lgmem`.
  - If you don't specify a partition, it will be sent to the default (`slow`).

(scavenge)=
### The `scavenge` partition

Some nodes were funded by research groups, who are the "owners". Their free compute cycles are contributed to the overall cluster, but owners retain the right to pull their availability at any point. Such nodes, marked `scavenge`  in the list below, belong to two partitions (queues) simultaneously:


- an **owner partition**, named after the owner's netid , which only members of the owning group may submit to. 
- **`scavenge`**, open to all ECCO users, in the same priority tier as `slow`, `fast` and `lgmem`.

:::{note}

Owner nodes sit in a higher priority tier than every other partition. 

:::

#### What happens when the owner submits a job

Jobs in the `scavenge`  queue can be "preemted". Preemption only happens when it is needed. If there are free nodes in the owner's queue, the owner's job is allocated to that.  Only jobs on the node the owner actually needs are preempted. Because  owner jobs always take a whole node, every `scavenge` job on that node is killed. 

#### What happens to pre-empted jobs?

A preempted job

1. has `CANCELLED ... DUE TO PREEMPTION` written to its output, and is sent `SIGCONT` followed by `SIGTERM`;
2. has **30 seconds** to save what it can, and is then killed with `SIGKILL`;
3. is put back in the queue if you submitted it with `--requeue`, keeping its job ID and its partition list. It is held for two minutes (`squeue` gives reason `BeginTime`), then starts again **from the top of the script**, possibly on a different node. Without `--requeue` it is simply cancelled; `--no-requeue` makes that explicit.

:::{note}
:class: dropdown

This cluster sets `JobRequeue=0`, so a preempted job is **cancelled unless you submitted it with `--requeue`**.


`scontrol show partition scavenge` reports `GraceTime=120`, but that setting only takes effect under `PreemptMode=CANCEL`. This partition uses `PreemptMode=REQUEUE`, where the interval between `SIGTERM` and `SIGKILL` is the cluster-wide `KillWait`, currently 30 seconds.

:::

For a concrete job template that is safe to requeue under `scavenge`, see the [scavenge-safe example in the SBATCH examples](sbatchexample).

#### Choosing between partitions

You can name several partitions, and SLURM will start your job in whichever one can run it first:

```bash
#SBATCH --partition=fast,scavenge
```

Listing `fast` first means SLURM uses `fast` whenever it can start the job there just as soon, and falls back to `scavenge` when that would start sooner. A job that does start in `scavenge` stays preemptable for as long as it runs there. Members of the owning group use the same mechanism the other way round, listing their own partition first (`owner`  should be replaced by the actual partition name of the owning group).


```bash
#SBATCH --partition=owner,fast
```

(fulltable)=
## List of nodes

The following table shows the allocated nodes. Nodes marked `flex` may not be available, because an owner has [reserved](reserving) them. Nodes marked `slurm` are always available. Nodes in the `scavenge` partition are contributed nodes that anybody may use at low priority, and where jobs may be interrupted by the owner: see [the `scavenge` partition](scavenge). 

:::{note}

`HT` means "hyper-threading", and effectively [multiplies the number of cores by 2](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html), but may not always lead to performance improvement. MATLAB ignores hyper-threading, and will only use the physical number of cores listed in the `cores` column. The various queues can be requested, but most jobs should use the `default` queue. 

:::

:::{note}

🤖 For AI coding of your SLURM submission, you can point the agent at <https://github.com/labordynamicsinstitute/ecco-notes/blob/main/_data/ecconodes.csv> for machine-readable version of this table.

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
