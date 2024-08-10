(vscode)=
# VSCode

Visual Studio Code is a free application maintained by Microsoft. It is a highly efficient editor, allowing to edit any modern statistical software code, develop Python applications, etc.

You can connect to BioHPC via SSH using VSCode - running the application locally on your laptop, but editing and running code remotely on the BioHPC cluster. Generic instructions are available at [VS Code documentation](https://code.visualstudio.com/docs/remote/ssh).


- Check that you have installed the [Remote-SSH extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) on VSCode.
- Open VSCode and select the Remote-SSH extension from the Command Palette.
- Enter the host name when prompted. The host name should follow this naming convention: 
  - BioHPC: "netid@cbsuecco##.biohpc.cornell.edu".
- You may be prompted to "Select the platform of the remote host". If so, select the "Linux" option in the drop down menu.

```{tip}
For this to work on BioHPC, verify that you have a valid reservation and an active VPN! 
```

- Enter your account password when prompted.
- Once connected, 
  - `Open Folder` and navigate to your working directory.
  - open a new terminal using the "Terminal" option in the top menu of VSCode (or `Ctrl-\``).
- You should now be able to work on the Linux server via command line. 

Some benefits of connecting to BioHPC with VSCode: You can view/edit programs, check log files, and run jobs simultaneously in a given instance of VSCode. Note that you should still use [`tmux`](tmux) within the VSCode terminal, in case of a disconnect. 

In particular, you can navigate to your working directory and `git clone` a Git repository (using either the command line, or VSCode prompt to `Clone Repository`). VSCode recognizes Git, so you can visually navigate through tracked and untracked files via the lefthand side menu.

For more information, see [VSCode Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh).

```{tip}
A tutorial video (thanks to Lars' former RA Ilona Khimey) is available at [Cornell Video-on-demand](https://vod.video.cornell.edu/media/BioHPC+Walkthrough+%28Mac%29/1_x2tmxhk9).
```


