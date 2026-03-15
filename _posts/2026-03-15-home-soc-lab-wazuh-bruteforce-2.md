---
layout: post
title: "Building a Home SOC Lab and Detecting an SSH Brute-Force Attack Part 2"
date: 2026-03-15
categories: cybersecurity
tags: [SOC, wazuh, lab, blue-team]
---

# Detection in the Wazuh Dashboard

Within seconds of launching the brute-force attack, multiple alerts appeared in the Wazuh dashboard.

The platform identified repeated authentication failures originating from the same source IP address. These alerts were triggered by Wazuh rules designed to detect suspicious login behavior.

Each alert provided valuable contextual information, including:

- Source IP address of the attacker
    
- Target system hostname
    
- Authentication service (SSH)
    
- Timestamp of the event
    
- Wazuh rule triggered
    
- Severity level of the alert
    

Repeated authentication failures within a short time period strongly suggest automated password guessing rather than legitimate user activity.

From a SOC perspective, these alerts represent indicators of a potential brute-force attack as reported by Wazuh automatically, with a severity of 10.

![Wazuh brute force alert](/assets/soc-lab/wazuh-alert.png)

Now, we can delve further into these logs and see who is doing it and gather further information that we can formulize into a proper report. What interests us here is MITRE.ID T1110, but we will look at that shortly after we delve deeper into the report.

![Wazuh alert investigation details](/assets/soc-lab/wazuh-analysis.png)

As we can see, there is plenty of data to decipher here and I've highlighted what's most important, firstly mapping the time of the attack is integral to understanding the attacker, are they perhaps attacking during off hours? During a weak point of our infrastructure? (I.E, update periods, etc.), is the attack constant or only in short bursts? Secondly, we can find the victim's IP address and the attacker's IP address, knowing the attacker's IP address helps us understand if it is a single entity doing the attack, or a botnet that is hammering down our system, in this case it was a single machine and we know that it is within our own subnet as IP 10.1.10.20.

Now that it is all out of the way, we can look at the raw logs that Wazuh gathered all this information from, We can find the source port and the amount of times they attempted to enter a password, it is far from a regular user's behaviour. What could it be?

Remember the MITRE ID that we found earlier? We can look through MITRE ATT&CK to properly understand what is happening to our poor victim agent by mapping the attack to T1110

A quote from MITRE regarding ID T1110
'
Brute force

Adversaries may use brute force techniques to gain access to accounts when passwords are unknown or when password hashes are obtained.[[1]](https://www.trendmicro.com/en_us/research/20/l/pawn-storm-lack-of-sophistication-as-a-strategy.html) Without knowledge of the password for an account or set of accounts, an adversary may systematically guess the password using a repetitive or iterative mechanism.[[2]](https://www.dragos.com/wp-content/uploads/CRASHOVERRIDE2018.pdf) Brute forcing passwords can take place via interaction with a service that will check the validity of those credentials or offline against previously acquired credential data, such as password hashes.

Brute forcing credentials may take place at various points during a breach. For example, adversaries may attempt to brute force access to [Valid Accounts](https://attack.mitre.org/techniques/T1078) within a victim environment leveraging knowledge gathered from other post-compromise behaviors such as [OS Credential Dumping](https://attack.mitre.org/techniques/T1003), [Account Discovery](https://attack.mitre.org/techniques/T1087), or [Password Policy Discovery](https://attack.mitre.org/techniques/T1201). Adversaries may also combine brute forcing activity with behaviors such as [External Remote Services](https://attack.mitre.org/techniques/T1133) as part of Initial Access.

If an adversary guesses the correct password but fails to login to a compromised account due to location-based conditional access policies, they may change their infrastructure until they match the victim’s location and therefore bypass those policies.[[3]](https://www.reliaquest.com/blog/health-care-social-engineering-campaign/)'

Now that the attack has been identified and classified using the **MITRE ATT&CK** framework as technique **T1110 (Brute Force)**, the next step from a SOC perspective is response and containment.

Detection alone is not enough, security teams must also determine how to prevent the attacker from successfully compromising the system. In a real-world environment, analysts would escalate the alert and begin implementing defensive measures to mitigate the threat.

	The first step is to determine whether the attack has already resulted in a successful login. In this case, examining the authentication logs reveals only repeated failed password attempts, indicating that the attacker did not manage to authenticate successfully. However, continuous brute-force attempts can still degrade system performance and may eventually succeed if weak passwords are used.

Because the attack originated from a single source IP address within the lab network, the most immediate mitigation strategy would be to block the offending address at the firewall or intrusion prevention layer. In enterprise environments, this could be implemented through network firewalls, endpoint security platforms, or automated response mechanisms within the SIEM itself.

Another effective mitigation technique involves limiting the number of login attempts allowed within a given timeframe. Many Linux systems implement this using tools such as **Fail2Ban**, which monitors authentication logs and temporarily bans IP addresses that exceed a predefined threshold of failed login attempts.

For example, if an IP address generates five failed SSH authentication attempts within a short time window, the system can automatically block further connections from that address for several minutes or even hours. This significantly reduces the effectiveness of brute-force attacks.

In addition to network-level defenses, administrators should also strengthen authentication mechanisms on the affected service. The Secure Shell service itself, implemented through **OpenSSH**, provides several configuration options that can greatly reduce the likelihood of brute-force compromise.

Common hardening measures include disabling password authentication entirely and requiring public key authentication, restricting which users are allowed to log in via SSH, and changing the default SSH port to reduce automated scanning attempts.

Within the context of this lab environment, these defensive strategies demonstrate how monitoring platforms such as **Wazuh** function as part of a larger security ecosystem. While Wazuh successfully detects the suspicious behavior and alerts analysts, it is the responsibility of the security team to interpret these alerts and implement appropriate countermeasures.

The exercise therefore illustrates an important principle in cybersecurity operations: detection systems identify potential threats, but effective security depends on the ability of analysts to interpret alerts and respond accordingly.

