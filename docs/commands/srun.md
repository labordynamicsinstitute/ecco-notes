(srun)=
# `srun`

`srun` is a command to run a job on a cluster. For much more information on all things SLURM, see [CAC tutorial](https://cvw.cac.cornell.edu/slurm/basics/execution-srun).


Interactive shell can be requested  with command 

```bash
srun --cluster cbsueccosl01 --pty bash -l
```

or if you've correctly set up your environment:

```bash
srun --pty bash -l
```
