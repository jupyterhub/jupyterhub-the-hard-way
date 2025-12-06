(https)=

# Setting up HTTPS

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

______________________________________________________________________

## Key Concepts

______________________________________________________________________

## Extend your learning

______________________________________________________________________

Next: [](#authenticator)
