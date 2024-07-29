# Active Directory SEIM Lab

## Introduction
This project demonstrates the setup and management of a home lab environment featuring Active Directory (AD) and Splunk SIEM. The environment includes:
- 1 Windows 10 machine (target-PC)
- 1 Kali Linux machine (attacker)
- 1 Windows Server 2022 machine (Active Directory Domain Controller)
- 1 Ubuntu Server machine (Splunk SIEM)

The purpose of this project is to explore how a domain environment works, learn how to ingest events into a SIEM, and generate telemetry related to attacks.
![Network Diagram](https://i.imgur.com/vor6Lci.png)


## Prerequisites
- Windows Server 2022 ISO
- Windows 10 ISO
- Kali Linux ISO
- Ubuntu Server ISO
- PowerShell
- Basic understanding of networking and system administration
- Virtualization software (e.g., VMware, VirtualBox)

## Installation
### Step 1: Set Up the Virtual Machines
1. **Windows Server 2022 (AD Domain Controller)**
    - Install Windows Server 2022.
    - Configure network settings.
    - Promote the server to a domain controller.

2. **Windows 10 Machine (Target-PC)**
    - Install Windows 10.
    - Join the machine to the AD domain.

3. **Kali Linux (Attacker)**
    - Install Kali Linux.
    - Configure network settings to communicate with the domain environment.

4. **Ubuntu Server (Splunk SIEM)**
    - Install Ubuntu Server.
    - Install and configure Splunk Enterprise.
![VM](https://github.com/user-attachments/assets/ff9678fd-3158-4d3a-a635-96cd15ac7ad0)

### Step 2: Configure Active Directory
1. Create Organizational Units (OUs), Users, and Groups using PowerShell scripts provided in `/scripts`.
    ```powershell
    # create_users.ps1
    Import-Module ActiveDirectory
    New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@domain.com" -Path "OU=Users,DC=domain,DC=com" -AccountPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force) -Enabled $true
    ```

### Step 3: Set Up Splunk
1. Install Splunk on the Ubuntu Server.
2. Configure Splunk to receive logs from the Windows Server.
    - Use configuration files in `/configurations` such as `splunk_inputs.conf` and `splunk_outputs.conf`.

## Usage
### Generating Telemetry
Use the Kali Linux machine to perform various attacks on the target-PC. Monitor and analyze the logs in Splunk to understand the telemetry generated by these attacks.

### Example Attack
1. Use Kali Linux to perform a brute-force attack on the Windows 10 machine.
2. Check Splunk for the generated events and analyze the attack patterns.

## Contributing
Feel free to fork this repository and submit pull requests. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](LICENSE)
