# Installing Packages

**Goal:** Get the base software we need to run JupyterHub.

## Install Python and npm

```bash
sudo apt update && sudo apt dist-upgrade
sudo apt install -y npm python3 python3-dev python3-pip
sudo python3 -m pip install --upgrade pip
```

### Verification:

```bash
python3 --version
npm --version
python3 -m pip --version
```

We now have the runtimes for Python and Node.js
and can start installing the packages we need.


## Install a Proxy

```bash
sudo npm install -g configurable-http-proxy
```

### Verification:

```bash
configurable-http-proxy --version
```

## Install JupyterHub and Jupyter Notebook

```bash
sudo python3 -m pip install --upgrade jupyterhub notebook
```

### Verification:

```bash
jupyterhub -h
jupyter notebook --version
```

### Where are we now?

We have:

- Runtimes for Python and node.js, which are needed to run JupyterHub
- The proxy used by JupyerHub (configurable-http-proxy, a node.js package)
- JupyterHub itself and the Jupyter notebook

This is everything that we need to run a basic installation of JupyterHub.

Next: [Starting the Hub](03-starting-the-hub.md)
