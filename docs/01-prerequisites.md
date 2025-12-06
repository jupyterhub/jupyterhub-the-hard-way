(prerequisites)=

# Prerequisites

For this tutorial, you will need:

- Your own server with administrator (root) access.
  Examples:
  - a local machine
  - a local virtual machine (VM)
  - a cloud VM
  - a Virtual Private Server (VPS) anywhere
- The install will be done through the command line, e.g. via SSH.
- You will need to be able to run terminal commands on the server (e.g. via ssh) and edit files on the server (e.g. terminal-based text editor or remote file mount, git push-pull, etc.).

```{note}
This tutorial was last tested on **Debian 13** with **JupyterHub 5.4.2**.
No other Linux distributions have been tested,
but the instructions should be mostly transferrable to other distributions.
```

## Tips and notes

(ssh-tunnel)=

### Tunnel HTTP port over SSH

If you are connecting to a remote server via ssh,
add the option:

```
-L 127.0.0.1:8000:127.0.0.1:8000
```

to your ssh command, e.g.

```
ssh -L 127.0.0.1:8000:127.0.0.1:8000 my-hub-server
```

or for a Google Cloud VM:

```
gcloud compute ssh --project=myproject --ssh-flag "-L 127.0.0.1:8000:127.0.0.1:8000" my-vm-name
```

This forwards port 8000 on your local machine to port 8000 on the remote machine via the ssh tunnel.
It will enable you to use plain HTTP before we have configured HTTPS without needing to worry about leaking passwords.

Next: [](#installing-packages)
