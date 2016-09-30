---
title: "Server recovery and update"
date: 2016-09-30 12:18:50 -0400
---

Recently you may have noticed that the webpage had this cool new look.
![SSE2.0](/assets/images/sse2-0.png)

Long story short, a planned outage turning into the boot drive booting into "Emergency Mode". After some brief research into what happened the decision was to reinstall proxmox on our boot drive, all the data drives where ok. What is proxmox? What are our data drives? What is technology? We run our own server???? These are questions that some of you may be asking right now. I will answer them to the best of my abilities by walking you through the recovery process.

### Our Server
![Back back back it up](/assets/images/server_back.jpg)
The server is a 1U HP Proliant G6 series server. It has 4 HD bays, that total in 2.3TB of storage. 74GB of DDR3 ram. Powered by 2, 6 core Xeon processors. That's a lot of juice to run this webpage, what are we doing? Our site doesn't run on that hardware, it runs on a VM on there. The extra power is used to create VMs for projects and other compute based projects we may want to host that fall out of our site deploy.

### Proxmox
You can explore the updated version of [Proxmox 4.3](http://www.proxmox.com/en/news/press-releases/proxmox-ve-4-3-released) to learn more about that. In short, we use it to create and manage our VM. It also offers some host level management of the server, including security updates and network management. Network management is a dangerous thing for us... more on that later.

The recovery of our VMs and ZSF pools was very simple, the os detected the pools automatically, and the vm configurations lived there along with the backups. We just had to re add the pool to the new os install and restore the vm.

### Networking
Due to limited access to the server room, and limited rack space. We have a single network jack available to us. This means we can't use the built in management port to the server for virtualKVM and bios access. So when we run into networking issues, we're SOL. This last incident did require us to pull the server to work on it (see above image), but a network failure shouldn't require this. The solution we opted into was a serial console.


<code style="font-size: 1em;">/dev/ttyS0</code>

There is a serial access console in the server room that we can access via ssh. Setting up serial access wasn't as simple as it sounds, lots of googling. We needed to enable comm port passthrough to the os in the bios, where we also set the hardware fixed baud rate. Then added the kernel flag `console=tty1 console=ttyS0,115300n8` to enable both the normal console output and also start a tty on the serial port. Most of the googling said this could all be done from a running os, but never worked.

