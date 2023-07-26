# Active Directory: Simulating a Corporate Environment

<!-- ## [YouTube Demonstration](#placeholder) -->

## Introduction
This Active Directory Lab consists of a Windows Server 2019 virtual machine and a Windows 10 Pro virtual machine inside Oracle VM VirtualBox to simulate a real-world Active Directory environment.
<br/>
<br/>
The Server '19 VM acts as the Active Directory domain controller and has two separate NICs: one internal, one external. The internal NIC will be used to connect client machines to the server 
via a virtual private network hosted by the domain controller. The external NIC will be used to connect the server and its clients to the Internet and its DHCP settings will be automatically 
configured by my ISP. 
<br/>
<br/>
The Windows 10 Pro VM acts as a client on the network and has an internal NIC whose DHCP addressing is provided by the domain controller.
<br/>
<br/>
A Microsoft PowerShell script will be run to populate Active Directory with over 1,000 users.

## Utilities Used

- **Oracle VM VirtualBox**
- **Microsoft Powershell**
- **Microsoft Active Directory**

## Environments Used

- **Microsoft Server 2019**
- **Windows 10 Pro**

## Network Environment Diagram:

<img src="https://i.imgur.com/CFzA6YB.png" alt="Network Environment Diagram" title="Network Environment Diagram" width="80%" height="80%">

## Setting Up Oracle VirtualBox

1. First, I installed Oracle VirtualBox and the VirtualBox Extension Pack.
2. I also installed the Windows 10 .iso and Server 2019 .iso files and saved them on my C:\ drive.

## Creating the Server 2019 Virtual Machine (Domain Controller)

3. I opened Oracle VirtualBox and setup a new virtual machine running Server 2019.
    - Name: Domain Controller (DC)
    - Memory: 4GB
    - Processor Cores: 4
    - Network Adapter 1: NAT
    - Network Adapter 2: Internal

<img src="https://i.imgur.com/mfhwHSx.png" alt="Domain Controller VM Configuration Settings" title="Domain Controller VM Configuration Settings" width="80%" height="80%">

4. I ran the setup process and installed the Standard Evaluation (Desktop Edition) to provide access to the GUI instead of only command prompt.
5. I also installed the VirtualBox Guest Additions software to make the VM experience more streamlined.

<img src="https://i.imgur.com/QOkWGXq.png" alt="Installing VirtualBox Guest Additions" title="Installing VirtualBox Guest Additions" width="80%" height="80%">

## Identifying and Configuring the Network Adapters

5. To identify which network adapter was which, I checked the network connection details to see which one had an established DNS server from my ISP.

<img src="https://i.imgur.com/YXsxIG7.png" alt="Identifying the NICs" title="Identifying the NICs" width="80%" height="80%">

6. Afterwards, I labeled each adapter as _INTERNAL and _INTERNET.

<img src="https://i.imgur.com/Q5w0RAH.png" alt="Renaming the Server NICS" title="Renaming the Server NICS" width="80%" height="80%">

