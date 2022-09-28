---
title: "Nomad Homelab"
date: 2022-09-27T20:35:39-05:00
draft: false

slug: nomad-homelab # Will take the filename as the slug.

# Lists
keywords: ["hashicorp", "nomad", "homelab", "madscience"]
tags: ["madscience", "homelab", "hashicorp", "nomad"]
categories: ["home automation", "hashicorp", "nomad", "homelab"]
---

{{ $image := .Resources.GetMatch "nomad-clients.jpeg" }}
{{ $image.Resize "x400" }}

In my ongoing quest to become a mad scientist, I realized I had a bunch of compute sitting around my home, largely unused. I could try to choose what runs on which machine and track and manage it all...

<div class="tenor-gif-embed" data-postid="13199396" data-share-method="host" data-aspect-ratio="1.77914" data-width="100%"><a href="https://tenor.com/view/why-huh-but-why-gif-13199396">Why Huh GIF</a>from <a href="https://tenor.com/search/why-gifs">Why GIFs</a></div> <script type="text/javascript" async src="https://tenor.com/embed.js"></script>

So instead, I embarked on a quest to make it all part of one big Nomad cluster at home. The Nomad server is a single qemu VM running on my Synology NAS system, which is also on a UPS to keep it running until my generator kicks on. Since [Hashicorp Nomad](https://nomadproject.io) separates the notion of server (orchestrator) and client (worker), that single VM is all I really need to schedule basically any workloads I need to at home.

What's on each client?

| Name             | Specs                                  | Service Scheduler |
| ---------------- | -------------------------------------- | ----------------- |
| emilys-imac      | M1 iMac with Docker Desktop            | launchd           |
| Prometheus       | WSL2 running Ubuntu 20.04              | init.d            |
| fury             | M1 MacBook Air laptop                  | launchd           |
| sisyphus         | Raspberry Pi 3B print server           | systemd           |
| athena           | Old Ubuntu 20.04 laptop                | systemd           |
| Prometheus-Win11 | Windows 11 Gaming Laptop with a 3070ti | Windows Service   |

The laptops have an `environment:Laptop` [meta setting](https://www.nomadproject.io/docs/configuration/client#custom-metadata-network-speed-and-node-class) so I can specify not to use those for jobs that need to be more persistent, although it probably doesn't matter because if a container fails it'll get rescheduled onto available compute.

So where do we go from here? Well, I have ideas...

I've got several Raspberry Pi boards sitting around waiting to be brought to life, which I plan to manage via Ansible and add to this cluster. I'm considering keeping the Raspberry Pis in a separate DC just for consistency's sake, but there's probably no reason for it. I also have a small stack of Raspberry Pi Zero Ws but I discovered the hard way that there isn't a binary for that architecture. Eventually I need to figure out how to compile it so I can add those to the cluster for ad-hoc jobs.

As for actual services, I need to write some code! I have some thoughts on writing some queue-based services that integrate with [Mycroft AI](https://mycroft.ai) and my [home automation via Home Assistant](https://www.home-assistant.io/). I also have some cron tasks and batch jobs I've been thinking of adding, plus a Discord bot, which could all find a home on this cluster instead of in the cloud.

I'll keep folks posted!
