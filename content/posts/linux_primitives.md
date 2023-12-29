---
title: "Linux Primitives"
date: 2023-12-29T14:00:00+11:00
# weight: 1
# aliases: ["/first"]
tags: ["linux"]
author: "Jeremiah Hsing"
# author: ["Me", "You"] # multiple authors
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
categories: ["Linux"]
draft: false
---

## In Linux, these concepts are foundational:

#### You will see lots of faces in the list (like Mounts, Processes, ...) while using containerization tools like [Docker](https://docs.docker.com/).

- **Processes**: These are running instances of programs. Linux manages them, allocating resources and scheduling their execution.
- **Mounts**: This refers to attaching a file system to a directory, enabling access to files in storage.
- **chroot/pivot_root**: These are methods to change the root directory. `chroot` changes the perceived root directory for a process, while `pivot_root` is used in system initialization, replacing the old root with a new one.
- **CoW Storage** (Copy-on-Write): A resource-management technique where system resources are shared and copied only when modified, optimizing performance and memory usage.
- **Users**: In Linux, users are entities with permissions to access and manipulate files and processes. Each user has a unique ID.
- **Namespaces**: These are used to isolate processes, ensuring they only see certain system resources. They're key in containerization, allowing safe and secure multitasking.
- **Memory Management**: Linux efficiently manages memory through techniques like paging and swapping, ensuring optimal use of RAM and storage resources for process execution.

For More Details: 
- [Linux Primitives Slides](https://docs.google.com/presentation/d/10vFQfEUvpf7qYyksNqiy-bAxcy-bvF0OnUElCOtTTRc/) (Credits and Acknowlegement: Nati Cohen, Avishai Ish-Shalom)
- [The Linux Kernel Documentation](https://docs.kernel.org/)
- Linux man page
