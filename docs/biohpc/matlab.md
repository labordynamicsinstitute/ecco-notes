(matlabexample)=
# MATLAB example

## Assumptions


We will assume that your code and data are organized as follows:

```
/home/netid/project/
            ├── main.m
            ├── data.csv
            ├── etc.
```

## Quick run

```
srun matlab -nodisplay -r "addpath(genpath('.')); main"
```
with

```matlab
# main.m
# Simple program to loop and time 
# the computation of the sum of the first 1000 integers
tic
s = 0;
for i = 1:1000
    s = s + i;
end
toc
```

which yields

```
[lv39@cbsulogin project]$ srun matlab -nodisplay -r "addpath(genpath('.')); main"
```

## SBATCH script

Create a new file, called `main.sbatch`, with the following content:

### Top

Use the default options from the [SBATCH example](sbatchexample), and paste them into `main.sbatch`.

### Bottom

Now for the functional part, which is more or less the same as the `srun` command above, but with some additional options:

- choosing additional MATLAB versions
- assuming you have done the [custom module setup](custommodules)

```bash
#!/bin/bash
#

### Use the module command to load the relevant matlab module
### Use "module avail" to see all available versions
module load matlab/2022a

## Matlab - will run "main.m", output goes to "srun-NNNN.out"
# matlab -nodisplay -r "addpath(genpath('.')); main"

## Matlab - will run "main.m", output goes to "main.(DATE_TIME).out"
matlab -nodisplay -r "addpath(genpath('.')); main" > main.$(date +"%Y%m%d_%H%M%S").out
```