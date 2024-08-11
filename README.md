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
- [Honey Pot VM Configuration (via Azure Portal)](#honey-pot-vm-configuration-via-azure-portal)
- [Creating Log Analytics Workspace](#creating-log-analytics-workspace)
- [Setting up Microsoft Defender for Cloud](#setting-up-microsoft-defender-for-cloud)
- [VM and Log Analytics integration](#vm-and-log-analytics-integration)
- [Log Analytics and Microsoft Sentinel (SIEM) integration](#log-analytics-and-microsoft-sentinel-siem-integration)
- [Running the VM via RDP](#running-the-vm-via-rdp)
- [Windows Event Viewer and Logon Failure](#windows-event-viewer-and-logon-failure)
- [Configuring VM for Network Exposure](#configuring-vm-for-network-exposure)
- [PowerShell script: Custom Security Log Exporter](#powershell-script-custom-security-log-exporter)
- [](#)

---



<h4>Tip: Any configuration options not mentioned in the walkthrough can be left at their default settings</h4>





---

[back to top](#toc)
## Honey Pot VM Configuration (via Azure Portal)

<br />

Browse to the Microsoft Azure Portal and sign up for a free trial. After logging in, navigate to the [Virtual Machine] service. Create a new virtual machine.

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

[back to top](#toc)
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

[back to top](#toc)
## Setting up Microsoft Defender for Cloud

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

[back to top](#toc)
## VM and Log Analytics integration

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

[back to top](#toc)
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

[back to top](#toc)
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

[back to top](#toc)
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

[back to top](#toc)
## Configuring VM for Network Exposure

<br />
<br />

We'll disable the firewall on our VM to make it responsive to ICMP echo requests, increasing its visibility on the Internet.

<br />
<br />


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




 - Open [Windows Defender Firewall] on your VM.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/117p943.png">
<br />
<br />
<br />
<br />


 - Click [Windows Defender Firewall Properties].
Change the [Firewall State] to [OFF]. Apply this to Domain, Public, and Private profiles. Click [Apply] > [OK]
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/SpVx1hm.png">
<br />
<br />
<br />
<br />


 - Return to the Command Prompt on your desktop. Ping the VM again:

```
ping x.x.x.x -t
```

<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/oSAUgGH.png">
<br />

 - You should now see successful ping responses.

<br />
<br />
<br />
<br />

---

[back to top](#toc)
## PowerShell script: Custom Security Log Exporter

<br />
<br />
<br />

 - Run [Windows PowerShell ISE] as Administrator, then open the PowerShell script.
<p align="center">
<br/>
<img width="500" alt="Portfolio" src="https://i.imgur.com/Sm44YT6.png">
<br />
<br />
<br />
<br />



 - The purpose of this script is to extract failed logon events from the Windows event log perpetually, send them to ipgeolocation.io, retrieve geographic information, and store it in a custom log file. We will later register this custom log file in the Log Analytics Workspace.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/6H8KrTW.png">
<br />
<br />
<br />
<br />

---
## code breakdown

<br />

We will go over the script code by code to understand it.

<br />
<br />
<br />

 - We're setting up three important pieces of information:
    - `$API_KEY`: This is a special code you get from ipgeolocation.io. You need to sign up on their website to get this key. It's like a password that lets you use their service.
    - `$LOGFILE_NAME`: This is the name we're giving to our custom log file. You can choose any name you want for this.
    - `$LOGFILE_PATH`: This tells the computer where to save our log file. It includes the full path to the file's location on your computer.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/CBYSoOR.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />




 - We're creating a special filter called `$XMLFilter`. This filter helps us find specific events in the Windows Event Log. Here's what you need to know:
     - The filter is written in XML, which is a way to organize data.
     - We use something called "here-strings" (marked by @' '@) to write the XML. This lets us write multiple lines of text easily, including quotation marks.
     - Our filter is set up to find events with ID 4625. These are failed login attempts.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/SJFFAiX.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />



 - Optional) To make your own filters:
     - Open the Windows Event Viewer on your computer.
     - Click on any event you're interested in.
     - Look at the "Details" tab and switch to "XML View".
     - This shows you the XML structure for that event, which you can use to build your own filters.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/bhHMHlE.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />

 - The newly defined `write-Sample-Log()` function generates multiple sample log entries and appends them to our log file, `failed_rdp.log`
 - The purpose of creating these sample logs is to provide data for training the [Extract] feature in [Log Analytics Workspace].
     - *Without enough sample log entries, [Log Analytics Workspace] may fail to extract specific fields accurately.*
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/H6VGmUp.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />

 - The sample log string flows through the pipeline, and the `Out-File` cmdlet directs it to the file specified by `$LOGFILE_PATH` and appends the string using UTF-8 encoding.
     - *We will prevent our SIEM from mapping these sample records in the future by creating a filter that excludes all log entries with a destination host of the "samplehost" Don't worry about it now!*
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/44drQRG.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />

 - This block of code will create the log file (with our sample log in it) if it doesn’t already exist. 
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/uFrcpi9.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />


 - This code creates a loop that runs forever, checking for new events every second. Here's what it does:
     - It uses `Get-WinEvent` to look at Windows Event logs.
     - It only pulls out failed login events (`EventID` 4625). We told it which events to look for using the XML filter we made earlier.
     - If it can't find any failed login events, it doesn't show an error message. Instead, it just keeps running. This is what `-ErrorAction SilentlyContinue` does.
     -  All the failed login events it finds are stored in the `$events` variable.
     -  The loop keeps running because of `while($true)`. This means "keep going forever".
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/3PoZXOU.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />


 - This code goes through each event stored in `$events` one at a time. For each event:
     - It looks at the 20th item (index 19) in the event's properties list. This item contains the source IP address of the failed login attempt.
     - It then checks if this IP address exists and isn't empty. We do this by seeing if the length of the IP address is at least 5 characters long. (The number 5 is just chosen as a simple way to check if there's actual content there.)
     - If there is an IP address present, the code inside the if-statement will run.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/sX3O6Rv.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />



 - This code takes information from the failed login event (`EventID` 4625) and turns it into a custom log entry. Here's what it does:
     - It gets the time the event happened and breaks it into parts like year, month, day, hour, minute, and second.
     - If the month, hour, or minute is a single digit, it adds a "0" in front. This keeps the format consistent.
     - It also gets the event ID, username, source computer name, and source IP address from the event.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/RIZKG6W.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />

 - *continue*
     - Then, it uses the `Invoke-WebRequest` cmdlet to send the source IP address to a website (ipgeolocation.io). This website gives back information about where the IP address is located.
     - It takes the location information from the website's response and saves it in variables.
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/yNoCRj1.png">
<br />
<br />
<br />
<br />
<br />
<br />
<br />

 - *continue*
     - Finally, it takes all this information - the time, event details, and location data - and writes it into our custom log file. 
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/408IaAj.png">
<br />

<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/BarSrWg.png">
<br />
<br />
<br />
<br />


---
## Running the script



 - As soon as you run the script, it will start to collect failed logon events with geographic data in a custom log format. We are already getting attacks from all over the world.(Russia, Angola, Sri Lanka, UK, etc..)
 - If you don’t see anything, it just means your VM has not been discovered by attackers yet. Check if the script is working by going to the RDP connection and intentionally entering the wrong logon credentials. 
<p align="center">
<br/>
<img width="672" alt="Portfolio" src="https://i.imgur.com/YH6vFhk.png">
<br />
<br />
<br />
<br />

---
##  feeding logs to [Log Analytics Workspace]


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



