---
layout: post
title: "NanoGrid - Idea"
date: 2025-12-27
---

Welcome to my NanoGrid project. Here you will find all information about this project.

## Vision

Having a `K8s cluster` running on `multiple Raspberry Pi nodes` with the `least amount of manual setup` in `self printed casing`.

## Architecture

The following diagram shows the initial setup of a cluster that has a single-node K3s server with an embedded SQLite database. Two K3s agents are registered to teh server node.

![K3s Architecture]({{ "/assets/images/k3s_architecture.drawio.png" | relative_url }})

## Hosted Applications

The following applications should be hosted on the k8s cluster.

* [Wiki-js](https://js.wiki)
* [Pi-Hole](https://pi-hole.net)
* [Github Runner](https://docs.github.com/de/actions/using-github-hosted-runners/using-github-hosted-runners)
* [Bitwarden/Vaultwarden](https://www.vaultwarden.net)
* Bind9
* Mail Server
