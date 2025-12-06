(installing-packages)=

# Installing Packages

**Goal:** Get the base software we need to run JupyterHub.

## Install Python and npm

```bash
sudo apt update && sudo apt dist-upgrade
sudo apt install -y npm python3 python3-dev python3-full
```

### Verification:

```bash
python3 --version
npm --version
```

```
Python 3.13.5
9.2.0
```

We now have the runtimes for Python and Node.js
and can start installing the packages we need.

## Install a Proxy

We are going to start with the default proxy implementation,
which is [configurable-http-proxy](https://github.com/jupyterhub/configurable-http-proxy), a Node.js-based proxy implementation.

```bash
sudo npm install -g configurable-http-proxy
```

### Verification:

```bash
configurable-http-proxy --version
```

```
5.1.0
```

## Install JupyterHub and JupyterLab

### Create a virtual environment

First we create a virtual environment under `/opt/jupyterhub`.
The `/opt` folder is where apps not belonging to the operating system are [commonly installed](https://unix.stackexchange.com/questions/11544/what-is-the-difference-between-opt-and-usr-local).
Both jupyterlab and jupyterhub will be installed into this virtualenv.
Create it with the command:

```
sudo python3 -m venv /opt/jupyterhub
```

Note that we use `/opt/jupyterhub/bin/python3 -m pip install` each time - this [makes sure](https://snarky.ca/why-you-should-use-python-m-pip/)
that the packages are installed to the correct virtual environment.

From now on, we expect to be running in the Hub environment, via:

```
source /opt/jupyterhub/bin/activate
```

#### Verification

```
which python
which pip
pip --version
```

```
/opt/jupyterhub/bin/python
/opt/jupyterhub/bin/pip
pip 25.1.1 from /opt/jupyterhub/lib/python3.13/site-packages/pip (python 3.13)
```

### Install JupyterHub and JupyterLab

Now we can install JupyterHub and JupyterLab in the `/opt/jupyterhub` env with `pip`:

```bash
# (/opt/jupyterhub)
sudo $VIRTUAL_ENV/bin/python3 -m pip install --upgrade jupyterhub jupyterlab
```

### Verification:

```bash
jupyterhub --version
jupyter lab --version
```

```
5.4.2
4.5.0
```

______________________________________________________________________

## Key Concepts

### Where are we now?

We have:

- Runtimes for Python and Node.js, which are needed to run JupyterHub
- The proxy used by JupyterHub (configurable-http-proxy, a node.js package)
- A virtual environment at `/opt/jupyterhub` containing JupyterHub itself and JupyterLab

This is everything that we need to run a basic installation of JupyterHub.

### Alternatives

There are approximately one million tools for installing Python or Node.js and managing environments.
In this case, we are using `apt` to get the two runtimes and standard Python `venv` to create a virtualenv for installing our Python packages.

You can also use conda, mamba, pixi, asdf, nix, pyenv, uv, or whatever tool you like to do the same.
The main end goal is that we have:

1. `jupyterhub` and `jupyterlab` installed in an env
2. `configurable-http-proxy` installed
3. the executables `configurable-http-proxy`, `jupyterhub`, `jupyterhub-singleuser` on our \$PATH

```

Other paths:

- If we skip straight to [traefik](proxy), then we wouldn't need Node,js/NPM at all.


______________________________________________________________________

## Extend your learning

______________________________________________________________________

Next: [](#starting-the-hub)
```
