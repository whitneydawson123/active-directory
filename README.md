<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources in Azure
- Ensure Connection between Client and Domain Controller
- Install Active Directory and Admin Creation
- Create Client Users using Powershell Script

<h2>Deployment and Configuration Steps</h2>

<p>
Create the Domain Controller VM (Windows Server 2022). I named it DC-1. Set up Domain Controller's Network Interface Card (NIC) Private IP address to be static DC-1 -> networking -> NIC -> IP configurations.
</p>

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/931ae21c-d378-4283-b08b-bb690e1ca977" height="80%" width="80%" alt="Step 1"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/bde53b46-9a97-4b77-817e-6132c8c50982" height="80%" width="80%" alt="Step 2"/>
</p>
<p>
Now create the Client VM (Windows 10) named Client-1. Use the same Resource Group and Vnet that was created in the DC-1 step.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/9fe2ab15-95bc-4f84-ae1b-341d9c299be1" height="80%" width="80%" alt="Step 3"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/094702b2-f473-472b-8942-0097284adbde" height="80%" width="80%" alt="Step 4"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/98ff4630-f6d9-4785-899a-65253aa8e61a" height="80%" width="80%" alt="Step 5"/>
</p>
<p>
Make sure client-1 and dc-1 are on the same Vnet and resource group. Then, log in to Client-1 with Remote Desktop and ping the DC-1's private IP address with ping -t. 

Notice that we are getting "request timed out" Let's fix that! Login to DC-1 and make sure to keep Client-1 open.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/8d513c64-a599-4708-8618-c0b5fbf9ec71" height="80%" width="80%" alt="Step 6"/>
</p>
<p>
Go to the search bar and search for wf.msc go to Inbound Rules in the top left and sort by protocol. Enable Core Networking Diagnostics - ICMP Echo Request. Enable both Private and Domain Profiles.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/10eeebc2-c41b-4f2a-9699-1d77bb842343" height="80%" width="80%" alt="Step 7"/>
</p>
<p>
Now go back to Client-1 and the ping should now be succeeding.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/11a44319-5552-4c47-a079-4d192bea30e4" height="80%" width="80%" alt="Step 8"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/0c236aba-3825-4b2c-bdb9-05c527d0187e" height="80%" width="80%" alt="Step 9"/>
</p>
<p>
Now, it is time to install Active Directory. Go to DC-1 and go to server manager -> add roles and features -> then check the "Active Directory Domain Services".
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/52212321-e1aa-4754-b66e-63fbee0cba7c" height="80%" width="80%" alt="Step 10"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/a5560785-8e4b-4628-adda-88bf961107b5" height="80%" width="80%" alt="Step 11"/>
</p>
<p>
Once you have installed that and are now on the main server control page. Click on the yellow triangle and click promote this server to a domain controller. Click Add a new forest and enter any domain name you want. I chose mydomain.com. Restart and then log back into DC-1 as user: mydomain.com\labuser
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/cca7ebe8-7e04-4315-b55e-e3fedb94e549" height="80%" width="80%" alt="Step 12"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/64879dd9-3e8c-4aa2-8047-259fe43b529a" height="80%" width="80%" alt="Step 13"/>
</p>
<p>
Go to Active Directory Users and Computer (ADUC) and create an Organization Unit (OU) called "_EMPLOYEES" and another OU called "_ADMIN".
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/42b58971-3fe8-40e2-848f-6a1d534b827f" height="80%" width="80%" alt="Step 14"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/60a77737-01ba-4068-bed0-a56ac06adc12" height="80%" width="80%" alt="Step 15"/>
</p>
<p>
In _ADMINS create a new employee named Jane Doe with the username of jane_admin. Add jane_admin to the Domain Admins Security Group. Log out of the Remote Desktop connection and log back in as mydomain.com\jane_admin.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/6d0efbd8-1134-4e31-ba56-3d44a9f9deda" height="80%" width="80%" alt="Step 16"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/e144f5a6-d49b-4abe-b5e4-979675b448d7" height="80%" width="80%" alt="Step 17"/>
</p>
<p>
Go back to the Azure Portal and set Client-1's DNS settings to the DC's Private IP address. Restart Client-1 and then log back in through Remote Desktop with labuser. Join Client-1 to the domain and the computer will restart.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/bc56b6c2-3c2b-41e0-9c2f-755871d80420" height="80%" width="80%" alt="Step 18"/>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/e82c6bbf-6742-4b8b-879a-bf0d846380a7" height="80%" width="80%" alt="Step 19"/>
</p>
<p>
Log into Client-1 as mydomain.com\jane_admin and open system properties. Click remote desktop. Then, allow domain users to access the remote desktop. You can now log into Client-1 as a non-administrative user.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/ee6dfd43-a764-44bc-ba80-b6a688c7120f" height="80%" width="80%" alt="Step 20"/>
</p>
<p>
Log into DC-1 as jane_admin. Open Powershell ISE as an administrator. Then I paste the Powershell script I created and hit the start green arrow.
</p>
<br />

<p>
<img src="https://github.com/whitneydawson123/active-directory/assets/94804621/8e97c27c-ee2b-4a51-8baa-71eb8e82f000" height="80%" width="80%" alt="Step 21"/>
</p>
<p>
Then, I log out of my Client-1 desktop and logged back in with one of the accounts the PowerShell script just created. 
</p>
<br />
