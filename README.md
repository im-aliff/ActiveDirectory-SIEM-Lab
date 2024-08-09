# Active Directory with SIEM Integration

## Introduction
Active Directory with SIEM Project: Builds on the first project, integrating AD with Splunk SIEM for security monitoring. This project demonstrates the setup and management of an Active Directory environment, coupled with Splunk SIEM to monitor and analyze security events. The project includes setting up a demo PC running Windows 10, a Windows Server 2022 as the Active Directory Domain Controller, and an Ubuntu server running Splunk.

The purpose of this project is to explore how a domain environment works, learn how to ingest events into a SIEM, and generate telemetry related to various cyberattacks using Atomic Red Team and MITRE ATT&CK framework.

![Network Topology](https://github.com/user-attachments/assets/4240ad30-efb5-4141-94b2-59dab1cd1b65)


## Objectives
- Set up a Windows Server 2022 as an Active Directory Domain Controller.
- Join a Windows 10 PC to the Active Directory domain.
- Install and configure Splunk on an Ubuntu server.
- Ingest AD logs into Splunk for security monitoring.
- Create dashboards and alerts in Splunk to monitor AD events.

## Prerequisites
- Windows Server 2022 installation media or ISO.
- Windows 10 installation media or ISO.
- Ubuntu Server installation media or ISO.
- Virtualization software (e.g., VirtualBox, VMware) or physical hardware for installation.
- Basic understanding of networking, system administration, SIEM
- SEIM (Splunk)
- Atomic Red Team
- MITRE ATT&CK framework

## Installation Steps
![image](https://github.com/user-attachments/assets/60b1a155-0366-4a75-b6af-31cc8f9dcebc)
### A. Create Virtual Machines in VirtualBox
1. **Create 4 Different Virtual Machines**:
   - Create a Windows 10 machine (target-PC).
   - Create a Windows Server 2022 machine (Active Directory Domain Controller).
   - Create a Kali Linux machine (attacker)
   - Create a Ubuntu Server machine (Splunk SIEM)
     
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
### Step 2: Create a NAT Network in VirtualBox:
   - Open VirtualBox, go to `File` > `Preferences` > `Network` > `NAT Networks`.
   - Click the `+` icon to add a new NAT network.
   - Name the network `AD-Project` and set the IP range to `192.168.10.0/24`.
![image](https://github.com/user-attachments/assets/d723e97b-3cb9-4326-a883-429a3962b7a9)

![image](https://github.com/user-attachments/assets/b87ee161-caa8-43f4-a0f4-19fc80fa04d3)

**Configure Network Settings for Virtual Machines**:
   - For each virtual machine:
     - Click on the virtual machine, then click `Settings`.
     - Click on `Network`.
     - In the `Adapter 1` tab, click inside the `Attached to:` dropdown menu and choose `NAT Network`.
     - In the `Name` dropdown menu, select the NAT network `AD-Project` created in the previous step.
     - Click `OK`.
![image](https://github.com/user-attachments/assets/bb97aef5-e724-48fd-a6c8-71e39c29b49a)
![image](https://github.com/user-attachments/assets/bab14702-4417-4c87-bb66-a91a7f9e1e75)
### Step 3: Set up and configure the Ubuntu Server (Splunk SIEM) virtual machine

   - start the Ubuntu Server (Splunk SIEM) virtual machine  log in,
   - type ip a, then hit "Enter" to see the virtual machine's current IP address
   - type in sudo nano /etc/netplan/00-installer-config.yaml, then hit the "enter" key
   - enter:
      ```powershell
       network:
      ethernet:
          enpos3:
              dhcp4: no
              addresses: [192.168.10.10/24]
              nameservers:
                  addresses: [8.8.8.8]
              routes:
                  - to: default
                    via: 192.168.10.1
      version: 2
     ```
   - hit the `ctrl + x` keys, press y, then hit `Enter` to save the file
   - type sudo netplan apply and hit `Enter`
   - type ip a again, to verify that our IP address is 192.168.10.10
   - type in ping google.com and hit `Enter` to verify the internet connection through our server
   - hit the `ctrl + c` keys to stop the ping command
     
   **Set up folder sharing between VirtualBox host and Ubuntu Server virtual machine**
   - on your host computer, create a project folder for your project named "Active-Directory-Home-Lab"
   - in your Ubuntu Server virtual machine, type `sudo apt install virtualbox-guest-additions-iso` and hit "Enter"
   - type in `y`, then hit "Enter" to start the virtualbox-guest-additions-iso installation
   - in the virtual machine window, click the "Devices" tab, hover over "Shared Folders", and select "Shared Folder Settings"
   - click on the blue folder icon towards the top-right of the window to add a folder
   - in the "Folder Path:" section, choose "Other", and chose the "Active-Directory-Home-Lab" folder we created in step 1 of this section
   - leave the "Folder Name:" section as is, then select the "Read-only", "Auto-mount", and "Make-Permanent" options, click "OK", then click "OK" again
   - in our Ubuntu Server command line interface, type `sudo reboot` and hit "Enter"
   - log back into the Ubuntu Server, type in `sudo apt install virtualbox-guest-utils`, then hit "Enter"
   - type `sudo adduser *your username*` vboxsf and hit "Enter", type `sudo reboot` and hit "Enter"
   - log back into the Ubuntu Server, then type `sudo adduser *your username* vboxsf`, and hit "Enter"
   - type `mkdir share`, hit "Enter", then type `ls`, and hit "Enter"
   - type in `sudo mount -t vboxsf -o uid=1000,gid=1000 Active-Directory-Home-Lab`, then hit "Enter"
  
   **Download and install Splunk on Ubuntu Server (Splunk SIEM)**
   On your host machine: (not on any of your virtual machines, but on the machine running your virtual machines)
   - go to splunk.com, sign up with an account, and log in
   - got to the "Products" tab, then click on "Free Trials & Downloads"
   - scroll down to "Splunk Enterprise" and click on "Get My Free Trial"
   - under "Choose Your Installation Package", click the "Linux" option, then click the "Download Now" button for the ".deb" option
   - scroll through the Splunk General Terms document, click the "I have read, understood, etc" box, then click the Access program" button to start the Splunk download
   - move the "spunk*.dev file into the "Active-Directory-Home-Lab" folder we created in step 1 of the previous section
   In your Ubuntu Server virtual machine:
   - type cd && cd share then hit "Enter"
   - type in sudo dpkg -i splunk, hit the "tab" key to autocomplete the filename, then hit "Enter" to install Splunk
   - type cd /opt/splunk/bin, hit "Enter", then type in sudo -u splunk bash, and hit "Enter"
   - type in ./splunk start, hit "Enter", hit "q", hit "y", then hit "Enter"
   - enter a username, enter a password, re-enter the password, and hit "Enter"
   - type exit, hit "Enter", type cd bin, hit "Enter", then type in sudo ./splunk enable boot-start -user splunk, and hit "Enter"

### Step 4: Set up and configure Windows 10 (target-PC) virtual machine
   - Please Follow [Active Directory Home Lab](https://github.com/im-aliff/ActiveDirectoryLab/blob/main/README.md) Project.

Verify that Splunk on our Ubuntu Server is accessible from our Windows 10 (target-PC)
   Make sure our Ubuntu Server virtual machine is running,then:
   from our Windows 10 (target-PC):
   - open a web browser and type in `192.168.10.10:8000` to verify that we can reach our Splunk log-in page

Install and configure Splunk Universal Forwarder on Windows 10 (target-PC)
   - open up a web browser, go to splunk.com and log in
   - hover the mouse over the "Products" tab, and click on "Free Trials & Downloads"
   - scroll down to "Universal Forwarder" and click the "Get My Free Download" button
   - go to the "64-bit" section of the "Windows" tab and click the "Download Now" button
   - read the "Splunk General Terms", select the "I have read, understood, etc." option, and click the "Access program" button
   - open the "splunkforwarder*.msi" download, read the "License Agreement", check the box to accept the license agreement, make sure the "An on-premises Splunk         Enterprise instance" option is selected, and click "Next"
   - for the username, type in "admin", leave the "generate random password" option checked, and click "Next"
   - since we don't have a Splunk Deployment Server, leave this section blank, and click "Next"
   - under "Receiving Indexer" in the "Hostname or IP" section, enter the IP of our Ubuntu (Splunk) Server, which is 192.168.10.10, type in the default port 9997       in the field to the right of the colon, and click "Next"
   - click "Install, for the pop-up window asking if we want to allow this app to make changes to our device, click "yes", and when the install finishes, click       "Finish"

Install Sysmon on Window 10 (target-PC)
   - open a web browser and search for "Sysmon", and click the link that shows "Sysmon - Sysinternals"
   - scroll down and click the " Download Sysmon" link
   - do a web search for "sysmon olaf config", click on the "Github - olafhartong/sysmon-modular" link, scroll down, and click the "sysmonconfig.xml" file
   - click the "raw" option on the top-right of the page, right-click and save the file
   - navigate to the directory we downloaded sysmon.zip to, click on the file to select it, right-click the file, click "Extract all", then click the "Extract"       button
   - in the window that just popped up, click the file explorer bar, right-click the folder path, then click "Copy"
   - hit the "Windows" key, type in "powershell", run powershell as administrator, then click "yes"
   - type in cd followed by a single space, then right-click inside powershell to paste the folder path we just copied in the previous step, and hit "Enter"
   - type in .\Sysmon64.exe -i ..\sysmonconfig.xml, hit "Enter", then click "Agree" to install Sysmon using our sysmonconfig.xml configuration file

Configure Splunk Forwarder on Windows 10 (target-PC) machine
   - hit the "Windows" key, type in "notepad", run notepad as administrator, and click "yes"
Enter the following text into notepad:
```
     [WinEventLog://Application]
     index = endpoint
     disabled = false

     [WinEventLog://Security]
     index = endpoint
     disabled = false

     [WinEventLog://System]
     index = endpoint
     disabled = false

     [WinEventLog://Microsoft-Windows-Sysmon/Operational]
     index = endpoint
     disabled = false
     renderXml = true
     source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```
and save the file to C:\Program Files\SplunkUniversalForwarder\etc\system\local\ folder
in the "Save as type" section, click text, and select "All Files, then in the "File name:" section, type "inputs.conf", and click "Save"
   - hit the "Windows" key, type "services", and click "Run as administrator"
   - scroll down and double-click on "SplunkForwarder"
   - click the "Log On" tab, then click the "Local System account" radio button, click "Apply", then click "OK"
   - right-click "SplunkFowarder", click "Restart", if you get a pop up saying that Windows could not stop the SplunkForwarder service, click "OK", then click
     "Start the service"

### Step 5: Continue Splunk Server configuration
**Configuring Splunk Server to accept logs from the Windows 10 (target-PC) machine**
   - open a web browser, go to `192.168.10.10:8000`, and log into Splunk Server
   - at the top of the window, select "Settings", then select "Indexes"
   - click "OK" then at the top-right, click "New Index"
   - in the "Index Name" field, type in "endpoint", and click "Save"
   - at the top of the window, click on "Settings", then click on "Forwarding and receiving"
   - under "Received data", click on "Configuring receiving", click on "New Receiving Port", then type 9997 in the "Listen on this port" section, and click "Save"

If everything has been set up correctly up to this point, we should be receiving data from our Windows 10 (target-PC) into Splunk on our Ubuntu Server. To verify:
   - click on "Apps" in the top-left corner, click on "Search & Reporting", and click "Skip", and click "Skip tour"
   - in the input field under "Search", type in index=endpoint, then click on the green magnifying glass search button
   - on the left, under "SELECTED FIELDS", click the "a source" option

If you see the following values:
   ```
* WinEventLog:Security
* WinEventLog:System
* WinEventLog:Application
* XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
   ```

### Step 6: Set up and configure Windows Server 2022 (Active Directory Domain Controller) virtual machine
   - Please Follow [Active Directory Home Lab](https://github.com/im-aliff/ActiveDirectoryLab/blob/main/README.md) Project.

Verify that Splunk on our Ubuntu Server is accessible from our Windows Server 2022 (Active Directory Domain Controller
   - Make sure our Ubuntu Server virtual machine is running, then:
   - from our Windows Server 2022 (Active Directory Domain Controller:
   - open a web browser and type in `192.168.10.10:8000` to verify that we can reach our Splunk log-in page

Install and configure Splunk Universal Forwarder on Windows Server 2022 (Active Directory Domain Controller
   - open up a web browser, go to splunk.com and log in
   - hover the mouse over the "Products" tab, and click on "Free Trials & Downloads"
   - scroll down to "Universal Forwarder" and click the "Get My Free Download" button
   - go to the "64-bit" section of the "Windows" tab and click the "Download Now" button
   - read the "Splunk General Terms", select the "I have read, understood, etc." option, and click the "Access program" button
   - open the "splunkforwarder*.msi" download, read the "License Agreement", check the box to accept the license agreement, make sure the "An on-premises Splunk
   - Enterprise instance" option is selected, and click "Next"
   - for the username, type in "admin", leave the "generate random password" option checked, and click "Next"
   - since we don't have a Splunk Deployment Server, leave this section blank, and click "Next"
   - under "Receiving Indexer" in the "Hostname or IP" section, enter the IP of our Ubuntu (Splunk) Server, which is 192.168.10.10, type in the default port 9997
   - in the field to the right of the colon, and click "Next"
   - click "Install, for the pop-up window asking if we want to allow this app to make changes to our device, click "yes", and when the install finishes, click "Finish"

Install Sysmon on Window Server 2022 (Active Directory Domain Controller
   - open a web browser and search for "Sysmon", and click the link that shows "Sysmon - Sysinternals"
   - scroll down and click the " Download Sysmon" link
   - do a web search for "sysmon olaf config", click on the "Github - olafhartong/sysmon-modular" link, scroll down, and click the "sysmonconfig.xml" file
   - click the "raw" option on the top-right of the page, right-click and save the file
   - navigate to the directory we downloaded sysmon.zip to, click on the file to select it, right-click the file, click "Extract all", then click the "Extract" button
   - in the window that just popped up, click the file explorer bar, right-click the folder path, then click "Copy"
   - hit the "Windows" key, type in "powershell", rick-click powershell, click "run as administrator"
   - type in cd followed by a single space, then right-click inside powershell to paste the folder path we just copied in the previous step, and hit "Enter"
   - type in .\Sysmon64.exe -i ..\sysmonconfig.xml, hit "Enter", then click "Agree" to install Sysmon using our sysmonconfig.xml configuration file.

Configure Splunk Forwarder on Windows Server 2022 (Active Directory Domain Controller) machine
   - hit the "Windows" key, type in "notepad", right-click notepad, and click run as administrator
Enter the following text into notepad:
```
     [WinEventLog://Application]
     index = endpoint
     disabled = false

     [WinEventLog://Security]
     index = endpoint
     disabled = false

     [WinEventLog://System]
     index = endpoint
     disabled = false

     [WinEventLog://Microsoft-Windows-Sysmon/Operational]
     index = endpoint
     disabled = false
     renderXml = true
     source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```
and save the file to `C:\Program Files\SplunkUniversalForwarder\etc\system\local\` folder

in the "Save as type" section, click text, and select "All Files, then in the "File name:" section, type "inputs.conf", and click "Save"
   - hit the "Windows" key, type "services", right-click Services, and click "Run as administrator"
   - scroll down and double-click on "SplunkForwarder"
   - click the "Log On" tab, then click the "Local System account" radio button, click "Apply", then click "OK"
   - right-click "SplunkFowarder", click "Restart", if you get a pop up saying that Windows could not stop the SplunkForwarder service, click "OK", then click "Start the service"

### Step 7: Continue Splunk Server configuration

    Configuring Splunk Server to accept logs from the Windows Server 2022 (Active Directory Domain Controller) machine
   - open a web browser, go to 192.168.10.10:8000, and log into Splunk Server
If you configured the Windows 10 machine first, the configuration below has already been made. If not:

   - at the top of the window, select "Settings", then select "Indexes"
   - click "OK" then at the top-right, click "New Index"
   - in the "Index Name" field, type in "endpoint", and click "Save"
   - at the top of the window, click on "Settings", then click on "Forwarding and receiving"
   - under "Received data", click on "Configuring receiving", click on "New Receiving Port", then type 9997 in the "Listen on this port" section, and click "Save"
If everything has been set up correctly up to this point, we should be receiving data from our Windows Server 2022 (Active Directory Domain Controller) into Splunk on our Ubuntu Server. To verify:
   - click on "Apps" in the top-left corner, and click on "Search & Reporting"
   - in the input field under "Search", type in index=endpoint, then click on the green magnifying glass search button
   - on the left, under "SELECTED FIELDS", click the "a source" option

If you see the following values:
```
* WinEventLog:Security
* WinEventLog:System
* WinEventLog:Application
* XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```
You have installed everything correctly.

### Step 8: Configure Windows Server 2022 as a Domain Controller
   - Please Follow [Active Directory Home Lab](https://github.com/im-aliff/ActiveDirectoryLab/blob/main/README.md) Project.

Join our Windows 10 (target-PC) to our new Domain
   - right-click on the network icon on the right side of the taskbar, click "Open Network & Internet settings", click on "Change adapter options", and right-click your adapter
   - select "Properties", double-click on Internet Protocol Version 4 (TCP/IPv4), and change the "Preferred DNS server:" to the IP address of our Domain
   - Controller (192.168.10.7), and click "OK"
   - to verify everything is set up correctly, open the command prompt, enter "ipconfig", and verify that the "DNS Server" has the IP address "192.168.10.7"
   - search for "PC", click on "Properties", scroll down, and select "Advanced system settings"
   - click the "Computer Name" tab, click "Change", click "Domain:", type in the name of your domain, and click "OK"
   - use your administrative account login credentials to log in (username: administrator password: your password)
   - click "OK", click "OK" again, close the window, then click "Restart Now"

To verify that all is working:
   - log in as one of your newly created users by clicking on "Other user" on the login screen
   - type in the username and password for your newly created user and log in



