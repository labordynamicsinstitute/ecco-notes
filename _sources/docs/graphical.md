(graphical)=
# Graphical applications

There are two types of graphical applications: web-enabled, and those that are not.

> Much more detail can be found at <https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A5>

## When to use graphical applications

BioHPC is not meant to be used persistently with graphical applications. Usage should be restricted to debugging and brief development. 

## Web-enabled Applications

Applications such as Rstudio and Jupyter Notebooks are web-enabled, i.e., they can be used through a browser window. This is NOT how you usually use these on a laptop. However, on BioHPC, this is strongly preferred, as much more stable and faster. See the [BioHPC page](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A5) for general guidance, and the specific software at the [BioHPC software help pages](https://biohpc.cornell.edu/lab/labsoftware.aspx) for details.


## Old-fashioned Graphical Applications

Stata and MATLAB are probably the most likely applications that are used via graphical applications. 

:::{note}

Note that Stata exists, on Linux systems, as a purely text-based application (`stata` instead of `xstata`) that is perfectly adapted to the SLURM scheduler. There are also ways to run MATLAB as a purely text-based application. Both can be run interactively, within a SLURM interactive shell.

:::

In order to run a graphical application, you need a graphical desktop. These can either be **remote** or **local** (i.e., on your desktop or laptop). 

### Remote desktop

A remote desktop available on BioHPC is [VNC](vnc).

### Local desktop

Running a remote graphical application (i.e., running on a BioHPC node) on your local desktop (laptop) is possible, but requires some understanding.

- The application will terminate when you close the connection to the remote node. This is not to be used for long-running applications.
- The graphical performance (and interacting with the graphical application) will be a function of your internet connection speed. This is perfectly doable in many situations, but may be impacted by network issues. This does not usually affect the statistical functioning of the application.
- You need a technical way to display the application on your local desktop. This usually works via what's called an "X server". This is a native part of Linux systems, but requires some setup on Windows and macOS. 
- BioHPC [recommends](https://biohpc.cornell.edu/lab/userguide.aspx?a=access#A5) [MobaXterm](http://mobaxterm.mobatek.net/download.html) for Windows (check with your system administrator, if necessary), though the use of [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps) is also possible. 
- [Xquartz](https://docs.cse.lehigh.edu/xforwarding/xforwarding-mac/) is the recommended way on macOS.
- In all cases, the initial connection is assumed to occur via [SSH](ssh).


:::{warning}

You should never use Rstudio as a graphical application. Performance sucks.

:::