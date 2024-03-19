
(slurm)=
# Job scheduler (experimental)

A SLURM cluster `eccoslurm` is ready on nodes `cbsueccosl[01,03-04]` and typically also `cbsuecco03` and `cbsuecco04` (the latter are "borrowed", and might not always be available). There are between 48 and 144 "slots" (cpus) available for compute jobs.

## Who can use

Everybody in the ECCO group can submit jobs.

## Why


If you only need one CPU, the easiest and fastest is to use the SLURM job scheduler. It can accomodate up to 100 simultaneous (normal-sized) jobs. You are guaranteed at least 1 CPU (or as many as requested), but no more. (This is new, and not yet fully tested.)

## Available resources

See [https://biohpc.cornell.edu/lab/ecco.htm](https://biohpc.cornell.edu/lab/ecco.htm). 
Some of the nodes are a bit old (they will run about as fast as CCSS-RS, but much slower than a recent desktop), but have a ton of memory, and lots of CPUs. For instance, cbsuecco02 has 1024GB of memory. 

| Access | Cores | Total memory | Total *local* disk | 
|--------|-------|--------------|--------------------|
| [Reservation](https://biohpc.cornell.edu/lab/labres.aspx) | 216 | 2,048 GB | 10.1 TB |
| SLURM | 144 | 1,129 GB | 18 TB |

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
