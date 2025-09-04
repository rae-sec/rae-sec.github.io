---
layout: post
title: "Building My Proxmox Homelab"
date: 2025-09-04 12:30:00 +0300
categories: [homelab, virtualization]
tags: [proxmox, homelab, virtualization, cybersecurity, lab setup]
---

Having a lab environment has always been my top priority, and last year I finally set up my Proxmox homelab. The goal was simple: create a flexible, virtual sandbox where I could put into practice the theory and concepts I’ve learned during my Cybersecurity degree, experimenting with different operating systems, networking topologies, and security solutions without putting my main machine at risk. A secondary objective was also to apply these skills to harden my brother's NAS server.

### Hardware Overview

I used an old, old computer we had lying around. It's not great, but it gets the job done:
- CPU: 4 x Intel(R) Core(TM) i5-3340 CPU @ 3.10GHz (1 Socket)
- RAM: 16GB
- Storage: 2 x 1TB HDD, 100 GB will be allocated to the Proxmox OS, the rest will be used for the virtual machines and containers.
- Networking: Single 1Gbps NIC on a 100Mbps switch

The switch allows me to block LAN and WLAN traffic, and that is helpful to troubleshoot network settings. My future plan is to swap the switch in the near future with improved speeds and performance.

### Installing Proxmox
I installed Proxmox with ease. I downloaded the ISO image, burned it onto a USB, and followed the installer. I ensured to:
- Set up my storage pools correctly right from the start.
- Install networking with a bridge so that VMs can talk to one another and the internet.

The installation was extremely simple and I could not photograph, but for the remainder of the components of this lab will be mentioned in their setup.

## Virtual Machines and Containers
Once Proxmox had been installed and operational, I installed a few VMs to fulfill my main use cases:

- **Kali Linux** for penetration testing experimentation.
- **Ubuntu Server** for general Linux practice and small server projects.
- **pfSense** to simulate a firewall/router setup and test network settings.
- **Wazuh Server** to monitor my endpoints and aggregate security alerts, giving me some hands-on experience with SIEM-like capabilities.

I also intend to include more specialty VMs down the road, like honeypots or other security tools, to create a complete cybersecurity simulation. 

### Use Cases

The primary purpose of this homelab is to possess a secure, isolated lab for experimentation and education. Some of the primary use cases are:

- Rehearsing SOC operations and monitoring configurations with Wazuh, viewing logs, and reacting to alarms.
- Pen testing on Kali Linux without compromising the security of my primary network.
- Automated scripts for testing, deployment strategies, and server configurations.
- Simulating complex network topologies using pfSense to understand routing, firewalls, and VLANs.
- Testing disaster recovery strategies, snapshots, and backup scenarios to recover VMs in a quick manner.


## Network Topology

Here’s a visual overview of my homelab network setup. The main goal was to keep the lab isolated from the main home network while allowing all virtual machines to communicate with each other. The single NIC on my server is bridged in Proxmox, connecting the VMs to the switch (Which is Pfsense in this case), which lets me experiment with VLANs, routing rules, and firewall configurations via pfSense without affecting other devices at home.

![Proxmox Homelab Network Diagram](assets/Topology.png)
