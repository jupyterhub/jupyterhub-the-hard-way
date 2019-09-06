# Using a Different Proxy (Traefik)

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
c.JupyterHub.proxy_class = 'traefik_toml'
```

Generate a secret using `openssl rand -hex 32`.

```python
c.TraefikTomlProxy.traefik_api_username = 'traefik'
c.TraefikTomlProxy.traefik_api_password = 'place_secret_here'
```

Restart JupyterHub.

Traefik is now running. JupyterHub is starting the proxy.



Potential discussion points:
* Discuss what a proxy is
* Diagram of different routes

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

Next: [Setting Up HTTPS](08-setting-up-HTTPS.md)