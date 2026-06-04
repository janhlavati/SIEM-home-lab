# SIEM-home-lab
A home lab focused on setting up Wazuh SIEM, Ubuntu agent and Kali Linux attacker machine

# Tools
- Hypervisor - VirtualBox
- Vulnerable Machine - Ubuntu CISCO LabVM
- Attacking Machine - Kali Linux
- SIEM - Wazuh

# Network Diagram
<img width="2908" height="2204" alt="image" src="https://github.com/user-attachments/assets/5b29099d-c78c-4176-a63c-3f7d1f81544b" />


# Installation and Configuration
In order to run the SIEM home lab, it is necessary to have a SIEM and at least two machines - one that will act as an agent and the second one that will be an attacking machine. In this case the hypervisor will be VirtualBox, while three virtual machines will be deployed - Wazuh (SIEM), Ubuntu CISCO LabVM (agent) and Kali (attacking machine).

Configuration of Ubuntu CISCO LabVM:

<img width="1101" height="863" alt="image" src="https://github.com/user-attachments/assets/d0a7728f-e23b-4926-9468-39874ff380d3" />


Configuration of Kali Linux VM:

<img width="1107" height="876" alt="image" src="https://github.com/user-attachments/assets/3615e628-1324-4a6f-b708-303dda226fee" />

Configuration of Wazuh WM:

<img width="1106" height="872" alt="image" src="https://github.com/user-attachments/assets/a7fbad43-15f1-40fc-93d0-f4a0a470e8f6" />


After the deployment of all three virtual machines, another thing that was done was connecting them together - in other words making the Ubuntu VM a Wazuh agent. This was done by downloading and installing Wazuh on Ubuntu from the Wazuh dashboard generated link. Once the Ubuntu was confirmed as Wazuh agent it could be monitored through a web browser SIEM dashboard.

# Attack Execution
The attack was executed from the Kali Linux VM, which has available tools for penetration and vulnerability testing pre-downloaded. The tools used for network attacks in this project are: Nmap, Hydra and John.

### 1. Network Mapping & Reconnaissance
Nmap is one of the tools that is used for Reconnaissance attacks, network scanning and port mapping. This tool is useful to gather the network data about the device which can show the attacker potential routes for an attack (e.g. open ports).

### 2. Active Authentication Brute-Force
Once the open port is discovered, the attacker can use utilities like Hydra to crack the password by brute-force.

### 3. Lateral Movement & Privilege Escalation
Since the Hydra managed to crack the password of the regular user we have managed to gain the access to the Ubuntu machine as a regular user. The regular user, however, does not have an access to system files (e.g. /etc/shadow) the attacker needs to escalate to root/superuser.

### 4. Post-Compromise Offline Cracking
Another tool that is used for cracking passwords is John - it is one of the fastest offline password cracking tools. It is using the text file rockyou.txt where it is checking the dictionary and unshadowing passwords.
