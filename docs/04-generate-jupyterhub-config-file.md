# Generate a JupyterHub Configuration File

Three extension points: spawner, authenticator, proxy

## Create a JupyterHub configuration file

```
mkdir hub
cd hub
jupyterhub --generate-config
```

A config has been generated.

**Task** Inspect the configuration file `jupyterhub_config.py`

## Add an admin user

Find line with `admin_users` and create a set with `{ your_username}`

## Restart JupyterHub

For configuration changes to take effect, the hub must be restarted.

- Stop the hub if it running

## Explore JupyterHub's admin user interface


---

## Key Concepts

* JupyterHub has an admin user.

* JupyterHub has a config file. It is a Python script. `c.thing_configuring.trait = setting`

---

*TODO* Add a whitelist, port, admin

Next: