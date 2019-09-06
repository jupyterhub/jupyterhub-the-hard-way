# Installing Packages

## Install Python and npm

```bash
sudo apt update && sudo apt dist-upgrade && sudo apt-get install -y npm python3 python3-dev python3-pip
```

Verification:

```bash
python3 --version
npm --version
```

## Install pip

```bash
sudo python3 -m pip install --upgrade pip
```

Verification:

```bash
python3 -m pip --version
```

## Install a Proxy

```bash
sudo npm install -g configurable-http-proxy
```
Verification:

```bash
configurable-http-proxy -h
```

## Install JupyterHub and Jupyter Notebook

```bash
sudo python3 -m pip install --upgrade jupyterhub notebook
```
Verification:

```bash
jupyterhub -h
jupyter notebook --version
```

Next: [Starting the Hub](03-starting-the-hub.md)