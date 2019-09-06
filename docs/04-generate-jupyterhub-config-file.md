# Generate a JupyterHub Configuration File

In this lab, you will:
- create a JupyterHub configuration file, `jupyterhub_config.py`
- add an admin
- explore the JupyterHub admin user interface

## JupyterHub configuration file

The JupyterHub configuration file, `jupyterhub_config.py`, contains
settings for users, admins, spawners, authenticators, proxies, and
hub maintenance. The file uses the `traitlets` library to add
configurable traits or settings.

### Generate a JupyterHub configuration file

To generate the configuration file:

```bash
mkdir hub
cd hub
jupyterhub --generate-config
```
A `jupyterhub_config.py` file has been created.

### Inspect the configuration file

As a typical Python file, `jupyterhub_config.py` can include any valid
Python commands. When JupyterHub starts, the configuration file is executed.

The configuration file using this format to represent an application and its
configurable traits: `c.application_to_be_configured.trait = setting`

### Tip

If you install a custom spawner or authenticator, running `jupyterhub --generate-config` will create a configuration file that has default
settings for the custom spawner or authenticator.

## Add an administrator for JupyterHub

In the configuration file, find the line with `Authenticator.admin_users`.
Set the `Authenticator.admin_users` to a set of usernames to grant admin
access. For example, let's grant the `hardway_admin` user rights to administer
JupyterHub:

```python
#------------------------------------------------------------------------------
# Authenticator(LoggingConfigurable) configuration
#------------------------------------------------------------------------------

## Base class for implementing an authentication provider for JupyterHub

## Set of users that will have admin rights on this JupyterHub.
#
#  Admin users have extra privileges:
#   - Use the admin panel to see list of users logged in
#   - Add / remove users in some authenticators
#   - Restart / halt the hub
#   - Start / stop users' single-user servers
#   - Can access each individual users' single-user server (if configured)
#
#  Admin access should be treated the same way root access is.
#
#  Defaults to an empty set, in which case no user has admin access.
c.Authenticator.admin_users = { 'hardway_admin' }
```

## Restart JupyterHub

For configuration changes to take effect, the hub must be restarted.

If the hub is running, stop the hub using *Control + C* in the terminal.

Run `jupyterhub`.

## JupyterHub's admin user interface

From the navbar in JupyterHub, access administrative options through the `Admin` tab.

The JupyterHub admin user interface allows admins to monitor, start, and stop
a user's notebook server.

---

## Key Concepts

* JupyterHub has a configuration file, `jupyterhub_config.py`. It is a Python script.
* A configuration item is represented by the format `c.application_to_be_configured.trait = setting`.
* The JupyterHub configuration file allows users to be given rights as an admin.
* The JupyterHub admin user interface allows admins to monitor, start, and stop
  a user's notebook server.

---

## Extend your learning

*TODO* Add a whitelist, port, admin

---

Next: [Spawning Notebook Servers](05-spawning-notebook-servers.md)
