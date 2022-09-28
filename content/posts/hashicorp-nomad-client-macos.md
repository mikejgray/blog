---
title: "Installing Hashicorp Nomad Client on a Mac - Persistent"
date: 2022-09-25T11:15:58-05:00
draft: false

# Lists
keywords: ["hashicorp", "nomad", "homelab", "madscience", "mac", "apple"]
tags: ["madscience", "homelab", "hashicorp", "nomad", "mac", "apple"]
categories: ["home automation", "hashicorp", "nomad", "homelab", "mac", "apple"]
---

I've been setting up Hashicorp Nomad as my scheduler for my homelab, which includes numerous Raspberry Pis, a few Linux VMs, an Ubuntu laptop, and several Macs. Getting the server set up was fairly straightforward - I'm taking an iterative approach with a single bootstrapped server for now - but I had trouble finding much in the way of setting up persistent clients.

This blog is me leaving breadcrumbs for my future self and any other folks who want to take advantage of the power of Hashicorp Nomad clients on Macs in their own homelabs.

## Installing the Nomad client

This assumes you've installed Nomad via `homebrew`, [per Hashicorp's recommendation.](https://learn.hashicorp.com/tutorials/nomad/get-started-install)

## Basic client config

First, we need a place to keep our Nomad client configuration. I chose the same path as Linux, but put it wherever makes sense to you:

```bash
sudo mkdir /etc/nomad.d
sudo touch /etc/nomad.d/client.hcl
```

Next, load up your configuration:

```hcl
# Update to the datacenter you configured. dc1 is the default.
datacenter = "dc1"

# data_dir tends to be environment specific.
data_dir = "/opt/nomad/data"

client {
  enabled = true
}

server_join {
  # Update to your Nomad cluster IP. You can include multiples with comma-separation.
  # retry_join = ["192.168.1.5", "192.168.1.6"]
  retry_join = [ "192.168.1.242" ]
}

consul {
  # If you want to leverage Consul, point this at your Consul IP.
  address = "192.168.1.242:8500"
}
```

## Launchctl

Mac OS uses a tool call `launchd`, managed by `launchctl`, for persistent services. I had difficulty finding straightforward tutorials, so here's an example of the basic `.plist` file I created. Note that I'm installing this in `/Library/LaunchDaemons` which sets it up as a persistent daemon that doesn't require users to be logged in. That allows me to use my iMac, for example, as a Nomad client even when it's sitting idle.

The file path is: `/Library/LaunchDaemons/io.nomad.daemon.plist` [(borrowed from unRob on GitHub)](https://github.com/unRob/nidito/blob/2ff2a81716e192af07d17d434a17d09b26697f0e/ansible/roles/nomad-server/tasks/macos/11.6.yml).

Note that if you've installed Nomad some other way besides using `homebrew`, you'll need to edit the first item in the `array` so it points to your Nomad binary. Also, if you chose to use a different path for your config besides `/etc/nomad.d`, you'll need to update the last item in the array as well.

```xml
<!-- /Library/LaunchDaemons/io.nomad.daemon.plist -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>io.nomad.daemon</string>
    <key>Disabled</key>
    <false/>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>ProgramArguments</key>
    <array>
        <string>/opt/homebrew/bin/nomad</string>
        <string>agent</string>
        <string>-config</string>
        <string>/etc/nomad.d</string>
    </array>
</dict>
</plist>
```

Finally, use `launchctl` to load up your new daemon:

```zsh
sudo launchctl load /Library/LaunchDaemons/io.nomad.daemon.plist
sudo launchctl enable system/io.nomad.daemon
sudo launchctl start system/io.nomad.daemon
```

And there you have it! Your Mac OS machine is now joined to your Nomad cluster as a client, whether you have a user logged in or not.
