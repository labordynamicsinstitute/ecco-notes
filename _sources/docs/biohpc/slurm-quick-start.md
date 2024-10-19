

# Quick start

## Command line

You need command line access to submit. You do not need a [reservation](reserving) to access a command line, you can connect to the BioHPC Login (head) node `cbsulogin?.biohpc.cornell.edu`.

(onetimesetup-slurm)=
## One-time setup

### Setting up SLURM-specific settings

From a command line, run the following lines, logout, then back in, and henceforth you can skip the `--cluster cbsueccosl01` option:
 
```bash
echo 'export SLURM_CLUSTERS="cbsueccosl01"' >> $HOME/.bash_profile
```

In the following, replace `netid` with your actual NetID. 


```bash
echo netid@cornell.edu >> $HOME/.forward
``` 


### Enabling software via `module`


```bash
git clone https://github.com/labordynamicsinstitute/biohpc-modules $HOME/.modulefiles.d
```

See [Customizing `modules`](custommodules) for more details.

## Submitting jobs

You can submit from the command line (SSH) at the login nodes `cbsulogin?.biohpc.cornell.edu` (see [access description](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A3). All commands (`sbatch, squeue, sinfo`, etc) have to be run with option `--cluster cbsueccosl01` (but see [one-time setup](onetimesetup-slurm)).

There is only one partition (queue) containing all nodes, default parameters (changeable through SLURM options at submission, see below) are:

- 1 core and 4 GB RAM per job 
- infinite run time. 

## Interactive shell

Interactive shell can be requested  with command 

```bash
srun --cluster cbsueccosl01 --pty bash -l
```

or if you ran the above TIP:

```bash
srun --pty bash -l
```

If you need a specific node, use


```bash
srun -w cbsueccoXX --pty bash -l
```

## Interactive GUI jobs

You should be able to get interactive GUI jobs (Stata, MATLAB) to work as follows:

```bash
salloc -N 1 
ssh -X $SLURM_NODELIST /usr/local/stata18/xstata
```

or 

```bash
salloc -N 1 
ssh -X $SLURM_NODELIST /local/opt/MATLAB/R2023a/bin/matlab
```

:::{warning}

It is technically feasible to login to each node without using SLURM. However, this confuses the job scheduler. Do not abuse this, exclusion from use of the cluster may be the consequence.

:::

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

## Additional information

- [https://biohpc.cornell.edu/lab/SLURM-on-demand.htm](https://biohpc.cornell.edu/lab/SLURM-on-demand.htm)
