---
title: "Secure and Low-latency SSH Connection: Setting Up via Tailscale"
date: 2023-12-20T00:54:00+11:00
# weight: 1
# aliases: ["/first"]
tags: ["Tailscale", "SSH", "VPN", "working remotely"]
author: "Jeremiah Hsing"
# author: ["Me", "You"] # multiple authors
cover:
  image: "<image path/url>" # image path/url
  alt: "<alt text>" # alt text
  caption: "<text>" # display caption under cover
  relative: false # when using page bundles set this to true
  hidden: true # only hide on current single page
categories: ["Coding Setup"]
draft: false
---

## Introduction

In our interconnected digital age, it's crucial to access our devices securely from any location. This guide will walk you through setting up a VPN with Tailscale, facilitating a seamless and secure connection between an Ubuntu desktop (acting as the server) and a MacBook.

For those in a hurry, jump straight to the instructions. If you're curious, here are some Q&As:

- [What is SSH?](https://www.techtarget.com/searchsecurity/definition/Secure-Shell)
- [How does SSH help us develop remotely?](https://code.visualstudio.com/docs/remote/ssh)
![ssh](/images/post/0/ssh.png)
- [What is a VPN?](https://en.wikipedia.org/wiki/Virtual_private_network)
- [Why use SSH and VPN in combination?](https://networkengineering.stackexchange.com/questions/23959/why-use-ssh-and-vpn-in-combination)

Setting this up might seem daunting, so I use [Tailscale](tailscale.com) to make my life easier.

## Install Tailscale

First, install Tailscale on both your SSH server and client.
MacBook users take note: There are [three methods to run Tailscale on macOS](https://tailscale.com/kb/1065/macos-variants#automating-app-store-installs). For an enhanced user experience, please install the [open-source Tailscale & Tailscaled CLI version](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS). The GUI version, unfortunately, doesn't operate in the background.

All the installation instructions can be found [here](https://tailscale.com/download).

## Connect Your Devices and Set Up Tailscale SSH

After installation, connecting your devices is straightforward. Just follow the on-screen instructions.

This guide uses Tailscale's SSH service for the connection, which is both user-friendly and secure. Dive deeper into the nuances of Tailscale SSH.

However, if you prefer, you can use the OpenSSH Server and client paired with the Tailscale VPN instead of Tailscale's SSH service. This approach is slightly trickier to configure securely.

On Ubuntu:
```bash
sudo tailscale up --ssh
```
On Mac (with the Tailscale CLI version):
```bash
tailscale up
```
Once you've linked the devices, you'll see an output resembling the following:
![tailscale_dashboard](/images/post/0/tailscale_dashboard.png)
The IP address is auto-allocated by Tailscale once your device gets connected. (and for sure it’s not 127.0.0.1 Lol)

## Connect Your Devices in IDE

Once you get there, in your client device terminal, use the below command to connect:
```bash
ssh <username>@<server device vpn ip adress>
```
Replace \<username\> with a valid user from your SSH server system.

If you've managed to connect successfully using SSH, you might want to set up an SSH connection within your favorite IDE for a smoother remote development experience. Personally, I use vscode, and you can find setup instructions here.

Here's a glimpse of the vscode SSH connection terminal on my laptop:

![vsc_ssh](/images/post/0/vscode_ssh.png)