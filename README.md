<h1 align="center">Summary Diagram</h1>


<p align="center">
<br/>
<img width="950" alt="Portfolio" src="https://i.imgur.com/C5wbyJ4.png">
<br />
</p>


<br />
<br />

<h1 align="center">Project walk-through</h1>

<br />
<br />

---
<a name="toc"></a>
**Table of Contents:**

- 

---

---

<h4>Tip: Any configuration options not mentioned in the walkthrough can be left at their default settings</h4>

---



---
## Honey Pot VM Configuration (via Azure Portal)

<br />

Browse to the Microsoft Azure Portal and sign up for a free trial. After logging in, navigate to the [Virtual Machine] service.

<br />
<br />

 - Under the [Resource Group] drop-down menu, click [Create New]. Create a new resource group named “HoneypotLab.”

<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/3A6MOv2.png">
<br />
<br />
<br />
<br />



 - We will name the virtual machine "honeypot-vm"
 - Choose your region ("US East" in my case)
 - Choose ISO (Select the most recent Windows server)
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/xwJoMOT.png">
<br />
<br />
<br />
<br />





 - Keep the size at the default setting (2 vCPUs, 8 GiB memory).
 - Create an admin account for this VM (in this lab, username: soeladmin, password: Cyberlab!123)
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/NLPUrkH.png">
<br />
<br />
<br />
<br />



 - For the Network Interface option, create a new virtual network named “honeypot-vm-vnet” and use the default settings. Choose the default subnet with the range 10.0.0.0/24. Name the new Public IP “honeypot-vm-ip.”
 - Select [Advanced] for the NIC network security group, and create a new network security group called “honeypot-vm-nsg.”
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/9cUJ96u.png">
<br />
<br />
<br />
<br />


 - We can add a firewall rule for this network security group. To do so, delete the default inbound rule and create a new one.
Click [Add an inbound rule]. Configure like below:
    - Source: Any
    - Source port ranges: * (asterisk means any port)
    - Destination: Any
    - Destination port ranges: *
    - Action: Allow
    - Priority: 100 (lower numbers have higher priority)
    - Name: You can choose any name, but for this lab, we’ll use “EXPOSED_ANY_IN”
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/5EGqz6r.png">
<br />
<br />
<br />
<br />

*This configuration is intended to make the virtual machine highly visible and attractive to potential interactions, ensuring that no traffic is dropped (including TCP ping, SYN scans, ICMP ping, etc.).*

<br />
<br />
<br />
<br />



 - After completing the network settings, proceed to the review section and create the VM.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/uDEovSk.png">
<br />
<br />
<br />
<br />

---
## Creating Log Analytics Workspace

<br />
Return to the Azure portal main page and select the [Log Analytics Workspace] service. This service will be used to ingest logs from the virtual machine. click [Create]
<br />
<br />
<br />
<br />


 - Choose the resource group "HoneypotLab" we previously created. Let's also name our Log Analytics workspace "law-honeypot."
 - Pick the region wherever you picked previously (EAST US in this lab)
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/4qRk5sy.png">
<br />
<br />
<br />
<br />


 - Review and create!
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/5H6SQMy.png">
<br />
<br />
<br />
<br />

---
## [Microsoft Defend for Cloud] Configuration

<br />
Next, we’ll enable log collection from the virtual machine into the Log Analytics workspace.
<br />
<br />
<br />
<br />



 - Navigate to [Microsoft Defender for Cloud] > [Environment Settings], then select the “law-honeypot” workspace.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/rlNzDcg.png">
<br />
<br />
<br />
<br />


 - Disable SQL Servers since we won’t be using them. Enable the other options: [Foundational CSPM] and [Servers].
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Awp0c1a.png">
<br />
<br />
<br />
<br />



 - Navigate to [Settings] > [Data Collection] Pick [All Events]
click [Save]
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/561BAUr.png">
<br />
<br />
<br />
<br />

---
## VM and Log Analytics intergration

<br />
Now we’ll connect the Log Analytics workspace to our VM.
<br />
<br />
<br />
<br />


 -  Go to [Log Analytics workspace] and select “law-honeypot.”
 -  Then, within the [Log Analytics workspace], navigate to [Virtual Machines] and choose “honeypot-vm.”
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/lWtMh6T.png">
<br />
<br />
<br />
<br />


 - click [Connect]
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/j2UwHtS.png">
<br />
<br />
<br />
<br />

---
## Log Analytics and Microsoft Sentinel (SIEM) integration

<br />
<br />
<br />
<br />

 - Navigate to [Microsoft Sentinel] and click [Create Microsoft Sentinel]
    - *Microsoft Sentinel will serve as our SIEM for visualizing the attack data.*
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/3W4vqmD.png">
<br />
<br />
<br />
<br />





 - Select our Log Analytics workspace "law-honeypot"
 - click [Add]
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/FwePCmd.png">
<br />
<br />
<br />
<br />

---
## Running the VM via RDP


 - Open [Remote Desktop Connection] on your host machine. Then, go to the Azure portal, navigate to [Virtual Machine], and select “honeypot-vm.” Copy the VM’s Public IP Address and paste it into Remote Desktop Connection on your host computer, then click [Connect].
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/9yY9YDN.png">
<br />
<br />
<br />
<br />



 - Enter the VM admin credentials you created during the VM deployment.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/ZalwypN.png">
<br />
<br />
<br />
<br />



 - Once you login to your VM, open [Event Viewer]
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/8PkX9Sa.png">
<br />
<br />
<br />
<br />

---
## Windows Event Viewer and Logon Failure

<br />
<br />

 - Go to [Windows Logs] > [Security].
 - The top pane displays all security events with timestamps and event IDs. The bottom pane shows detailed information about each selected event.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/BduPEjy.png">
