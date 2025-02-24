(manageslurm)=
# `manage_slurm`

The `manage_slurm` command can be used to manage user-iniated SLURM clusters. It may also be used by an administrator to manage the ECCO-level SLURM cluster

## General help

```bash
manage_slurm --help
```

## Help for subcommands

```bash
manage_slurm [command] --help
```

## Show available clusters

```bash
manage_slurm list
``` 

## Create a cluster

```bash
manage_slurm new machine1,machine2,.
```
where the first node becomes the "head node". A reservation (see [biohpc_res](biohpcres)) is needed on all nodes. 

## Add a node

```bash
manage_slurm addNode masterNode machine
```

will add `machine` to the cluster that has `masterNode` as the head node. Again, a reservation is needed on `machine`, and all users currently with cluster access also need to be added to that reservation.

## Example (complete)

Adjust these:

```bash
node=cbsuecco10
account=2556
headnode=cbsueccosl01
```

then run this

```bash
biohpc_res new $account --server $node --hours 72
biohpc_res list
```

Now grap the reservation number:

```bash
reservation=12345
```

and add that reservation to the cluster:

```bash
biohpc_res edit $reservation --hide
biohpc_res edit $reservation --add ECCO # adds the entire ECCO group to the reservation
manage_slurm addNode $headnode $node 
```

## Debugging SLURM

Sometimes, things go wrong. For instance,

```bash
> sinfo
regular*     up   infinite      2  drain cbsuecco02,cbsueccosl04
```

What does `drain` mean, and how to put the nodes back into service? 

### Find the reason 

```bash
scontrol show nodes=cbsueccosl04 | grep Reason
```

which might return
 
```bash
Reason=Kill task failed [root@2025-02-21T18:19:11]
```

This means a job crashed or timed out leaving open files which made it hard for SLURM to kill it and a timeout kicked in. If you notice something like this, you (as an 'operator' of your cluster) can re-active the node by running 
 
```bash
scontrol update nodename=cbsueccosl04 state=resume
```
