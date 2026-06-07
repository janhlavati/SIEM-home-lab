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
Nmap is one of the tools that is used for Reconnaissance attacks, network scanning and port mapping. This tool is useful to gather the network data about the device which can show the attacker potential routes for an attack (e.g. open ports). The command that was used for the reconnaissance attack was "sudo nmap -A -v -T4 192.168.56.103". 
- sudo nmap: runs the nmap tool with root administrative privilege
- -A: aggressive mode
- -v: prints the port immediately when they are discovered
- -T4: time template, T0 is silent and slow, while T4 is aggressive and fast
- 192.168.56.103: the ip address of the Ubuntu VM

<img width="1271" height="874" alt="Screenshot 2026-05-31 134224" src="https://github.com/user-attachments/assets/5db552ed-ad68-41c6-ac54-47700d6003db" />

Here are images of caught traffic on Wazuh agent:

<img width="959" height="744" alt="Screenshot 2026-05-31 135013" src="https://github.com/user-attachments/assets/0ce8d930-d0c1-4678-aff1-1c533c8b348e" />
<img width="955" height="741" alt="Screenshot 2026-05-31 135334" src="https://github.com/user-attachments/assets/8361567f-1ee3-48de-a566-cd36943ffd59" />


### 2. Active Authentication Brute-Force
Once the open port is discovered, the attacker can use utilities like Hydra to crack the password by brute-force. The first step showed us that the Port 22 (SSH) is open, therefore, we have an opportunity to exploit that through Hydra. The command that was used for brute-force active authentication was "hydra -l cisco -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.103 -t 4 -V".
- hydra: runs the hydra tool
- -l cisco: marks the specific user "cisco"
- -P /usr/share/wordlists/rockyou.txt: points to rockyou.txt, which is a text file that contains over 14 million cracked passwords
- ssh://192.168.56.103: specifies the ip address and network layer that is going to be attacked
- -t 4: limits the the parallel attack tasks to 4 threads
- -V: verbose, prints every single combination immediately

<img width="657" height="512" alt="Screenshot 2026-05-31 140856" src="https://github.com/user-attachments/assets/c70b8a72-c3e0-4743-9d0c-7aafa944d37e" />

Every failed attempt on Ubuntu VM writes a line to /var/auth.log, that is the file that the Wazuh agent reads in real time. Therefore, the dashboard will provide the overview of login attempts.

<img width="1919" height="1016" alt="Screenshot 2026-05-31 140910" src="https://github.com/user-attachments/assets/61ca3b56-72ca-4f1c-92e5-5a46a6af1d94" />

We can see failed ssh authentication sessions at first, and after that a successful one - which is the successful brute-force attempt.

### 3. Lateral Movement & Privilege Escalation
Since the Hydra managed to crack the password of the cisco user we have managed to gain the access to the Ubuntu machin. The regular user, however, does not have an access to system files (e.g. /etc/shadow), but in this case cisco user have root privileges.

<img width="1278" height="818" alt="Screenshot 2026-05-31 142119" src="https://github.com/user-attachments/assets/1aa4b87e-80e8-401e-89e8-7b1ede0e6605" />

Here we can see a display of alerts and 5402 rule.id - which marks the successful root login.

<img width="1918" height="991" alt="Screenshot 2026-05-31 142204" src="https://github.com/user-attachments/assets/dc16fa7e-fe98-43aa-98af-65b757dcffad" />


### 4. Post-Compromise Offline Cracking
Another tool that is used for cracking passwords is John - it is one of the fastest offline password cracking tools. It is using the text file rockyou.txt where it is checking the dictionary and unshadowing passwords. Since we managed to gain the access to root user, we have an access to shadow file and we can use the unshadow tool to unshadow the hashed passwords. The command used for unshadowing is: "sudo unshadow /etc/passwd /etc/shadow > unshadowed_hashes.txt".
- sudo unshadow: root privileges to run unshadow tool
- /etc/passwd /etc/shadow: path to the passwd and shadow files that contains public account details, and the actual hashes
- > unshadowed_hashes.txt: path to the new file where it combines text stream and redirects it into clean local file

<img width="1272" height="799" alt="Screenshot 2026-05-31 144610" src="https://github.com/user-attachments/assets/75cceec6-a182-42e0-8591-5627026e605c" />


