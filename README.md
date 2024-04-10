<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

<h2>Description</h2>
In this lab, we'll create two VMs in the same VNET—one as a Domain Controller (DC) with a static IP offering Active Directory services, and the other as a Client machine. The client will join the domain, and its DNS settings will be configured to use the DC as the primary DNS server. The project involves establishing an Active Directory system for centralized user credential oversight and network traffic management. By routing all internet traffic through the main server (Active Directory) via organization devices (Clients), administrators can monitor network activity and detect suspicious logs. Additionally, the project includes a PowerShell script to generate 1,000 users and showcases a device within the organization's domain, ensuring efficient management of user credentials and network traffic.
<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Project Diagram</h2>

<p align="center">
<img src="https://i.imgur.com/BcRNcBi.png" height="50%" width="50%" alt="9"/><br />
</p>

<h2>Step 1: Setup</h2>

First, using Azure, create a Resource Group. Now, create 2 Virtual Machines(VMs). One will be the Domain Controller and the other will be the Client. To create the Domain Controller, give the VM a name as well as assign it to the Resource Group created before. 

<img src="https://i.imgur.com/uYfHMQG.png" height="70%" width="70%" alt="9"/><br />

Now for the image use Windows Server 2022. It is recommended for the size to use 2 vcpus.

<img src="https://i.imgur.com/FNoA7m0.png" height="70%" width="70%" alt="9"/><br />

Give the admin log in credentials that can be remembered or just write them down in notepad. Now, click "Next" until reaching the "Networking" tab. Take note of the "Virtual Network" created. This will be important when creating the Client VM. Check the box under Licensing then "Review and create" the VM.

<img src="https://i.imgur.com/NxXFK16.png" height="70%" width="70%" alt="9"/><br />

Now, create the Client VM. Same thing as the first one except the image should be using Windows 10.

<img src="https://i.imgur.com/2PvUCJN.png" height="70%" width="70%" alt="9"/><br />

Click, Next until reaching the Networking tab. Make sure the Virtual Network is the same as the Domain Controller. Now Review and create.

Now it's time to set the Domain Controller's NIC Private IP to static. Go to the Domain Controller and click on the "Networking" tab. Next, click on the "Network Interface."

<img src="https://i.imgur.com/6W2WZTA.png" height="60%" width="60%" alt="9"/><br />

Now, go the "IP configurations" tab and click on the IP configuration. 

<img src="https://i.imgur.com/0R53K7r.png" height="60%" width="60%" alt="9"/><br />

Now, change the Allocation from "Dynamic" to "Static." Then click Save.

<img src="https://i.imgur.com/JAGBZtk.png" height="60%" width="60%" alt="9"/><br />

Now, using the user and password created before, login to the Client with it's IP address in Remote Desktop Connection. 

<img src="https://i.imgur.com/28TrmKg.png" height="55%" width="55%" alt="9"/><br />

Now, using Command Prompt, ping the Domain Controller with it's Private IP Address. Type in "ping (Your DC Private IP) -t" to perpetually ping. For now it will time out.

```commandline
ping 10.0.0.4 -t
```

<img src="https://i.imgur.com/1zyrIUN.png" height="80%" width="80%" alt="9"/><br />

Next its time to enable ICMPv4. First, login to the Domain Controller VM then open "Windows Defender Firewall with Advanced Security" 

<img src="https://i.imgur.com/bYkAEwk.png" height="85%" width="85%" alt="9"/><br />

Click on "Inbound Rules" and Sort by "Protocol". Look for the rules with "Core Networking Diagnostics - ICMP Echo Request(ICMPv4-In)" There will be two of them (Both on the bottom of the image below)

<img src="https://i.imgur.com/EydkpVV.png" height="80%" width="80%" alt="9"/><br />

Right-click and Enable both rules. Now go back to the Client VM and check on the command prompt. It should now be properly pinging the Domain Controller.

<img src="https://i.imgur.com/ENb2KyF.png" height="55%" width="55%" alt="9"/><br />

<img src="https://i.imgur.com/2YNRrzi.png" height="80%" width="80%" alt="9"/><br />

