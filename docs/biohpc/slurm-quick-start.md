
(slurm-quick-start)=
# Quick start

## Command line

You need command line access to submit. You do not need a [reservation](reserving) to access a command line, you can connect to the BioHPC Login (head) node `cbsulogin?.biohpc.cornell.edu`.

(onetimesetup-slurm)=
## One-time setup

::::{note}
:class: dropdown
**Setting up SLURM-specific settings**

From a command line, run the following lines, logout, then back in, and henceforth you can skip the `--cluster cbsueccosl01` option:
 
```bash
echo 'export SLURM_CLUSTERS="cbsueccosl01"' >> $HOME/.bash_profile
```

In the following, replace `netid` with your actual NetID. 


```bash
echo netid@cornell.edu >> $HOME/.forward
``` 


**Enabling software via `module`**


```bash
git clone https://github.com/labordynamicsinstitute/biohpc-modules $HOME/privatemodules
```

See [Customizing `modules`](custommodules) for more details.

::::

## Submitting jobs

You can submit from the command line (SSH) at the login nodes `cbsulogin?.biohpc.cornell.edu` (see [access description](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A3) or the [SSH section](ssh)). All commands (`sbatch, squeue, sinfo`, etc) have to be run with option `--cluster cbsueccosl01` (but see [one-time setup](onetimesetup-slurm)).

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

You can use all valid SLURM command line options (the same as are listed in a `sbatch` file) as well. In their absence, you will get default values (limitations).[^limits] For instance, the above invocations get unlimited memory, but are limited to **1 task** on **1 CPU**. If you needed to use more within your interactive shell, for instance , you might want to specify

```bash
srun --nodelist cbsueccoXX --cpus-per-task 8 --pty bash -l
```
or

```bash
srun -w cbsueccoXX -c 8 --pty bash -l
```

to run an 8-core job on node `cbsueccoXX` ([documentation](https://slurm.schedmd.com/sbatch.html)).

[^limits]: You can get a list of all the default values by getting a login shell (`srun --pty bash -l`), then listing all the SLURM-related environment variables (`export | grep SLURM`).

## Interactive GUI jobs

Once you have set yourself up for "X11" jobs (see [Graphical applications](graphical), you should be able to get interactive GUI jobs (Stata, MATLAB) to work as follows:

- Get an interactive shell, as above, but with additional parameters (if you haven't done the [One-time setup](onetimesetup-slurm), add `--cluster cbsueccosl01`):

```bash
srun --cpus-per-task 2 --nodes 1 --mem=8G  --x11 --pty bash -l
```

or

```bash
srun -c 2 -N1 --mem=8G --x11 --pty bash -l
```

then, for Stata (see [Software page](software) for variants))

```bash
/usr/local/stata18/xstata
```

or, for MATLAB (see [Software page](software) for variants):

```bash
srun -c 2 -N1 --mem=8G  --x11 --pty bash -l
/local/opt/MATLAB/R2023a/bin/matlab
```

When done, type `exit` to exit the interactive session. 

Note that you must align the requested CPU count (`-c 2`) with the expected usage of the MATLAB and Stata variants. While just opening them will not cause a problem, our Stata/MP version will use up to 8 cores (e.g., use `-c 9`), and MATLAB, unless specifically configured not to do so, will utilize the amount of *physical* cores on the system (for instance, use `-c 55` if running on a 54-CPU machine that has hyper-threading turn on, and `-n 54` if not). In general, you want the compute job to have 1 core for the terminal you launch it from, and as many cores as the software will consume. For any serious computing, please use [non-interactive mode](sbatchexample), or adjust the number of cores you request accordingly.

:::{warning}

It is technically feasible to login to each node without using SLURM. However, this confuses the job scheduler. Do not abuse this, **exclusion from use of the cluster may be the consequence**.

:::

## Running RStudio

See the [BioHPC Software site](https://biohpc.cornell.edu/lab/userguide.aspx?a=software) (search for Rstudio, then click on the link) for instructions on how to run Rstudio on the cluster. In a nutshell,

```bash
salloc -N 1
srun /programs/rstudio_server/rstudio_start 4.4.0
```

will run an instance of RStudio Server on the node you were allocated (if it isn't already running). You can then connect to it using a web browser, using port 8016. The output of the `srun` command will tell you which node to connect to. For instance, 

```
Nov 18 08:37:44 cbsueccosl03.biohpc.cornell.edu systemd[1]: Starting RStudio Server...
Nov 18 08:37:44 cbsueccosl03.biohpc.cornell.edu systemd[1]: Started RStudio Server.
```

means you would connect to <http://cbsueccosl03.biohpc.cornell.edu:8016>. If you are connected to the VPC, you can connect to it from your laptop, otherwise use the browser within the [VNC](vnc) session.

::::{warning}

If you see
```
[lv39@cbsueccosl01 ~]$ srun /programs/rstudio_server/rstudio_start 4.4.0
Rserver is already running - no need to start it
If you want to restart it please stop it first using
/programs/rstudio_server/rstudio_stop
```

then somebody else may already have started an instance there. **DO NOT STOP IT**. You can still connect to **your own session** on that server. 
::::

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
