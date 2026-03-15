---
layout: post
title: "Building a Home SOC Lab and Detecting an SSH Brute-Force Attack Part 1"
date: 2026-03-15
categories: cybersecurity
tags: [SOC, wazuh, lab, blue-team]
---

# Building a Home SOC Lab with Wazuh and Detecting an SSH Brute-Force Attack

Everything we've done prior has built up to this point where we can (properly!) practice in a SOC environment, while most want to focus on the installation process, I want to mainly focus on the methodology and how to properly simulate an attack chain from the perspective of the attacker side, and the effects of said attempts and how they look like on the defender side, the SOC analyst's side.

The objective was to simulate an attack scenario and observe how the monitoring platform collects logs, analyzes security events, and generates alerts.

This article documents how I built a lab using **Proxmox VE** and **Wazuh**, then simulated an SSH brute-force attack using **Hydra** from an attacker machine running **Kali Linux**.

The goal was not only to generate alerts but to understand the entire process from **attack execution to SOC investigation**.

# Lab Architecture

Let's talk architecture first, this lab consists of three main components: an attacker machine, a victim endpoint, and the SOC monitoring platform. Be mindful that they are all on the same subnet, and we've made sure that the subnet is safe to be as vulnerable as we want, as it only allows outgoing traffic to our LAN, but prevents anything coming in through our WAN. (i.e, my personal computer cant ping our Linux Mint VM.)

- **Attacker:** Kali Linux VM used to simulate attacks

- **Victim:** Linux Mint VM running an SSH service

- **SOC Platform:** Wazuh manager and dashboard monitoring the victim system


The victim machine runs a Wazuh agent, which forwards security logs to the Wazuh manager for analysis.


# Deploying the Security Monitoring Platform

The SOC component of the lab was built using **Wazuh**, an open-source security platform widely used for threat detection and host-based intrusion detection.

Wazuh provides several capabilities relevant to SOC operations:

- Log collection and analysis
    
- Host-based intrusion detection
    
- File integrity monitoring
    
- Security event correlation
    
- Threat detection and alerting
    

The Wazuh manager and dashboard were installed on a dedicated virtual machine. Once operational, the Linux Mint victim machine was configured with a **Wazuh agent** and registered with the manager.

After registration, the agent began forwarding system logs to the Wazuh manager. Among the most important logs being monitored were the Linux authentication logs located at: /var/log/auth.log

These logs record authentication activity such as successful logins, failed login attempts, and privilege escalation actions. Monitoring these logs is essential for detecting attacks targeting user credentials.

# Preparing the Target System

The victim machine was configured with an SSH service running on port 22, allowing remote login attempts.

SSH is a common target for brute-force attacks because attackers often attempt to guess weak credentials to gain initial access to a system.

Before launching the attack, the SSH service was verified to be listening on all network interfaces using ss -tulnp | grep 22 to verify if the machine is listening to port 22, so we can reach the ssh service to attack later.

# Simulating an SSH Brute-Force Attack

To simulate a credential attack, the attacker machine running **Kali Linux** used **Hydra**, a widely used password-guessing tool capable of performing brute-force and dictionary attacks against various services.

The following command was used to initiate the attack:

hydra -l firewallunit(our ssh servers name) -P rockyou.txt ssh://10.1.10.10

This command instructs Hydra to attempt multiple password guesses against the SSH service on the victim machine from rockyou.txt, which realistically would've taken several days, perhaps even weeks with the limited computing power that my machine has. Some may ask 'What is Hydra?', and you'd be right to ask. Hydra is an automated brute forcing tool to try and crack simple passwords, it runs a wordlist full of the most common passwords, running multiple password submissions at a time till it finally finds the correct one, however in this case we won't be waiting that long as it may take weeks (I preemptively generated a large password for my victim)..

# Lessons Learned

While building this lab, we ran into a few issues that needed troubleshooting.

One issue encountered was that the SSH service initially appeared **filtered** when scanned with **Nmap**, despite the service running correctly. This was ultimately caused by firewall rules blocking inbound connections to port 22.

Debugging the issue required verifying several factors:

- Network connectivity between virtual machines
    
- SSH service status
    
- Firewall configuration
    
- Port accessibility
    

Resolving these issues was an important part of the learning process as we all know technical issues are never a glamorous subject, but we resolved most of these issues by allowing port 22 through the firewall, which altered the state of the port to open instead of filtered.

This is the first post in this series, the second one will introduce the investigation aspect of this exercise, showcasing the SOC analysts side of things and how a real attack can look like on a Wazuh dashboard. 
