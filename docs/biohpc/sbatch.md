(sbatchexample)=
# SBATCH examples

## Simple SBATCH example

For more fine-grained control over jobs submitted to the SLURM queues, you can use `sbatch`:

```
sbatch run.sh
```

`sbatch` will respect both command-line options and embedded options in the script headers:

```
#!/bin/bash
# Job name:
# Note: Only the first 8 characters show up when typing 'squeue' to get a list of running jobs
#SBATCH --job-name=RunStata
#
# Request one node:
#SBATCH --nodes=1
#
# Specify number of tasks for use case (example):
#SBATCH --ntasks-per-node=1
#
# Processors per task: here, 8 bc we have Stata-MP/8
#SBATCH --cpus-per-task=8
#
# Memory limit. Default is 4GB
#SBATCH --mem=4G
#
# Wall clock limit. Default is 1 hour. Format is HH:MM:SS, or DD-HH:MM:SS where DD is the number of days.
#SBATCH --time=00:00:30
#
# Email?
# A better alternative is to put your email into $HOME/.forward and remove it here, makes code more portable.
#DONOTSBATCH --mail-user=youremail@cornell.edu
#SBATCH --mail-type=ALL
#
## Command(s) to run (example):
### This command is specific to the ECCO nodes
/usr/local/stata16/stata-mp -b main.do

### Assumes "matlab" is in your path, see BioHPC notes on Matlab for non-default locations.
## Matlab - will run "main.m", output goes to "srun-NNNN.out"
# matlab -nodisplay -r "addpath(genpath('.')); main"

# Alternatively, load any modules. You may need to follow the custom setup on the website.
# 
# module load stata/18
# stata-mp -b do main.do


```

These options allow you to extend the allowed runtime, the allowed memory, etc. You can query what the defaults are by running

```
scontrol show partitions
```

which will show a few (somewhat obscure) parameters for the queue ("partition"). For instance,

```
$ scontrol show partitions
PartitionName=regular
   AllowGroups=ALL AllowAccounts=ALL AllowQos=ALL
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=cbsuecco[03-04],cbsueccosl[01,03-04]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=144 TotalNodes=5 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=4096 MaxMemPerNode=UNLIMITED
   TRES=cpu=144,mem=1157977M,node=5,billing=144
```
shows that the default partition (`regular`) has a default of **4096 MB** per Node (`DefMemPerNode=4096`), which can be increased without restriction upon request (`MaxMemPerNode`) by using `--mem xxxx` at the command line or `#SBATCH --mem xxxx` in the script (`--mem 0` uses all the memory on the node).


## SBATCH Array job

If you want to launch many parallel jobs that use a different parameter each time, use arrays. Here's an example:

```bash
#!/bin/bash
#SBATCH --job-name=ilm
## This will write the output to a one file per array id. Make sure the directory exists!
#SBATCH --output=logs/%a.out
## This will write any error msgs
#SBATCH --error=logs/%a.err
#SBATCH --ntasks=128
## Adjust the range of the array you probably want this to be the total number of firms, but try it with a few.
#SBATCH --array=1-12345
## This means you allow each job to run up to 0 days and 72 hours (that's what the notation says). Adjust
#SBATCH -t 0-72:00 

## Adjust the R version to be the same you are using in Rstudio/interactively!
module load R/4.4.0

## The ${SLURM_ARRAY_TASK_ID} is the counter from the array argument.
## You will need to process the first argument within the code.
## You can try out this same command line manually. If it works, then it will work from within SLURM
R CMD BATCH name_of_program.R  "${SLURM_ARRAY_TASK_ID}
```

Save the above code as `slurm_name_of_program.sh` and submit as `sbatch slurm_name_of_program.sh`.

Test it first:

- try out the command line separately, on a compute node: `R CMD BATCH name_of_program.R  some_number`
- then try out the SLURM file with a low count (eg. `array=1-10`)
- then set it to run.