<h2>Step 2: Installing Active Directory</h2>

Now time to Install Active Directory. Go to the Domain Controller. In "Server Manager" click on "Add roles and features."

<img src="https://i.imgur.com/0BcdJpW.png" height="80%" width="80%" alt="9"/><br />

Click "Next" until reaching the "Server Roles" section. Now, check the box next to "Active Directory Domain Services" then "Add Features."

<img src="https://i.imgur.com/K5oTmkD.png" height="80%" width="80%" alt="9"/><br />

Click Next until reaching the "Confirmation" tab then click "Install." It may take a while to install. Once it says "Configuration required. Installation succeeded on (Your DC name here). Click "Close"

Towards the top-right corner of the Server Manager window, there will be a flag and a yellow triangle with a "!" symbol. Click on that then "Promote the server to a domain controller"

<img src="https://i.imgur.com/D8p1wU9.png" height="40%" width="40%" alt="9"/><br />

A window will pop up for a Configuration Wizard. Check the bubble next to "Add a new forest" then give it a domain (Example in image below) Click next.

<img src="https://i.imgur.com/BefHqfW.png" height="80%" width="80%" alt="9"/><br />

Give it a DSRM password (Required but wont be used in this tutorial) Click next.

<img src="https://i.imgur.com/TYXfTrJ.png" height="80%" width="80%" alt="9"/><br />

Next, the NETBIOS domain will be made. This may take a moment. Once it is made, Click next until reaching the "Prerequisites Check" tab. This process will take a moment. Now click "Install"

After Installing the VM will reboot. Once it is rebooted, Log back into the Domain Controller with the domain name and the username. Example below.

<img src="https://i.imgur.com/nT5uFiT.png" height="55%" width="55%" alt="9"/><br />

<h2>Step 3: Creating a Domain Admin</h2>

Once logged in, using Server Manager click on tools in the top-right corner. Next click on "Active Directory Users and Computers."

<img src="https://i.imgur.com/grdGvPg.png" height="70%" width="70%" alt="9"/><br />

In the Domain container, create a new "Organizational Unit"

<img src="https://i.imgur.com/9DGmBMS.png" height="80%" width="80%" alt="9"/><br />

Name the OU "_ADMINS", then click OK. In the "_ADMINS" tab, create a new "User"

<img src="https://i.imgur.com/gUev6rr.png" height="80%" width="80%" alt="9"/><br />

Name this anything. Just remember the user and password. Uncheck the box that is next to "User must change password at next logon." This wont be necessary. Click next then click Finish.

<img src="https://i.imgur.com/fmMKhNj.png" height="50%" width="50%" alt="9"/><br />
<img src="https://i.imgur.com/S0c7T05.png" height="50%" width="50%" alt="9"/><br />

Now add this user to the "Domain Admins" security group. Right-click on the user create, then click "Properties." Click on the "Members of" tab, then click "Add." 

<img src="https://i.imgur.com/3MooGCr.png" height="50%" width="50%" alt="9"/><br />

Type "domain" in the box under "Enter the object names to select:" then click "Check Names" 

<img src="https://i.imgur.com/WnHnpsK.png" height="60%" width="60%" alt="9"/><br />

Choose the "Domain Admins" option then click OK

<img src="https://i.imgur.com/eHOKSWT.png" height="80%" width="80%" alt="9"/><br />

Now, click "Apply." The user has successfully been added to the Domain Admins security group. Click OK. Now logout of the Domain controller and re-log as the user just created.

<img src="https://i.imgur.com/oECi1Rd.png" height="50%" width="50%" alt="9"/><br />

<h2>Step 4: Setting Client DNS Settings to Domain Controller Private IP Address</h2>

First, on Azure go to the Client VM. Next, go to the Networking tab and click on the Network Interface.

<img src="https://i.imgur.com/0jFQlMM.png" height="60%" width="60%" alt="9"/><br />

Next, go to the "DNS Servers tab and create a custom DNS Server. Add a custom server using the Domain Controller's Private IP address. Example Below.

<img src="https://i.imgur.com/DK1mOBp.png" height="60%" width="60%" alt="9"/><br />

