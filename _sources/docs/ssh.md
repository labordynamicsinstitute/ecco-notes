(ssh)=
# SSH

## Getting SSH

::::{admonition} Use of 2FA for SSH

As of February 2026, BioHPC also requires the use of 2FA for SSH. Consult the BioHPC documentation to be sure to make this work.

::::

::::{tab-set}

:::{tab-item} Windows

Some guidance on how to use SSH on Windows can be found on the [Windows website](https://learn.microsoft.com/en-us/windows/terminal/tutorials/ssh#access-windows-ssh-client-and-ssh-server), and how to use SSH keys is described on the [Github documentation site](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases) - which does not just apply to connecting to Github.com, but for most other SSH connections as well.  

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


## Setting up SSH Agent for password-less login

The SSH protocol allows you to create a public-private key pair. You deposit the public key on the Linux server, and keep the (passphrase-protected) private key on your laptop. You can then configure a "ssh-agent" running on your laptop to store (temporarily) your passphrase in memory, and provide it every time you log in to the Linux server. This works very easily on Unix-like systems (macOS, Linux laptops), but is a bit trickier on Windows laptops. The same mechanism can be used to authenticate with Github, Bitbucket, and other services, seemlessly integrating login to BioHPC with the use of Git.

:::{tip}

You might want to read the 

- [Carpentries tutorial on SSH](https://carpentries-incubator.github.io/shell-extras/02-ssh/).
- [Github tutorial on SSH](https://docs.github.com/en/enterprise-cloud@latest/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [Atlassian tutorial on SSH](https://support.atlassian.com/bitbucket-cloud/docs/configure-ssh-and-two-step-verification/)

:::


:::::{tab-set}

::::{tab-item} macOS

Most macOS installations come with SSH installed and the SSH agent active. You can check if it is running with 

```bash
eval $(ssh-agent -s)
```

If it is not running, you may need to add the command above to your shell startup file (either `~/.bashrc`, `~/.zshrc`, or `~/.profile`).

For more details, have a look at [Atlassian's tutorial](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-macos/). 

::::

::::{tab-item} Linux (laptop)

Most Linux distributions come with an SSH agent installed. You can check if it is running with the command:

```bash
eval $(ssh-agent -s)
```

This should return a number, which is the process ID of the SSH agent. If not, you may need to add the command above to your shell startup file (probably `~/.bashrc`, possibly `~/.profile`).

::::

::::{tab-item} Windows (laptop only)

First, you need to ensure that the SSH subsystem is installed. Consult [Atlassian's tutorial](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-windows/). You will likely need admin privileges on your laptop.


::::

:::::

The next steps are common to all operating systems.

You will want to create a key, then transfer it to the Linux server. The following commands should do this (should work on both Powershell and Bash):

```bash
ssh-keygen -t ed25519 -C "For BioHPC"
```

`ed25519` is the encryption type, "For BioHPC" is an arbitrary comment for your own tracking of keys. When prompted, you should add a passphrase.

This should have created two files in your `.ssh` directory:

```bash
ls $HOME/.ssh
```

Add the new key to your SSH-agent (optional, but recommended):

```bash
ssh-add $HOME/.ssh/id_ed25519.pub
# you should be prompted for your passphrase.
```

You will want to transfer the `.pub` file to the Linux server. You can do this with the command `ssh-copy-id` (if it exists), or manually. We show you how to do this manually:

:::{note}

You should open two terminals: one locally on your laptop, one remotely on the Windows server!

:::


This creates a directory used by SSH:

```bash
# this is on the remote server
mkdir $HOME/.ssh
# stay logged in!
```

This transfer the public key to the remote Linux server

```bash
# this is on your laptop
netid=lv39  # adjust to be your own netid!
scp $HOME/.ssh/*.pub ${netid}@cbsulogin.biohpc.cornell.edu:.ssh/
```

You should now see the `.pub` key in your `.ssh` directory on the remote Linux server:

```bash
# this is on the remote server
# this should show the *.pub file
ls $HOME/.ssh
# this authorizes you to use the SSH key to log in:
cat $HOME/.ssh/*.pub >> $HOME/.ssh/authorized_keys
```

Now test it:

```bash
# This is run from your laptop
ssh ${netid}@cbsulogin.biohpc.cornell.edu
```

You should now be prompted for your SSH passphrase:

```
$ ssh netid@cbsulogin.biohpc.cornell.edu
Enter passphrase for key `C:\Users\netid\.ssh\id_ed15559.pub`:
```

If you have `ssh-agent` running, subsequent logins should NOT prompt you for the passphrase again!


:::{tip}

Use `tmux` to allow for terminal sessions that you can disconnect and reconnect from.

:::
