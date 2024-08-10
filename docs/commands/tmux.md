(tmux)=
# TMux

```{tip}

- Cheatsheet: https://gist.github.com/MohamedAlaa/2961058
- [tmux guide](https://github.com/tmux/tmux/wiki/Getting-Started)

```

1. Login via [SSH](ssh) or [VSCode](vscode)
2. Launch tmux with a session name that makes sense, e.g. `tmux new -s xxxx`
3. Launch your Matlab, Stata, etc job
4. Disconnect from tmux: `ctrl-b d`. You don't need to press this both Keyboard shortcut at a time. First press "Ctrl+b" and then press "d".
5. Log out of SSH

Next time:

1. Login via SSH
2. List your tmux sessions, `tmux ls`
3. Reconnect to your tmux session: `tmux a -t xxxx`

To save the output of a `tmux` session to a file, see [https://unix.stackexchange.com/questions/26548/write-all-tmux-scrollback-to-a-file](https://unix.stackexchange.com/questions/26548/write-all-tmux-scrollback-to-a-file).