<br />
<br />
<br />
<br />


 - Let's experiment: Pretend you are an attacker attempting to brute-force the VM's login credential.
 - Try logging in to the VM (while the VM is running) using RDP with an invalid account name and password.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/yxV6dLt.png">
<br />
<br />
<br />
<br />


 - You will immediately see new Logon failure security event log.
You can double-click the event to see the detail:
    - Account Name: SoelFailureExample
    - Failure Reason: Unknown
    - username or bad password.
    - Workstation name: YourDesktopName
    - Source Network Address: YourIPaddress
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Yl4AdyH.png">
<br />
<br />
<br />
<br />



 - From the previous example, you saw that when attackers try to access our VM, their IP addresses are recorded in the Windows event log, viewable in the Event Viewer.
We'll now use these IP addresses to:


    - Retrieve geographic information (latitude, longitude, city, state, and country) using PowerShell and a third-party API (ipgeolocation.io).
    - Create a custom log with this geographic data on our VM.
    - Send these custom logs to the Azure Log Analytics workspace.
    - Use Azure Sentinel (SIEM) to ingest the logs and plot the attackers' locations on a map.

<br />

 - This process allows us to visualize the geographic origin of attempted intrusions.

<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/SUmUxE0.png">
<br />

 
 - *Example from ipgeolocation.io*

<br />
<br />
<br />

---
## Configuring VM for Network Exposure

<br />
<br />

 - We'll disable the firewall on our VM to make it responsive to ICMP echo requests, increasing its visibility on the Internet.
 - On your host machine (not the VM), open Command Prompt. Start a continuous ping to your VM:

```
ping x.x.x.x -t
```

 - *(Replace x.x.x.x with your VM's IP address. The -t flag enables continuous pinging.)*

<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/9NePZMJ.png">
<br />
<br />

 - You'll see 'Request timed out' messages.
 - We need to disable the VM's firewall. While we could allow ICMP traffic specifically, we'll turn off the entire firewall for this honeypot setup.
    - *Note: Disabling the firewall increases security risks. Only do this in a controlled environment for specific testing purposes.*

 
<br />
<br />




25
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/117p943.png">
<br />
<br />
<br />
<br />


26
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/SpVx1hm.png">
<br />
<br />
<br />
<br />


27
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/oSAUgGH.png">
<br />
<br />
<br />
<br />



28
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Sm44YT6.png">
<br />
<br />
<br />
<br />



29
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/6H8KrTW.png">
<br />
<br />
<br />
<br />


30
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/CBYSoOR.png">
<br />
<br />
<br />
<br />





31
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/SJFFAiX.png">
<br />
<br />
<br />
<br />



32
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/bhHMHlE.png">
<br />
<br />
<br />
<br />


33
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/H6VGmUp.png">
<br />
<br />
<br />
<br />


34
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/44drQRG.png">
<br />
<br />
<br />
<br />


35
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/uFrcpi9.png">
<br />
<br />
<br />
<br />



36
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/3PoZXOU.png">
<br />
<br />
<br />
<br />



37
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/sX3O6Rv.png">
<br />
<br />
<br />
<br />




38
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/RIZKG6W.png">
<br />
<br />
<br />
<br />



39
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/yNoCRj1.png">
<br />
<br />
<br />
<br />



40
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/408IaAj.png">
<br />
<br />
<br />
<br />



41
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/BarSrWg.png">
<br />
<br />
<br />
<br />




42
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/YH6vFhk.png">
<br />
<br />
<br />
<br />




43
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/gxKSpKn.png">
<br />
<br />
<br />
<br />



44
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/njOcNNU.png">
<br />
<br />
<br />
<br />




45
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/dm4peR2.png">
<br />
<br />
<br />
<br />



46
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/ypjexuQ.png">
<br />
<br />
<br />
<br />




47
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/CAO3Dzu.png">
<br />
<br />
<br />
<br />



48
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/PqA2yuN.png">
<br />
<br />
<br />
<br />




49
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/tQqcA7U.png">
<br />
<br />
<br />
<br />




50
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/ZNwh1OJ.png">
<br />
<br />
<br />
<br />




51
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/XlKcX1A.png">
<br />
<br />
<br />
<br />



52
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Cfqdkaf.png">
<br />
<br />
<br />
<br />



53
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/4JHmthk.png">
<br />
<br />
<br />
<br />



54
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/J0pvZ3H.png">
<br />
<br />
<br />
<br />


55
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/4wHB5i3.png">
<br />
<br />
<br />
<br />



56
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/EXJig5P.png">
<br />
<br />
<br />
<br />


57
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/l7DWZPi.png">
<br />
<br />
<br />
<br />


58
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/OcRtJ01.png">
<br />
<br />
<br />
<br />



59
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/0d9kzPK.png">
<br />
<br />
<br />
<br />


60
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/obqAUFi.png">
<br />
<br />
<br />
<br />



61
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/AaMTmB7.png">
<br />
<br />
<br />
<br />



62
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/PKrPZCp.png">
<br />
<br />
<br />
<br />



63
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/JmUbj1V.png">
<br />
<br />
<br />
<br />


64
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Zg5ElR2.png">
<br />
<br />
<br />
<br />



65
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/iDJPrdo.png">
<br />
<br />
<br />
<br />



66
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/t60dHRV.png">
<br />
<br />
<br />
<br />


67
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/WYFf3E3.png">
<br />
<br />
<br />
<br />



68
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/Vqwbpr5.png">
<br />
<br />
<br />
<br />


69
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/byxOXS6.png">
<br />
<br />
<br />
<br />






\<end\>

[back to top](#toc)

<br />
<br />
<br />



