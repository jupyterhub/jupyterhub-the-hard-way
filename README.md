# JupyterHub the Hard Way

**THIS IS A WORK IN PROGRESS**

Understanding JupyterHub by deploying it step by step.

## Motivation

We want to help you build understanding of JupyterHub and its core components: proxy, authenticator, spawner.
This tutorial goes through a series of labs to progressively customize JupyterHub beyond its defaults. The
configurability of JupyterHub enables its use in a variety of deployment settings. This benefits our users, but
also makes it difficult to recommend a one-size-fits-all deployment. By helping you understand how
JupyterHub works, we hope you will be able to confidently customize JupyterHub to suit your deployment needs.

## Project Notes

Notes from [JupyterHub September 2019 Oslo Meeting](docs/hackmd-notes.md)

## Inspiration

Inspired by [Kelsey Hightower's Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

## Labs

- [Prerequisites](docs/01-prerequisites.md)
- [Installing Packages](docs/02-installing-packages.md)
- [Starting the Hub](docs/03-starting-the-hub.md)
- [Creating a JupyterHub Configuration File](docs/04-generate-jupyterhub-config-file.md)
- [Spawning Notebook Servers](docs/05-spawning-notebook-servers.md)
- [Configuring the Chosen Spawner](docs/06-configuring-the-chosen-spawner.md)
- [Using a Different Proxy (Traefik)](docs/07-using-a-different-proxy-traefik.md)
- [Setting up HTTPS](docs/08-setting-up-HTTPS.md)
- [Using a Different Authenticator (GitHub Oauthenticator)](docs/09-using-a-different-authenticator.md)
- [Cleaning Up](docs/10-cleaning-up.md)

## Alternative walkthrough: [Install JupyterHub and JupyterLab from the ground up](docs/installation-guide-hard.md)