Now click "Save" Next go back to the Client and click "Restart in the "Overview" tab 

<img src="https://i.imgur.com/Cq7d1PZ.png" height="80%" width="80%" alt="9"/><br />

Once the Client is restarted, login to the client with Remote Desktop as the admin account created.

<img src="https://i.imgur.com/LkqUK6Q.png" height="50%" width="50%" alt="9"/><br />

Once logged in go to Settings>System>About and click on "Rename this PC(advanced)"

<img src="https://i.imgur.com/1tu4Kwj.png" height="80%" width="80%" alt="9"/><br />

Now Click on "Change..."

<img src="https://i.imgur.com/zKDbFIs.png" height="50%" width="50%" alt="9"/><br />

Now check the bubble next to "Domain" then type in the domain name (Your own domain name). There should be window that pops up for a login. Use the admin previously created to login. Example below:

<img src="https://i.imgur.com/nT3wd3q.png" height="80%" width="80%" alt="9"/><br />
<img src="https://i.imgur.com/oVmTUG5.png" height="40%" width="40%" alt="9"/><br />

Success. The VM will now restart after a short period.

<h2>Step 5: Setting up Remote Connection for Domain Users</h2>

Now, log into the Domain Controller. Go back to Server Manager>Tools>Active Directory Users and Computers. Under the Domain container, go to the "Computers" tab. It should show that the client has been added to the list.

<img src="https://i.imgur.com/TT1JXxR.png" height="80%" width="80%" alt="9"/><br />

Now, log into the Client as the admin user created and go to System Settings>Remote Desktop. Click on "Select users that can remotely access this PC" Next click Add.

<img src="https://i.imgur.com/oKxoprK.png" height="80%" width="80%" alt="9"/><br />

In the box at the bottom, type in "Domain Users" and Check Names. Next click OK.

<img src="https://i.imgur.com/JXijlI7.png" height="60%" width="60%" alt="9"/><br />

<h2>Step 6: Creating Domain Users</h2>

In the Domain Controller, open "Windows PowerShell ISE." Make sure to open it as Administrator. Click "New File" in the top left corner.

<img src="https://i.imgur.com/I3165Lu.png" height="85%" width="85%" alt="9"/><br />
<img src="https://i.imgur.com/Y5BAh4S.png" height="80%" width="80%" alt="9"/><br />

Next, copy and paste the script from this link into the text editor. 

https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1

Choose "1_CREATE_USERS.ps1".

<img src="https://i.imgur.com/m6N6m4j.png" height="80%" width="80%" alt="9"/><br />

Run "Set-ExecutionPolicy Unrestricted" in the command line.

```commandline
Set-ExecutionPolicy Unrestricted
```

<img src="https://i.imgur.com/xCzIjyZ.png" height="65%" width="65%" alt="9"/><br />

Change directory to script.

```commandline
cd C:\Users\tsmith\Desktop\1_CREATE_USERS.psy
```

Now, click the Run button to run the script. This will start creating domain users with usernames and passwords (The Password for these users will be "Password1") Example below:

<img src="https://i.imgur.com/IN8xvda.png" height="65%" width="65%" alt="9"/><br />
<img src="https://i.imgur.com/RMyC0Co.png" height="80%" width="80%" alt="9"/><br />

Go to Server Manager>Tools>Active Directory Users and Computers. Under the "_EMPLOYEES" tab, look at all of the users created from the script.

<img src="https://i.imgur.com/f2xPlao.png" height="80%" width="80%" alt="9"/><br />

These names are all randomly generated. Choose one and log into the Client VM with the username it is assigned. (Remember the password is "Password1)

<img src="https://i.imgur.com/LoWC3Er.png" height="50%" width="50%" alt="9"/><br />

Congrats! You completed this tutorial.

<h2>Conclusion</h2>

Active Directory is crucial for organizations to effectively control their network traffic and prevent unauthorized access to internal networks or leakage of information to external parties. Understanding and learning about Active Directory is a fundamental principle for all IT professionals, regardless of their specific roles. Hope you found this blog both informative and valuable.

