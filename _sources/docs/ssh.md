(ssh)=
# SSH

## Getting SSH

::::{tab-set}

:::{tab-item} Windows

SSH instructions for Windows to come

:::

:::{tab-item} Linux and macOS

You have SSH installed already, you're all set.

:::

::::

## Connecting via SSH


:::::{tab-set}

::::{tab-item} Without a reservation

Even without a reservation or an active SLURM job, you can connect to the BioHPC headnodes `cbsulogin.biohpc.cornell.edu` and `cbsulogin{2,3}.biohpc.cornell.edu` anytime:

```bash
ssh netid@cbsulogin.biohpc.cornell.edu
```

where you replace `netid` with... your NetID!

:::{note}
You do not need to be on the Cornell network or using the VPN for this connection.
:::

::::

::::{tab-item} With a reservation

If you have a reservation or an active SLURM job, you can connect directly to the compute node, e.g.,


```bash
ssh netid@cbsueccoNN.biohpc.cornell.edu
```

where `cbsueccoNN` might be `cbsuecco01` if that's where your jobs are.

:::{warning}
You need to be on the Cornell network, or using the VPN, for this connection to work.
:::

::::

:::::


:::{tip}

Use `tmux` to allow for terminal sessions that you can disconnect and reconnect from.

:::