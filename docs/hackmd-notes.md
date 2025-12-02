# JupyterHub the Hard Way

This tutorial roughly follows a conceptual TLJH-like deployment and its components. The goal of this tutorial is to give you an understanding of JupyterHub components and their configuration. Ultimately, the tutorial would guide you in how to create an authenticator, spawner or proxy.

[TOC]

## Notes

Assumptions:

- Ubuntu latest LTS
- Python? Conda?
- What assumptions are in the commands we're writing?
  - ports
  - sudo access (probably a reasonable assumption though)
  - assuming linux dist (make notes for macs)
  - gcloud use option -L 8000:127.0.0.1:8000 to make localhost to pass no credentials over http (insecure)
    - Windows??
- Running on a server with a real domain name pointing to it

Future:

- Flavor UI for steps with differences (apt, yum, mac)
  - eg, [`kubectl` installation guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Open questions:

- Cloud vs local setup
- Right order for introducing Spawners?

______________________________________________________________________

## Installation

### Install Python, npm

```
sudo apt update && sudo apt dist-upgrade && sudo apt-get install -y npm python3 python3-dev python3-pip
```

```
sudo python3 -m pip install --upgrade pip
```

Install a proxy:

```
sudo npm install -g configurable-http-proxy
```

Install JupyterHub and notebook:

```
sudo python3 -m pip install --upgrade jupyterhub notebook
```

Started JupyterHub on a port

Authenticate JupyterHub user password (PAM)

Enter username/password launched a server

**Task** Point out the log in the terminal

**Task** Unpack the URL server/user/username/tree

Proxy is started when typing JupyterHub. In this case, the configurable-http-proxy is started.

**Task** Click Control Panel
\- Start and Stop Server for the User

**Concept** JupyterHub is not different than Jupyter notebook and JupyterLab. It's a tool for deploying those on behalf of users.

At this point, no admin stuff has been discussed.

**Task** Control-C stop JupyterHub

Modification: Set up an ssh tunnel [See assumptions above]

## Configuring JupyterHub

Three extension points: spawner, authenticator, proxy

### Create a config file

```
mkdir hub
cd hub
jupyterhub --generate-config
```

A config has been generated.

**Task** Inspect the configuration file `jupyterhub_config.py`

### Add an admin user

Find line with `admin_users` and create a set with `{ your_username}`

**Concept** JupyterHub has an admin user.

**Learned** JupyterHub has a config file. It is a Python script. `c.thing_configuring.trait = setting`

**Result** Made yourself an admin user.

*TODO* Add a whitelist, port, admin

## Spawners

**TODO:** Talk about what a spawner is. How to write a Spawner.

Install DockerSpawner and Docker.

Set docker group! - Make sure you have permission to use Docker!

```
sudo usermod -aG docker `whoami`  # Add me to group called docker
newgrp docker  # Start new shell where docker group is activated
```

In `jupyterhub_config` set `SpawnerClass`: `c.JupyterHub.spawner_class = 'docker'`

Start JupyterHub again

Visit and click Start My Server

New spawn may take some time since it's pulling the Docker image down.

Error message: When visiting a notebook server, the proxy sends the message to the serve, the server communicates with the Hub to verify the authentication. In order to do this, the single-user server needs to talk to the hub, and by default the hub only listens on localhost. Need to reconfigure so that the hub is accessible to something outside for Docker (i.e. remote spawner). Set `c.JupyterHub.hub_ip`.

```python
import netifaces

docker_iface = netifaces.ifaddresses("docker0")
c.JupyterHub.hub_ip = docker_iface[netifaces.AF_INET][0]["addr"]
```

Delete the Container and restart JupyterHub to use new config.

By default, containers are only started and stopped (not deleted) and so they persist part of the configuration.

**Task** Create a notebook to see the changes made by DockerSpawner to username as Jovyan.

```
!hostname

!whoami

%pip list
```

**Concepts**
Everyone using JupyterHub wants to expose some computational resources for use. Two scenarios:

- Just expose an access point. Users are power users and can look after their own environments.
- Person deploying the JupyterHub controls the user environment - done by a Docker image.

* Configuration of JupyterHub and restart.
* Configuration of the spawner

**TODO:** Build a single-user image from scratch, not using a stack

**Learned** Single user servers need to talk to the hub. When Docker added, configuration change is needed to take into account the network location of Docker. Now there are two things to configure DockerSpawner and JupyterHub
DockerSpawner is unique that restart of JupyterHub is not enough. Sensible default?

**Result**

## Configuring our chosen Spawner

Choose a new image from docker-stacks

The logs showed that we were pulling the image from dockerhub.

There's lots to configure.

**Recap until here**
We've learned about JupyterHub, proxy, hub, spawner

## Setting up Traefik Proxy

Let's use the Traefik proxy instead of configurable-http-proxy.

Install the Traefik Python API:

```bash
python3 -m pip install jupyterhub-traefik-proxy
```

Note: traefik is a binary. JupyterHub Traefik Proxy PTrTython package doesn't install traefik but has a handy command to bootstrap it.[Traefik](https://traefik.io/) is installed.

Downloads the Traefik binary (any other method of downloading this binary is fine!):

```bash
sudo python3 -m jupyterhub_traefik_proxy.install --output=/usr/local/bin
```

Tell JupyterHub to use Traefik for the reverse proxy.

Edit `jupyterhub_config.py`:

```python
c.JupyterHub.proxy_class = "traefik_toml"
```

Generate a secret using `openssl rand -hex 32`.

```python
c.TraefikTomlProxy.traefik_api_username = "traefik"
c.TraefikTomlProxy.traefik_api_password = "place_secret_here"
```

Restart JupyterHub.

Traefik is now running. JupyterHub is starting the proxy.

Potential discussion points:

- Discuss what a proxy is
- Diagram of different routes

JupyterHub is a tool for starting endpoints and creating a proxy to route requests.

The proxy is a customisable endpoint.

- show current routes
- add a new route
- remove an existing route

Routing must be done on a longest prefix basis. Route for / and everything else is below. Let JupyterHub have a base route and user routes adds specificity.

**Concepts:**

**Learned:**

Run the proxy external to the hub.
Restarting jupyterhub restarts everything. So that users can still talk to their running servers without involving the hub while it restarts, we want to run the proxy external to the hub.

**Results:**

## Set up HTTPS using Let's Encrypt with Traefik

```
sudo apt install apache2-utils
```

When we started JupyterHub earlier, JupyterHub was starting the Hub and the proxy.

systemd can start and stop processes.

Let's use systemd instead of JupyterHub to start the Traefik proxy. This allows modifications of the proxy to be done independent of a running JupyterHub.

Want to create a systemd service for the proxy.

Create `/etc/systemd/jupyter-proxy.service`:

```ini
[Unit]
# Wait for network stack to be fully up before starting proxy
After=network.target

[Service]
User=root
Restart=always
ProtectHome=yes

ProtectSystem=strict
PrivateTmp=yes
PrivateDevices=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ReadWritePaths=/srv/jupyterhub/proxy
WorkingDirectory=/srv/jupyterhub/proxy
ExecStart=/usr/local/bin/traefik -c /srv/jupyterhub/proxy/traefik.toml

[Install]
# Start service when system boots
WantedBy=multi-user.target
```

This configuration will run the service as root. The configuration above prevents from having permissions to write things other than Traefik proxy.

Make the directory:
`mkdir -p /srv/jupyterhub/proxy/`

Edit file: `/srv/jupyterhub/proxy/traefik.toml`

[Copy and paste from TLJH]

```toml
defaultEntryPoints = ["http", "https"]

logLevel = "INFO"
# log errors, which could be proxy errors
[accessLog]
format = "json"
[accessLog.filters]
statusCodes = ["500-999"]

[accessLog.fields.headers]
[accessLog.fields.headers.names]
Authorization = "redact"
Cookie = "redact"
Set-Cookie = "redact"
X-Xsrftoken = "redact"

[respondingTimeouts]
idleTimeout = "10m0s"

[entryPoints]
  [entryPoints.http]
  address = ":8000"

  [entryPoints.https]
  address = ":443"
  [entryPoints.auth_api]
  address = "127.0.0.1:8099"
  [entryPoints.auth_api.whiteList]
  sourceRange = ['127.0.0.1']
  [entryPoints.auth_api.auth.basic]
  users = ['traefik:$apr1$...']

[wss]
protocol = "http"

[api]
dashboard = true
entrypoint = "auth_api"

[file]
filename = "rules.toml"
watch = true
```

Above should have the same config as the http one.

We modify `users = ` to use the Traefik user and password. We run:

```bash
htpasswd -bn $traefik_api_username $traefik_api_password
```

Take the output and enter in the `users = ['xxxx']` line in the `traefik.toml` file.

Reload systemd to find the new service:

```bash
sudo systemctl daemon-reload
```

Update `jupyterhub_config.py`:

- to not start the Traefik proxy `c.TraefikTomlProxy.should_start = False`
- add `c.TraefikTomlProxy.toml_dynamic_config_file = '/srv/jupyterhub/proxy/rules.toml'`

```bash
sudo systemctl start jupyterhub-proxy
```

logs:

```bash
journalctl -u jupyterhub-proxy
```

Start jupyterhub and we should have the same config as before, but traefik is now running even when the Hub is not.

### Set up DNS

Add a DNS 'A' record using the external IP for the instance and let resolve.

Update entrypoints in traefik.toml:

```toml
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"

  [entryPoints.https]
  address = ":443"
  [entryPoints.https.tls]
```

and add acme section:

```toml
# configure ACME with letsencrypt.org
[acme]
email = "you@email.com"
storage = "acme.json"
entryPoint = "https"
  [acme.httpChallenge]
  entryPoint = "http"

[[acme.domains]]
  main = "your.domain.horse"
```

Restart the Traefik proxy.

```bash
sudo systemctl restart jupyterhub-proxy
```

and restart the Hub (if it isn't running)

Now, your Hub is accessible at `https://your.domain.horse`.

### Set up the Hub with systemd

Let's do this now so that the Hub also uses systemd

Set up jupyterhub service with a dependency on jupyterhub-proxy.

Create a `jupyterhub.service` file in /etc/systemd/system/.

```ini
[Unit]
# The proxy must have successfully started *before* we launch JupyterHub
Requires=jupyterhub-proxy.service
After=jupyterhub-proxy.service

[Service]
User=root
Restart=always
# jupyterhub process should have no access to home directories
ProtectHome=yes
WorkingDirectory=/srv/jupyterhub
# Protect bits that are normally shared across the system
PrivateTmp=yes
PrivateDevices=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
# Run upgrade-db before starting, in case Hub version has changed
# This is a no-op when no db exists or no upgrades are needed
ExecStart=/usr/bin/python3 -m jupyterhub -f /srv/jupyterhub/jupyterhub_config.py

[Install]
# Start service when system boots
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload && sudo systemctl restart jupyterhub
```

### Using journalctl to view the logs

## Authenticators

Using GitHub OAuth - needs to use public accessible server but will require HTTPS. (We set up HTTPS above.)

### Set up GitHub Auth

```bash
sudo python3 -m pip install oauthenticator
```

Configure authenticator in `jupyterhub_config.py`:

GitHub set up of Oauth
https://github.com/settings/apps/new

- Developer settings
- Oauth App
  - Give a name jupyterhub the hard way
  - Homepage url https://hardway.jupyter.org
  - callback url https://hardway.jupyter.org/

Add Oauth info to `jupyterhub_config.py`

```bash
sudo apt install libssl-dev       libcurl4-openssl-dev
sudo python3 -m pip install pycurl
```

Note: above is a Tornado bug that seems to come up with GitHub Oauth.
