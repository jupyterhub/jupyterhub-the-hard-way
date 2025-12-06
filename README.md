# JupyterHub the Hard Way

**THIS IS A WORK IN PROGRESS**

Understanding JupyterHub by deploying it step by step.

## Motivation

We want to help you build understanding of JupyterHub and its core components: proxy, authenticator, spawner.
This tutorial goes through a series of labs to progressively customize JupyterHub beyond its defaults.
The configurability of JupyterHub enables its use in a variety of deployment settings.
This benefits our users, but
also makes it difficult to recommend a one-size-fits-all deployment.
By helping you understand how JupyterHub works,
we hope you will be able to confidently customize JupyterHub to suit your deployment needs.

This is first a **learning tool**, and not meant to be a _final_ production-ready deployment.
What you learn following this tutorial should still be applicable when using a prepared distribution, such as [Zero to JupyterHub on Kubernetes](https://z2jh.jupyter.org) or [The Littlest JupyterHub](https://tljh.jupyter.org)

## Overview

The combination of [JupyterHub](https://jupyterhub.readthedocs.io) and [JupyterLab](https://jupyterlab.readthedocs.io)
is a great way to make shared computing resources available to a group.

These instructions are a step-by-step tutorial for a manual, 'bare metal' install of [JupyterHub](https://jupyterhub.readthedocs.io) and [JupyterLab](https://jupyterlab.readthedocs.io).
This is ideal for running on a single server: build a big machine and share it within your lab, or request a virtual machine from any VPS or cloud provider.

This guide produces similar results to [The Littlest JupyterHub](https://tljh.jupyter.org).
However, instead of bundling all these steps for you into one installer, we will perform each step manually.
This makes it easy to customize any part (e.g. if you want to run other services on the same system and need to make them work together),
as well as giving you full control and understanding of your setup.

## Goals

In this tutorial we will:

- Install packages required to run JupyterHub and JupyterLab
- Integrate JupyterHub and its proxy to be managed by the system supervisor `systemd`
- Configure JupyterHub
- Select and configure an alternative Spawner (DockerSpawner)
- Select and configure an alternative Authenticator (GitHub)
- Select and configure an alternative proxy (Traefik)
- Enable HTTPS with Let's Encrypt

all by hand.

## Labs

The Labs are listed in the table of contents on the side.
Get started with the [](#prerequisites).

## Alternative walkthrough

There is a more complete (no "TODO" items), but less detailed, from-scratch deployment doc in [](docs/installation-guide-hard.md).
This will eventually be removed when the full tutorial is more complete.

## Inspiration

Inspired by [Kelsey Hightower's Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

## Project Notes

Notes from [JupyterHub September 2019 Oslo Meeting](docs/hackmd-notes.md)
