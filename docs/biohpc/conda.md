# Running Python and R from Stata (with conda)

Often we will want to call Python or R from inside Stata, this will often look like `python script example.py`, `shell Rscript example.R`, or use a stata package like `rsource`. By default the new process inherits Stata's environment and picks up whatever `python3` or `Rscript` is first on the `PATH` (which is often not our intended version). A [conda](https://docs.conda.io/) environment fixes this: we create an environment with the intended software and package versions and activate it before running code so there is no ambiguitity.

## Loading conda

Follow instructions [at BioHPC](https://biohpc.cornell.edu/lab/doc/Software_Installation_exercises1.html) on how to install `miniconda` in your home directory, then the line

```bash
source $HOME/miniconda3/bin/activate
```

will load conda. Conda will need to be loaded **every time** before creating or activating an environment.



## Creating the environment

A conda environment is a named, self-contained set of package versions. A single environment can hold both Python and R, so you do not need separate environments for the two languages. You create it once and it stays until you delete it, and you simply activate it whenever you need it. There are two equivalent ways to create one.
 
**From an `environment.yml` file.** This is the better option, because the file
is a written record of exactly what the environment contains. Keep it alongside
your code so the environment can be recreated later. Each
line under `dependencies` is a package. `=` pins a specific version, and
`conda-forge` is the public (and free!) repository the packages are downloaded from:
 
```
# example environment.yml
name: (your_env_name)
channels:
  - conda-forge
dependencies:
  - python=3.10
  - r-base=4.3
  - pandas=2.2
  - r-fixest
```
 
Then build it from that file:
 
```
source $HOME/miniconda3/bin/activate
conda env create -f environment.yml
```
 
**In one line.** If you would rather not write a file, list the same packages
directly on the command line. `-n` sets the environment name and `-c` sets the
channel:
 
```
source $HOME/miniconda3/bin/activate
conda create -n (your_env_name) -c conda-forge python=3.10 r-base=4.3 pandas=2.2 r-fixest
```
 
Either way, you can confirm the environment was created with `conda env list`. Both of these options should be run on a compute node.
 

## Using it from Stata

Load conda and activate the environment **before** launching Stata, so future processes inherit it:
  
```
source $HOME/miniconda3/bin/activate
conda activate (your_env_name)
```
 
**Python.** If the do-file runs Python via the command line (i.e. `shell python example.py`), it works automatically from the activated
environment because the environment's `PATH` is inherited. No extra setup is needed.
 
You only need the next step to use Stata's *built-in* Python (a
`python:` ... `end` block, or `python script file.py`). Then, Stata must be pointed at the environment.
Stata can read the `CONDA_PREFIX` that activating the environment set for us:
 
```
* main.do
local pyexec : environment CONDA_PREFIX
python set exec "`pyexec'/bin/python3"
```
 
**R.** Stata has no native R integration; `rsource` (and `shell`) just
calls `Rscript` on the `PATH`, which is already pinned after activating our environment.

 
```{note}
To actually *run* a script, you use `shell` as shown below. Output from
`shell` is not reliably captured in Stata's batch log (`main.log`), so redirect
it to its own file and print that to the stata log file:
 
    *somewhere in main.do
    shell python example_python.py > example_python.log 2>&1
    type example_python.log
 
    shell Rscript example_R.R > example_R.log 2>&1
    type example_R.log
 
`2>&1` sends error messages to the same file as normal output, so a script that
fails still leaves a readable log.
```
 

## SBATCH script

Use the default options from the [SBATCH example](sbatch.html#sbatchexample),
then add:

```
source $HOME/miniconda3/bin/activate
conda activate (your_env_name)

module load stata/18
stata-mp -b main.do
```

You may also create your conda environment in the sbatch script but it should only be run the first time; either delete or comment out the creation after the environment exists.
