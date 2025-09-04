---
layout: post
title: "Building My Proxmox Homelab"
date: 2025-09-04 12:30:00 +0300
categories: [homelab, virtualization]
tags: [proxmox, homelab, virtualization, cybersecurity, lab setup]
---

Ever since I've graduated, I needed a way to apply all my theory into practice in safe environments under legal conditions and without harming others. So I've went ahead and installed Proxmox on an old computer of mine to virtualize multiple devices and experiment with some of the tools that I'll eventually be using in my professional career within the comforts of my home office.

### Hardware Overview

This dated office computer we had lying around was used for this project. It's not great, but it gets the job done:
- CPU: 4 x Intel(R) Core(TM) i5-3340 CPU @ 3.10GHz (1 Socket)
- RAM: 16GB
- Storage: 1 x 1TB HDD, 100 GB will be allocated to the Proxmox OS, the rest will be used for the virtual machines and containers.
- Networking: Single 1Gbps NIC on a 100Mbps switch

The switch helps the Virtual environment have both access to the LAN network and the WLAN network, which will be useful in managing Firewalls and SIEMs that will interact with my home local network. My future plan is to upgrade the switch for wider bandwidth.

Despite the dated hardware, most of the equipment I had served it's purpose, despite having to overly manage my memory usage on multiple VMs, make sure that every virtual machine was using sufficient amounts of resources without limiting functionalities.

### Installing Proxmox
I installed Proxmox with ease. I downloaded the ISO image, burned it onto a USB, and followed the installer. I made sure to:

- Set up my storage pools correctly right from the start.
- Install networking with a bridge so that VMs can talk to one another and the internet. We call this a Virtual Switch which only the virtual machines inside of the environment can interact with, and external access is not possible.

The installation was extremely simple, however I was unable to capture screenshots, however the other components of this project will have their own segments where we walk through their installations step-by-step.

## Virtual Machines and Containers
Once Proxmox had been installed and operational, I installed a few VMs to fulfill my main use cases:

- **Kali Linux** for penetration testing experimentation.
- **Ubuntu Server** for general Linux practice and small server projects.
- **pfSense** to simulate a firewall/router setup and test network settings.
- **Wazuh Server** to monitor my endpoints and aggregate security alerts, giving me some hands-on experience with SIEM-like capabilities.

As mentioned prior we will walkthrough installation steps in their own pages, and I will be expanding upon this page once I install more specialized virtual machines.

### Use Cases

As stated prior, I wanted to practice theory and learn how different systems interact with one another, understand how to install and configure my own network topology and so on:

- Rehearsing SOC operations and monitoring configurations with Wazuh, viewing logs, and reacting to alarms.
- Pen testing on Kali Linux without compromising the security of my primary network.
- Automated scripts for testing, deployment strategies, and server configurations.
- Simulating complex network topologies using pfSense to understand routing, firewalls, and VLANs.
- Testing disaster recovery strategies, snapshots, and backup scenarios to recover VMs in a quick manner.


## Network Topology

Here’s a visual overview of my homelab network setup. The main goal was to keep the lab isolated from the main home network while allowing all virtual machines to communicate with each other. The single NIC on my server is bridged in Proxmox, connecting the VMs to the switch (Which is pfSense in this case), which lets me experiment with VLANs, routing rules, and firewall configurations via pfSense without affecting other devices at home.

![Proxmox Homelab Network Diagram](/Images/Topology.png)

