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
# Job scheduler (experimental)

A SLURM cluster `eccoslurm` is ready on nodes `cbsueccosl[01,03-04]` and typically also `cbsuecco03` and `cbsuecco04` (the latter are "borrowed", and might not always be available). There are between 48 and 144 "slots" (cpus) available for compute jobs.

## Who can use

Everybody in the ECCO group can submit jobs.

## Why


If you only need one CPU, the easiest and fastest is to use the SLURM job scheduler. It can accomodate up to 100 simultaneous (normal-sized) jobs. You are guaranteed at least 1 CPU (or as many as requested), but no more. (This is new, and not yet fully tested.)

## Available resources

See [https://biohpc.cornell.edu/lab/ecco.htm](https://biohpc.cornell.edu/lab/ecco.htm) for the general overview, and the table below for some more specifics. 
Some of the nodes are a bit old (they will run about as fast as CCSS-RS, but much slower than a recent desktop), but have a ton of memory, and lots of CPUs. For instance, cbsuecco02 has 1024GB of memory. 

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
summary_table = nodes[["allocation", "cores", "CPUs","RAM","local storage in TB"]]
summary = summary_table.groupby('allocation')[['cores', 'CPUs', 'RAM',"local storage in TB"]].sum().reset_index()

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

# Reorder the columns
nodes = nodes[columns]

#table = nodes.to_html(index=False, classes='table table-striped table-bordered table-sm', escape=False, render_links=True)
# Render the HTML table in Jupyter Notebook
#HTML(table)
show(nodes, lengthMenu=[15, 25, 50], layout={"topStart": "search"}, classes="display compact")

```

- *local* disk space refers to the `/workdir` temporary workspace. All nodes have access to the shared home directory.

## Detailed info

Detailed instructions on how to use a cluster are provided at [https://biohpc.cornell.edu/lab/cbsubscb_SLURM.htm](https://biohpc.cornell.edu/lab/cbsubscb_SLURM.htm) and the [official SLURM documentation](https://slurm.schedmd.com/documentation.html) ([useful cheatsheet on commands (PDF)](https://slurm.schedmd.com/pdfs/summary.pdf)).

## Quick start

You can submit from the command line (SSH) at the login nodes `cbsulogin?.biohpc.cornell.edu` (see [access description](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A3). All commands (`sbatch, squeue, sinfo`, etc) have to be run with option `--cluster eccoslurm`, otherwise they will apply to a different SLURM cluster (the one at BSCB).

:::{admonition} TIP
:class: tip

Run the following line, logout, then back in, and henceforth you can skip the `--cluster eccoslurm` option:
 
```bash
echo 'export SLURM_CLUSTERS="eccoslurm"' >> $HOME/.bash_profile
echo netid@cornell.edu >> $HOME/.forward
``` 

(replace your `netid` in the second command).

:::

There is only one partition (queue) containing all nodes, default parameters (changeable through SLURM options at submission, see below) are:

- 1 core and 4 GB RAM per job 
- infinite run time. 

## Interactive shell

Interactive shell can be requested  with command 

```bash
srun --cluster eccoslurm --pty bash -l
```

or if you ran the above TIP:

```bash
srun --pty bash -l
```


## To see running jobs

```
squeue
```

## To cancel a running job

Use

```
scancel (ID)
```

where the ID can be gleaned from the `squeue` command.
