(software)=
# Software

The general ECCO landing page at BIOHPC ([https://biohpc.cornell.edu/lab/ecco.htm](https://biohpc.cornell.edu/lab/ecco.htm)) for the general overview.

## Available software

- MATLAB (various versions)
- Stata (various versions)

:::{admonition} Note on Stata variants
:class: dropdown

On our installed Stata, all three variants are available:

- Stata/BE (Basic Edition) (`stata` command)
- Stata/SE (Standard Edition) (`stata-se` command)
- Stata/MP (Multiprocessor Edition) (`stata-mp` command)

You probably do not want to use `stata` except for quick tests. You may not want to use `stata-mp` unless you know that you will leverage the additional cores, since it will **consume** more cores (you need to request more cores in your SLURM job) and may not be faster for smaller jobs.

Pre-pending each command with `x` (lower-case; e.g., `xstata-mp`) will run the command in a graphical mode. See the [Graphical applications](graphical) page for more details.

:::

In addition, all regular [BioHPC-maintained software](https://biohpc.cornell.edu/lab/userguide.aspx?a=software) is available, see the [full list](https://biohpc.cornell.edu/lab/userguide.aspx?a=software). You search through the list with `ctrl-f` or `cmd-f` in your browser.

- [R](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=37#c) (multiple versions)
- [Python](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=556#c) (multiple versions)
- [Julia](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=182#c)
- [Docker](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=340#c)
- [Singularity](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=543#c)

You can also run

- [Jupyterlab](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=1053#c)
- [RStudio](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=266#c)

:::{tip}

Always read the BioHPC-specific instructions on how to run the software!

:::


## Using `module`

When multiple versions of a software exist, you must decide which version to use. For BioHPC-maintained software, you can use the `module` command to load the desired version. 

### BioHPC-managed modules

You can find out the available versions of software "managed" through `module` by using the `module avail` command.

```bash
module avail 
```

which will list out the available configurations:

```bash
$ module avail

------------------------------------------------------ /usr/share/modulefiles -------------------------------------------------------
mpi/openmpi-x86_64  

-------------------------------------------------- /usr/share/Modules/modulefiles ---------------------------------------------------
gcc/10.2.0  java/1.7.0  java/13.0.2  perl/5.16.3  python/2.7.5   python/3.6.15-r9  R/4.0.5-r9  R/4.2.1-r9  
gcc/10.4.0  java/1.8.0  java/21.0.1  perl/5.22.0  python/2.7.15  python/3.10.6-r9  R/4.1.3-r9  R/4.3.2     
```

(default versions may be underlined).



For instance, to load the default version of R, use:

```bash

module load R
```

To load a specific version of R, use:

```bash
module load R/4.3.2
```

(custommodules)=
### Customizing `modules`

BioHPC only maintains modules for common software. For ECCO-specific software, you may need to create your own module files, or use a pre-configured set maintained by Lars.

#### Getting additional module configurations

The easiest way to get started is to copy the `module` files from Lars's Github repository:

```bash

git clone https://github.com/labordynamicsinstitute/biohpc-modules $HOME/privatemodules
```

You can refresh this at any time using

```bash
cd $HOME/privatemodules
git pull
```

:::{admonition} Not all modules listed are actually installed!

The modules listed in the repository will work on the specific nodes that the software is installed. Not all nodes have all software installed. The `module` command only allows the shell to find the software if it is installed, but does not actually install the software.

For MATLAB in particular, due to space constraints, some versions listed in the modules are not available on all nodes. You can use the `sbatch`  `constraint`  option to ensure that your job ends up on a node that has the desired MATLAB version installed (you should still use the `module`  command to then configure your environment to use that version). For instance, to ensure that your job runs on a node with MATLAB R2023b, use

```bash
sbatch -C matlab_R2023b [other options]
```


:::

#### Using the additional modules

```bash
module load use.own
```

will load the configuration from `$HOME/privatemodules`. To have the modules always be available, add that line to your `$HOME/.bashrc`.



#### Creating your own module files

You can suggest additional module files, or create your own. If you create your own, create a [fork from Lars' repository](https://github.com/labordynamicsinstitute/biohpc-modules/fork), and create additional ones there. If you want to contribute back, issue a [pull request](https://github.com/labordynamicsinstitute/biohpc-modules/compare).


## Additional software

You can use additional software via `docker` or `singularity` (`apptainer`). If you need software installed, contact BioHPC, or install in your own home directory.
