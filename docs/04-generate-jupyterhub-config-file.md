(config-file)=

# Create a JupyterHub Configuration File

In this lab, you will:

- create a JupyterHub configuration file, `jupyterhub_config.py`
- add an admin user
- explore the JupyterHub admin user interface

## JupyterHub configuration file

The JupyterHub configuration file, `jupyterhub_config.py`, contains settings for users, admins, spawners, authenticators, proxies, and hub maintenance.
The file uses the `traitlets` package to add configurable traits or settings.

### Generate a JupyterHub configuration file

First, make a private directory where JupyterHub configuration and state will live:

```bash
sudo mkdir -p /srv/jupyterhub
sudo chmod o-rwx /srv/jupyterhub
sudo chown root:adm /srv/jupyterhub
sudo chmod g+rw /srv/jupyterhub
```

```{admonition} What happened?

We made a directory `/srv/jupyterhub` which is:

1. owned by the user `root` and the group `adm` (a name for the admin group on debian, you might find `admin` or another group useful)
2. not readable by most users
3. readable and writable by members of the `adm` group
```

Now we can actually generate the config file:

```
sudo /opt/jupyterhub/bin/jupyterhub --generate-config -f /srv/jupyterhub/jupyterhub_config.py
```

```
Writing default config to: /srv/jupyterhub/jupyterhub_config.py
```

```{admonition} What happened?

JupyterHub _generated_ a default config file in `/srv/jupyterhub/jupyterhub_config.py`.
This file has no effect, but contains (commented out) all the available configuration options.
This configuration file is a _python script_,
so can contain arbitrary code,
such as reading other files, detecting local configuration or state,
iteration over lists, etc.
```

### Inspect the configuration file

As a standard Python script, `jupyterhub_config.py` can include any valid
Python commands.
When JupyterHub starts, the configuration file is executed.

At the top of a traitlets-based config file like this one, is generally the line:

```python
c = get_config()  # noqa
```

where `c` is the "configuration object," and each configuration option is represented as:

```python
c.ClassName.trait_name = value
```

See more in the [traitlets documentation](xref:traitlets#config)

```{tip}

If you install a custom spawner or authenticator, running `jupyterhub --generate-config` will create a configuration file that includes default
settings for the custom spawner or authenticator.
```

## Grant access to JupyterHub

By default, nobody has access to JupyterHub,
so the first configuration added to a JupyterHub deployment is typically who should have access.

```{note}
this is why we added `--Authenticator.allow_all=True` in the previous test step,
when we had no config file.
No user is granted access to JupyterHub without _explicit_ configuration somewhere.
```

There are a number of ways to grant access, specific to each Authenticator.
Since we are currently using the default PAM Authenticator, we have three choices:

1. [Authenticator.allow_all](xref:jupyterhub#jupyterhub.auth.Authenticator.allow_all) defers to the Authenticator, such that any user who can successfully **authenticate** will be **allowed**. This is _often_ the right choice for PAM.

   ```python
   c.Authenticator.allow_all = True
   ```

2. [Authenticator.allowed_users](xref:jupyterhub#jupyterhub.auth.Authenticator.allowed_users) can be a set of usernames, who will be allowed to access the Hub.
   Only users in this list will be allowed to login to the Hub.

   ````{caution}
   `Authenticator.allowed_users` on its own only _grants_ users access by default,
   so removing a user from this set will not remove their access unless you also set[Authenticator.allow_existing_users](xref:jupyterhub#jupyterhub.auth.Authenticator.allow_existing_users) to False.

   ```python
   c.Authenticator.allowed_users = {"kelsey", "hardway"}
   c.Authenticator.allow_existing_users = False
   ````

3. [PAMAuthenticator.allowed_groups](xref:jupyterhub#jupyterhub.auth.PAMAuthenticator.allowed_groups) lets you grant access based on system group membership.
   This is nice because you can use existing system tools to manage user access to JupyterHub,
   instead of relying on JupyterHub-specific tools.

```python
c.Authenticator.allowed_groups = {"jupyterhub-users"}
```

Let's go with the simplest and most logical version for PAM,
which also happens to be the same thing we added on the command-line before:

```python
c.Authenticator.allow_all = True
```

Now, we want JupyterHub to load this configuration.
How do we do that?

## Restart JupyterHub

For configuration changes to take effect, the hub progress must be restarted.

If the hub is running, stop the hub using *Control + C* in the terminal.

Run

```bash
sudo -D /srv/jupyterhub /opt/jupyterhub/bin/jupyterhub
```

We'll do this each time we restart JupyterHub.

## Grant some administrative privileges

JupyterHub grants permissions through a pattern called [Role-Based Access Control](https://jupyterhub.readthedocs.io/en/stable/rbac) (RBAC).
Each action a user takes in JupyterHub,
be it starting a server or viewing information about other users,
is associated with a [**scope**](https://jupyterhub.readthedocs.io/en/stable/rbac/scopes.html).

A **role** is a named collection of scopes,
and a **user** (or group or service) can be assigned one or more roles.

There is also a **super user** role, called an **admin**.
Admin users can do literally anything for anyone (much like `root` on your computer),
so it's advisable to use *limited* roles to grant just the permissions folks actually need,
and not actually create any admin users.

We're going to define a "monitor" role, who can see user activity and restart their servers,
but not take over everything.

```python
c.JupyterHub.load_roles = [
    {
        "name": "monitor",
        "scopes": [
            "admin-ui",
            "read:users",
            "list:users",
            "servers",
        ],
        "users": ["hardway-watcher"],
    }
]
```

We can also add a 'true admin':

```python
c.Authenticator.admin_users = {"jupyroot"}
```

Now restart the `jupyterhub` process for configuration to take effect.

## JupyterHub's admin user interface

From the navbar in JupyterHub, access administrative options through the `Admin` tab.

The JupyterHub admin user interface allows admins to monitor, start, and stop
a user's notebook server.

______________________________________________________________________

## Key Concepts

- JupyterHub has a configuration file, `jupyterhub_config.py`. It is a Python script.
- A configuration item is represented by the format `c.Class.trait = value`.
- The JupyterHub configuration file allows specifying which users can access JupyterHub via `Authenticator.allow_*` config.
- JupyterHub users can be granted permissions via roles.
- The JupyterHub admin user interface allows some users to monitor, start, and stop
  other users' servers.

______________________________________________________________________

## Extend your learning

*TODO* Add an allow list, configure port

______________________________________________________________________

Next: [](#spawning)
