---
layout: post
title: Pfsense Firewall and Networking setup
date: 2025-09-04 15:53 +0300
---
When building a home cybersecurity lab, one of the most important goals is ensuring that potentially dangerous testing activities remain isolated from the rest of the network. To achieve this, I deployed pfSense as a virtual firewall inside my Proxmox Virtual Environment. This setup allows me to simulate a realistic enterprise-style network architecture while safely separating my internal lab environment from my home network.

**Infrastructure Overview**

The lab is hosted on a single physical machine running Proxmox Virtual Environment as the hypervisor. The host machine is equipped with two physical network interfaces. One interface is connected to my home router and provides internet connectivity to the host system. The second interface is dedicated exclusively to the internal lab network.

Within Proxmox, two network bridges were configured to represent these connections. The first bridge, vmbr0, is attached to the external network interface and serves as the bridge for outbound connectivity. The second bridge, vmbr1, is connected to the dedicated lab interface and is used to carry traffic within the isolated lab environment.

This structure allows virtual machines to connect either to the external network or to the isolated lab network depending on which bridge they are attached to.

Deploying the pfSense Firewall

To enforce network segmentation, I deployed a virtual machine running pfSense. The pfSense VM was configured with two virtual network adapters. The first adapter was connected to vmbr0, which acts as the WAN interface. Through this interface, pfSense receives an IP address from the home router and gains access to the internet.

The second adapter was connected to vmbr1, which functions as the LAN interface. This interface represents the internal cybersecurity lab network and was configured with the IP address 10.1.10.1/24. This address serves as the default gateway for all machines operating within the lab environment.

With this configuration in place, pfSense effectively sits between the internal lab network and the external home network, acting as a firewall and routing device.

**DHCP Configuration for the Lab Network**

To simplify network management, the DHCP server on the pfSense LAN interface was enabled to allow internal virtual machines to automatically obtain IP addresses when they connect to the lab network.

The DHCP pool was configured within the 10.1.10.0/24 subnet, assigning addresses from 10.1.10.10 through 10.1.10.245. Any virtual machine connected to the vmbr1 bridge can therefore request an address dynamically from pfSense. The firewall also advertises its LAN address (10.1.10.1) as the default gateway and DNS server.

This setup makes it easy to spin up new virtual machines for testing without needing to manually configure network parameters.

**Internal Lab Connectivity**

All cybersecurity lab systems—including test machines and monitoring tools—are connected to the vmbr1 bridge within Proxmox. Because this bridge corresponds to the pfSense LAN interface, all traffic from these machines flows through the firewall before leaving the network.

For example, when a lab machine attempts to access the internet, the traffic follows this path:

Internal VM → pfSense LAN → pfSense WAN → Home Router → Internet

pfSense performs network address translation (NAT) and firewall filtering during this process, ensuring that internal machines can access the internet while remaining protected from inbound connections.

**Benefits of Network Segmentation**

By introducing pfSense as a virtual firewall, the lab environment becomes fully isolated from the rest of the home network. This is particularly important when performing activities such as vulnerability testing, malware analysis, or penetration testing exercises. Even if a machine within the lab becomes compromised during experimentation, the firewall prevents the threat from spreading to other devices on the home network.

At the same time, my lab can retain controlled internet connectivity, allowing tools to download updates, retrieve threat intelligence feeds, or interact with external services when necessary without risking vulnerability from external networks.