7. For the Internal adapter, I used the IP/TCP settings shown in the [Network Environment Diagram](#network-environment-diagram) above.
    - This Internal NIC will allow the Client computer to connect to my Domain Controller via a private network.

<img src="https://i.imgur.com/iKxBT4l.png" alt="Internal NIC: IP/TCP Settings" title="Internal NIC: IP/TCP Settings" width="80%" height="80%">

8. I also renamed the machine to DomainController to make it easier to identify.

## Setting Up Active Directory Domain Services
9. Next, I installed Active Directory Domain Services via Add Roles and Features and created a new forest with root domain name mydomain.com.
    - This creates the initial Active Directory domain that will contain all my users.

<img src="https://i.imgur.com/209UIUJ.png" alt="Configuring Active Directory Domain Services" title="Configuring Active Directory Domain Services" width="80%" height="80%">

10. Afterwards, I utilized Active Directory Users and Computers to add a new Organizational Unit called _ADMIN and created an administrator account a-blathers for myself.

<img src="https://i.imgur.com/dgAvrH8.png" alt="Creating the Administrator Account" title="Creating the Administrator Account" width="80%" height="80%">

11. I switched to this new administrator account to make all future changes to the system.

## Configuring Routing and Remote Access
12. I installed Remote Access with Routing and setup NAT on my Internet network adapter to provide Internet access to my server and clients.

<img src="https://i.imgur.com/mKLBtny.png" alt="Remote Access and Routing: NAT" title="Remote Access and Routing: NAT" width="80%" height="80%">

## Configuring DHCP for the Domain Controller
13. I installed DHCP and set my Scope using the settings shown in the [Network Environment Diagram](#network-environment-diagram).
    - The range of assignable IP addresses includes 172.16.0.100 through 172.16.0.200.
    - The domain controller will act as the DHCP server for the clients and act as the router, so the DNS server will be the Domain Controller's IP address.

<img src="https://i.imgur.com/DNWbi1h.png" alt="Installing DHCP on the Server" title="Installing DHCP on the Server" width="80%" height="80%">

<img src="https://i.imgur.com/nyJ2jvX.png" alt="Setting the DHCP Server Scope" title="Setting the DHCP Server Scope" width="80%" height="80%">

14. I authorized the DHCP server and refreshed to make sure the configuration applied.


## Adding Users Via a Powershell Script
15. I transferred the Powershell script onto the virtual machine and opened it using Powershell (ISE) in administrator mode.
    - I made sure to change Set-ExecutionPolicy Unrestricted to allow the script to be run on the server.
    - I navigated to the directory of where the script was stored and ran it to create the users.

<img src="https://i.imgur.com/FxHf90G.png" alt="Running the PowerShell Script" title="Running the PowerShell Script" width="80%" height="80%">

<img src="https://i.imgur.com/yO9qfs1.png" alt="Verifying User Population" title="Verifying User Population" width="80%" height="80%">


### How the Script Works
- The $PASSWORD_FOR_USERS variable sets the password for all created users as "Password1" for simplicity.
- The $USER_FIRST_LAST_LIST variable pulls and stores the list of names from the names.txt file that will be used to create the users.
- The $password variable converts the string text for the password to a secure string that Active Directory can use.
- The 7th line creates a new organizational unit in Active Directory called _USERS where the users will be stored.
- The loop on lines 9-12 takes each entry in the names list and breaks them up into first and last name variables.
    - The first letter from the first name is combined with the entire last name to create the username.
- Line 13 generates a message for each username as it is created.
- Lines 15-23 provide the user details that are stored in Active Directory for each users.
- After the script finishes running, the domain is populated with users.

## Creating the Windows 10 Client Machine
16. I kept my domain controller VM open and created another VM in VirtualBox.
    - Name: CLIENT1
    - Memory: 4GB
    - Processor Cores: 4
    - Network Adapter 1: Internal

<img src="https://i.imgur.com/2TdF8pY.png" alt="CLIENT1 VM Configuration Settings" title="CLIENT1 VM Configuration Settings" width="80%" height="80%">

17. I ran the installer and installed Windows 10 Pro to allow the client to connect to my domain.
18. I renamed the machine and added it to the mydomain.com domain using my standard user account blathers.

<img src="https://i.imgur.com/EopqDBr.png" alt="Authorizing Addition of CLIENT1 to Domain" title="Authorizing Addition of CLIENT1 to Domain" width="80%" height="80%">

19. Finally, I switched to my blathers user account.

<img src="https://i.imgur.com/9ydkTB0.png" alt="Verifying the Client User and Domain" title="Verifying the Client User and Domain" width="80%" height="80%">

## Results
- I now have a fully configured Active Directory Home Lab with over 1,000 users.
- The Server '19 VM acts as the domain controller where I can make changes to the Active Directory configuration.
- The Windows 10 Pro VM acts as a client on the network or an employee in a corporate environment.
- I can use this environment to practice managing the creation of users, deletion of users, and modification of access privileges in an Active Directory Environment.
