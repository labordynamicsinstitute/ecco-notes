(stataexample)=
# Stata example

## Assumptions


We will assume that your code and data are organized as follows:

```
/home/netid/project/
            ├── main.do
            ├── data.csv
            ├── etc.
```

## Quick run

To run some simple, quick programs, you could run

```
srun /usr/local/stata16/stata-mp -b test.do
```

where 

```
// test.do:
creturn list
exit, clear
```

yields

```
...
. do test.do 

. creturn list

System values
-------------

    ---------------------------------------------------------------------------
        c(current_date) = " 3 Feb 2023"
        c(current_time) = "16:59:50"
           c(rmsg_time) = 0                          (seconds, from set rmsg)
    ---------------------------------------------------------------------------
       c(stata_version) = 16.1
             c(version) = 16.1                       (version)
         c(userversion) = 16.1                       (version)
      c(dyndoc_version) = 2                          (dyndoc)
    ---------------------------------------------------------------------------
           c(born_date) = "20 May 2020"
              c(flavor) = "IC"
....
```

## SBATCH script


Create a new file, called `main.sbatch`, with the following content:

### Top

Use the default options from the [SBATCH example](sbatchexample), and paste them into `main.sbatch`.

### Bottom

Now for the functional part, which is more or less the same as the `srun` command above, but with some additional options:

- choosing additional Stata versions
- assuming you have done the [custom module setup](custommodules)

```bash
#!/bin/bash
#

### Use the module command to load the relevant matlab module
### Use "module avail" to see all available versions
module load stata/18

## Will create a `main.log` file with the output

stata-mp -b main.do

### Without the use of modules
# STATAVERSION=18
# /usr/local/stata${STATAVERSION}/stata-mp -b main.do
```
