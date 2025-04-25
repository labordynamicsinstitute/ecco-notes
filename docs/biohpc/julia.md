(juliaexample)=
# Julia example

## Assumptions


We will assume that your code and data are organized as follows:

```
/home/netid/project/
            ├── test.jl
            ├── data.csv
            ├── etc.
```

## Quick run

```
cd $HOME/project
srun julia test.jl
```
with

```julia
# test.jl
x = rand(1000);
function sum_global()
           s = 0.0
           for i in x
               s += i
           end
           return s
       end;
@time sum_global()
@time sum_global()
```

which yields

```
[lv39@cbsulogin project]$ srun  julia test.jl
  0.016342 seconds (7.42 k allocations: 303.106 KiB, 98.58% compilation time)
  0.000143 seconds (3.49 k allocations: 70.156 KiB)
```

## SBATCH script

Create a new file, called `main.sbatch`, with the following content:

### Top

Use the default options from the [SBATCH example](sbatchexample), and paste them into `main.sbatch`.

### Adjustments

If you are using any parallel processing Julia, adjust the `node`, `tasks-per-node`, and `cpus-per-task`. For instance, if you intend to run with **4** parallel threads, use

```
#SBATCH --cpus-per-task=4
```

### Bottom

Now for the functional part, which is more or less the same as the `srun` command above, but with some additional options:

```bash
# Modules aren't available yet for Julia, consider using `juliaup`. 

julia --threads ${SLURM_CPUS_PER_TASK} test.jl
```

You should also consider using Julia-specific project features. This simple page is not the right place to teach that.
