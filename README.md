<p align="center">
<img src="./images/active-directory-logo.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop (RDP)
- Active Directory Domain Services
- PowerShell
- Azure Virtual Network
- DNS

<h2>Operating Systems Used </h2>

- Windows Server 2025 Datacenter
- Windows 11 Pro

<h2>Deployment and Configuration Steps</h2>

- This lab provisions two virtual machines within the same Azure virtual network to simulate a basic on-premises Active Directory environment.

- Create a virtual network and subnet for both Virtual Machines to join (name: Active-Directory-Vnet)

<br>
<img src="./images/fig1.2-Virtual-Network.jpg" height="60%" width="60%" alt="Virtual Network"/>
<br>

<h2>Step 1: Create Virtual Machines</h2>

One virtual machine is configured as a Domain Controller, while the second serves as a client workstation.

- <b>Domain Controller (DC)</b>
  - OS: Windows (Windows Server 2025 Datacenter Azure Edition)
  - VM Size: Minimum 2 vCPUs
  - Name: dc-01
  - Virtual Network: Previously created (Active-Directory-VNet)
  - Private IP: Static (important)

- <b>Client Machine</b>
  - OS: Windows (Windows 11 Pro)
  - VM Size: Minimum 2 vCPUs
  - Name: client-01
  - Virtual Network: Previously created (Active-Directory-VNet)

<br>
<img src="./images/1.1-VMs.jpg" height="80%" width="80%" alt="Virtual Network"/>
<br>

<h2>Step 2: Assign Domain Controller a static private IP address</h2>

- The Domain Controller (dc-1) is assigned a static private IP address to ensure stable network communication with domain-joined clients.

<br>
<img src="./images/1.3-DC1-staticIP.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br>

<h2>Step 3: Set client-1’s DNS settings to dc-1’s Private IP address</h2>

- client-1 is configured to use dc-1’s private IP address as its DNS server because Active Directory depends on DNS for domain discovery, authentication, and service location.

- This ensures the client can reliably locate the Domain Controller and interact with domain services within the virtual network.

<br>
<img src="./images/1.4-dc1-privateIP.jpg" height="80%" width="80%" alt="dc-1 private IP"/>
<br>

<br>
<img src="./images/1.4-client1-dnsSettings.jpg" height="80%" width="80%" alt="client-1 DNS settings"/>
<br>

<h2>Step 4: Verify Connectivity</h2>

- To verify connectivity, the client machine (client-1) attempts to ping dc-1.

- (If request fails: check that both VMs are in the same network or if its due to ICMP traffic being blocked by the Windows firewall on the Domain Controller.)

<img src="./images/1.5-clinet1ping.jpg" height="80%" width="80%" alt="ping"/>

- Still in client-1, run ipconfig /all

  -The output for the DNS settings should show dc-1’s private IP Address

<br>
<img src="./images/a.5-client1-ipconfig.jpg" height="80%" width="80%" alt=""/>
<br>

<h2>Step 5: Promote Server to Domain Controller</h2>

- <b>Install Active Directory Domain Services on dc-1</b>
  - Open <b>Server Manager</b>
  - Select <b>Add Roles and Features</b>
  - Install <b>Active Directory Domain Services</b>

<br>
<img src="./images/2.0-dc-1.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br>

- <b>Promote dc-1 to a Domain Controller</b>
  - Select <b>Promote this Server to a Domain Controller</b>
  - Choose <b>Add New Forest</b>
  - Set Domain name to <b>mydomain.com</b>
  - Finish Installation

<br>
<img src="./images/2.1-dc1-promotion.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br>

<br>
<img src="./images/2.1-dc1-forest.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br>

- After promotion, the system is restarted to apply the configuration changes.

- Once the restart is complete, dc-1 is accessed using the domain account mydomain.com\labuser.

<h2>Step 6: Create Organizational Units and Structure</h2>

1. Successful configuration is confirmed by launching <b>Active Directory Users and Computers</b>, indicating that the domain services are operational.

2. Organizational Units (OUs) are created to establish basic directory structure and administrative separation within the domain.

3. <b>Two OUs are defined at the domain level</b>

- \_EMPLOYEES
- \_ADMINS

<br>
<img src="./images/organizational-unit1.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br>

<br>
<img src="./images/ou-2.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br

4.  Whin these OUs, a new user account is created to represent an administrative user. The account Jane Doe is provisioned with the username Jane_admin and placed appropriately within the directory structure.

- To grant elevated privileges, the user Jane_admin is added to the Domain Admins security group. This confirms both user creation and group-based privilege assignment within Active Directory.

<br>
<img src="./images/jane_doe-security.jpg" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<br>

- The Jane_admin account is used as the administrative credential for subsequent domain operations.

<h2>Step 7: client-1 is joined to mydomain.com</h2>

1. client-1 is joined to the mydomain.com domain to complete the Active Directory integration.

- <b>While logged into client-1</b>
  - Select Windows <b>System</b>
  - Select <b>Advanced System Settings</b>
  - Select <b>Computer Name</b>
  - Set Domain name to <b>mydomain.com</b>
  - When finished, computer will automaticall restart.

<br>
<img src="./images/client-1_domain.jpg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br>

<h2>Promote Server to Domain Controller</h2>

1. After the restart, Client-1 is successfully enrolled in the domain and is managed under mydomain.com, confirming proper communication with the Domain Controller and Active Directory services.

<br>
<img src="./images/verification.jpg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br>

- With Client-1 successfully joined to the domain, Remote Desktop access is configured to support non-administrative domain users.

<h2>Step 8: Validate Remote Desktop Access</h2>

1.To validate Remote Desktop access for standard domain users, a user was created using the username "Tom Smith" and placed in the \_EMPLOYEES Organizational Group.

<img src="./images/tom_smith1.jpg" height="60%" width="60%" alt="Disk Sanitization Steps"/>

2. After the user is created, "Tom Smith" is selected and used to initiate a Remote Desktop session to client-1. Successful authentication and login confirm that domain users can access the client machine as intended.

3. This final step verifies both user provisioning and proper Remote Desktop access configuration for non-privileged accounts.
