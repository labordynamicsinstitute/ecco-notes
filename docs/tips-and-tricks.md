(tips-and-tricks)=
# Tips and Tricks


## Configuring automatic reservation cancellation 

If you use the [BioHPC reservation system](reserving) or [SLURM](slurm), it helps others if at the end of a long-running job, your reservation is cancelled as soon as possible. One way to do this is to add the following to the scripts you are running:




::::{tab-set}


:::{tab-item}  Stata

```stata
// Use the code below at the bottom of the Stata "main" or 
// "master" script to automatically sign out 

shell /programs/bin/labutils/endres.pl 
```


:::

:::{tab-item} R

```r
# Add to end of main or last script.
system("/programs/bin/labutils/endres.pl")
```


:::

:::{tab-item} Matlab

```
%Use code below at end of MATLAB main script, or last script, to automatically sign out

system("/programs/bin/labutils/endres.pl")
```


:::

:::{tab-item}  Python

```python
# Use code below at bottom of Python/Anaconda script 
# to automatically sign out

import os

os.system("/programs/bin/labutils/endres.pl")
```

:::


:::{tab-item}  Bash

```bash
#Use the code below at the bottom of the bash "main" or 
# "master" script to automatically sign out 
/programs/bin/labutils/endres.pl 
```

:::

::::


## Unzipping large ZIP files fails

In some cases, unzipping large ZIP files (larger than 2GB) may fail (although the local `unzip` command has been compiled to handle large files).

```
error: invalid zip file with overlapped components (possible zip bomb)
```

If this is detected, you need to use a 64-bit version of a decompression program. This may vary by Linux host.


::::{tab-set}


:::{tab-item}  7z

Instead of `zip`, use `7z` as follows:

```
FILENAME=something
/programs/bin/util/7z x -O${FILENAME} ${FILENAME}.zip
```

(which is the equivalent to the zip command `zip -n ${FILENAME} -d ${FILENAME}). The first option (`-O`) is an upper-case letter `O`, not zero.

:::

:::{tab-item} Others

Other options include using `jar`, if available.
:::

::::
