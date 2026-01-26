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

If you want to launch many parallel jobs that use a different parameter each time, use arrays. 

### R example

Here's an example with R:

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
R CMD BATCH name_of_program.R  "${SLURM_ARRAY_TASK_ID}"
```

Save the above code as `slurm_name_of_program.sh` and submit as `sbatch slurm_name_of_program.sh`.

Test it first:

- try out the command line separately, on a compute node: `R CMD BATCH name_of_program.R  some_number`
- then try out the SLURM file with a low count (eg. `array=1-10`)
- then set it to run.

### Stata example

Here's a similar example with Stata. Note that Stata likes to write out a log file when running in batch mode, so this prevents the log files from clobbering each other.

```bash
# ... see above for other SLURM config
#
# Processors per task: usually, 8 bc we have Stata-MP/8. But if your application is single-threaded, then use =1 here, and use "stata-se" below.
#SBATCH --cpus-per-task=1
#
# Memory limit. Default is 4GB. Do testing to find out what a comfortable but not too loose limit is. You might constrain how many jobs can be run simultaneously
#SBATCH --mem=4G
#
# other stuff here...
#

# Create a task-specific directory
[[ -d task${SLURM_ARRAY_TASK_ID} ]] || mkdir task${SLURM_ARRAY_TASK_ID}
# Enter that directory
cd task${SLURM_ARRAY_TASK_ID}
# Run the job with stata-se (single-threaded) passing the argument for the particular job
stata-se -b ../name_of_job.do "${SLURM_ARRAY_TASK_ID}"
#
# writing the log file (name_of_job.log) into the directory (this avoids conflicts)
```

Within Stata, you could always load in some job-specific parameters which you previously have defined, e.g.,

```stata
// stata code
// other configuration above, like directories
//
global lookup "`1'" // capture the command line argument
insheet using "$paramdir/parameters.csv"
keep if jobid=$lookup
// do stuff here that depends on the parametes you just read in.
```

Test it out:

- try out the command line separately, on a compute node: `stata-se name_of_program.do  some_number`
- then try out the SLURM file with a low count (eg. `array=1-10`)
- then set it to run.

### Limit the number of jobs running in an SBATCH array

Large array jobs may fill up the compute cluster, limiting yourself or others from running other jobs. You can limit the number of array jobs that run at once with the `--array=1-200%20` notation. For example,
```
sbatch --array=1-200%10 sbatch-shell.sh
```
This will run an array of 200 jobs, but limit the number of jobs from the array that run at once to 10. 

### Running SBATCH jobs in sequence

It may be advantageous to run jobs on the cluster in sequence, to limit how much you use the cluster at once or e.g. run data preparation code then data analysis code. To do this, you can use the `--dependency` notation:

```
JOB1=$(sbatch --parsable sbatch-1.sh | cut -d ';' -f1)
JOB2=$(sbatch --parsable --dependency=afterok:${JOB1} sbatch-2.sh | cut -d ';' -f1)
```

See more details about `--dependency` [here](https://slurm.schedmd.com/sbatch.html). 
The code above uses the --parsable condition to limit the output to {JOBID};{CLUSTER}. It then pipes {JOBID};{CLUSTER} to the bash command `cut`, which splits {JOBID};{CLUSTER} by `;`, and returns just the {JOBID}. The {JOBID} is then used to define `--dependency=afterok:${JOB1}`, which instructs SLURM to run JOB2 only after JOB1 has finished.   

### Holding Submitted SBATCH Jobs

Sometimes it can be helpful to hold a SBATCH job from running. For example, when you don't want to overwhelm the queue, you can hold a subset of jobs then release them once the first jobs finish up. To hold a job,

```
scontrol hold {JOBID}
```

At this point, when you check the `squeue`, you will see 

```
{JOBID}   regular   {NAME}   {NETID} PD       0:00      1 (JobHeldUser)
```

indicating it being held. The job will remain there until it is released. To release the job, run the command

```
scontrol release {JOBID}
```

