# Spawning Notebook Servers

## Select a Spawner

## DockerSpawner

### Install DockerSpawner

Using DockerSpawner

netifaces - python convenience library
local GH to general GH users
GH - DockerSpawner and whitelist
0:44:00 Initially missing piece Hub API if cookie is valid

docker0 ip address netifaces
0:48:00 Lots you can do with DockerSpawner

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

______________________________________________________________________

## Key Concepts

Everyone using JupyterHub wants to expose some computational resources for use. Two scenarios:

- Just expose an access point. Users are power users and can look after their own environments.
- Person deploying the JupyterHub controls the user environment - done by a Docker image.

* Configuration of JupyterHub and restart.
* Configuration of the spawner

**Learned** Single user servers need to talk to the hub. When Docker added, configuration change is needed to take into account the network location of Docker. Now there are two things to configure DockerSpawner and JupyterHub
DockerSpawner is unique that restart of JupyterHub is not enough. Sensible default?

______________________________________________________________________

## Extend your learning

**TODO:** Build a single-user image from scratch, not using a stack

______________________________________________________________________

Next: [Configuring the Chosen Spawner](06-configuring-the-chosen-spawner.md)
