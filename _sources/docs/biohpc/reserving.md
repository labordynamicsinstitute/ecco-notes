(reserving)=
# Requesting exclusive access to an entire node

Once logged in to the BioHPC website, go to [https://biohpc.cornell.edu/lab/labres.aspx](https://biohpc.cornell.edu/lab/labres.aspx), choose `Restricted`. You can reserve any node, up to the time limit imposed by your group membership:

- general: 24 hours
- faculty: 3 days
- contributing: 7 days on their contributed node

Typical nodes have between 16 and 32 CPUs, and between 128Gb and 1024Gb of RAM (memory). File storage varies substantially.

:::{tip}

You can view your current reservations on the [My Reservations](https://biohpc.cornell.edu/lab/labresman.aspx) page.

:::


:::{warning}

While you have reserved a node, nobody else can access it (unless you explicitly add them to a reservation). If you know you are only using a few CPUs, consider submitting individual jobs via SLURM.

:::

:::{note}

If you do not see an available node immediately, make a reservation for a later time. The default ECCO [SLURM cluster](slurm) uses active scavenging for free cycles, but if you cannot use SLURM and need to use the complete node immediately, contact the owner of the reservation before yours to see if they can release it early.

:::

## Adding users to a reservation

On the [My Reservations](https://biohpc.cornell.edu/lab/labresman.aspx) page, scroll down where you can either

- "Add user with labid" to a reservation from the pull-down menu.
- "Link Group" to an existing reservation, where "Group" is a defined group of users, e.g., your "lab" (e.g., Lars's group would be `ecco_lv39`).

## Manipulating reservations from the command line

If using the command line login node, also see [`biohpc_res`](biohpcres) command.

## Things you can do 

### Create your own private SLURM

If you have to manage a large number of independent jobs, you can create your own private SLURM cluster, including across multiple nodes. For more information, see [SLURM page at BioHPC](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=689#c).
