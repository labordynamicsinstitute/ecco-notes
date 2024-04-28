

# Quick start

## Command line

You need command line access to submit. You do not need a [reservation](reserving) to access a command line.

## Submitting jobs

You can submit from the command line (SSH) at the login nodes `cbsulogin?.biohpc.cornell.edu` (see [access description](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A3). All commands (`sbatch, squeue, sinfo`, etc) have to be run with option `--cluster cbsueccosl01`, otherwise they will apply to a different SLURM cluster (the one at BSCB).

:::{admonition} TIP
:class: tip

Run the following line, logout, then back in, and henceforth you can skip the `--cluster cbsueccosl01` option:
 
```bash
echo 'export SLURM_CLUSTERS="cbsueccosl01"' >> $HOME/.bash_profile
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