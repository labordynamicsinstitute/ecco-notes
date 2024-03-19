(biohpcres)=
# `biohpc_res`

The `biohpc_res` command is a simple way to check the status of the BioHPC reservation system. Useful commands are:

## General help

```bash
biohpc_res -h
```
## Help for subcommands

```bash
biohpc_res [command] -h
```

## Show available nodes

```bash
biohpc_res avail --category restricted
``` 

## Cancel your reservation

```bash
biohpc_res cancel --res 87654
```

or if you are logged in to a compute node (or at the end of a script)

```bash
biohpc_res cancel current
```

## Create a new reservation

```bash
biohpc_res new --category restricted --hours 24
```