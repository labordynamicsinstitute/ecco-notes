(juliaexample)=
# Julia example

```
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
[lv39@cbsulogin test]$ srun  julia test.jl
  0.016342 seconds (7.42 k allocations: 303.106 KiB, 98.58% compilation time)
  0.000143 seconds (3.49 k allocations: 70.156 KiB)
```
