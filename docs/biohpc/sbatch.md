(sbatchexample)=
# SBATCH example

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
# Wall clock limit:
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
shows that the default partition (`regular`) has a default of 4096 MB per Node (`DefMemPerNode=4096`), which can be increased without restriction upon request (`MaxMemPerNode`) by using `--mem xxxx` at the command line or `#SBATCH --mem xxxx` in the script (`--mem 0` uses all the memory on the node).

