(stataexample)=
# Stata example

To run some simple, quick programs, you could run

```
srun /usr/local/stata16/stata-mp -b test.do
```

where 

```
// test.do:
creturn list
exit, clear
```

yields

```
...
. do test.do 

. creturn list

System values
-------------

    ---------------------------------------------------------------------------
        c(current_date) = " 3 Feb 2023"
        c(current_time) = "16:59:50"
           c(rmsg_time) = 0                          (seconds, from set rmsg)
    ---------------------------------------------------------------------------
       c(stata_version) = 16.1
             c(version) = 16.1                       (version)
         c(userversion) = 16.1                       (version)
      c(dyndoc_version) = 2                          (dyndoc)
    ---------------------------------------------------------------------------
           c(born_date) = "20 May 2020"
              c(flavor) = "IC"
....
```
