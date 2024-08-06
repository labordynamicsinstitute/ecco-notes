(transfer)=
# Transferring data to and from BioHPC

See [BioHPC document](https://biohpc.cornell.edu/lab/doc/accessing_data_on_BioHPC.pdf). 

See also [`rclone`](https://biohpc.cornell.edu/lab/userguide.aspx?a=software&i=665#c).

## Pushing data to BioHPC

There are a few ways to push to or synchronize with BioHPC storage/filesystem.

### Using VS Code

You can drag-and-drop into the file pane in VS Code, or upload from the menu. See [VS Code Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh).

### Using terminal on your laptop

#### SFTP

(for Mac and Linux especially) Use the built-in SFTP/SCP command line client (comes with SSH), or any of the graphical frontends. 

```bash
scp fancydata.dta netid@cbsulogin.biohpc.cornell.edu:/path/to/where/you/need/it/
```

#### Rsync

Install rsync and sync back and forth, preferably over SSH (bidirectional)

```bash
rsync -auvz /my/laptop/path/ netid@cbsulogin.biohpc.cornell.edu:/path/to/where/you/need/it/
```

#### Git LFS

If using Git for project code, you can configure Git LFS on all of your machines to handle data transfers.

## Initiating from a BioHPC program or terminal

### Dropbox 

#### Bi-directional

Dropbox is usually not installable on BioHPC. The [unofficial command line tool](https://github.com/dropbox/dbxcli) can be used for one-off transfers (follow instructions for Mac OSX installation, replacing `dbxcli-darwin-amd64` with `dbxcli-linux-amd64`). You can then use `dbxcli` to download or upload data from your Dropbox account.

#### Download only

Alternatively, you can find the download link for single files, e.g. `https://www.dropbox.com/scl/fi/vv8wujjdhxislw15sfmbng1a/fancydata.dta?rlkey=d2x4ehh46rjxqe8dhsp89vv53&st=2x47iv6s&dl=0` and then use the command line `wget` to download it directly (no upload). Replace the `dl=0` at the end of the URL with `dl=1`.

```bash
wget -o fancydata.dta "https://www.dropbox.com/scl/fi/vv8wujjdhxislw15sfmbng1a/fancydata.dta?rlkey=d2x4ehh46rjxqe8dhsp89vv53&st=2x47iv6s&dl=1"
```

### Box

Similar to the "Download only" Dropbox method, you can use a URL to download specific Box-hosted files. 

- Find the "Link" icon, and click on it.
- Do not use the "Share Link". Rather, click on "Link Settings"
- At the bottom of the display, use the "Direct Link" URL.
- Use `wget` to download.

```bash
wget -o fancydata.dta "https://cornell.box.com/shared/static/q0vmkhzfd8mrcl9wzgeub5x74a8yoh8u.dta"
```

### Using APIs

All major cloud services have APIs that can be used. 

#### R

- For Box: [`boxr`](https://r-box.github.io/boxr/index.html)
- For Dropbox: [`rdrop2`](https://github.com/karthik/rdrop2)

#### Python

- For Box:
- For Dropbox: [Official Dropbox SDK](https://github.com/dropbox/dropbox-sdk-python)
