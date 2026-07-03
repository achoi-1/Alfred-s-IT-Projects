# Hi, I'm Alfred. Here are my current projects:

## Information Technology Projects 💻: 
* Install and Configure Active Directory within Oracle VirtualBox VMs
  * [Oracle VirtualBox: Diagram, Installation, and Setup](#oracle-virtualbox-diagram-installation-and-setup)
    * [Setting up Server VM](#step-3-set-up-the-server-virtual-machine-windows-server-2019)
    * [Create a Domain (Active Directory Domain Services)](#step-4-create-a-domain-active-directory-domain-services)
    * [Setting up DHCP server on the Domain Controller](#step-5-set-up-dhcp-server-on-the-domain-controller)
    * [Setting up Client VM](#step-6-set-up-the-client-vm)
    * [(OPTIONAL) Download and run PowerShell script to add 1k test users](#step-7-optional-download-and-run-powershell-script-to-add-1k-test-users)
  * [Active Directory: How to](#active-directory-how-to)
    * [Create AD Organizational Units](#how-to-create-ad-organizational-units)
    * [Create Domain User Accounts](#how-to-create-domain-user-accounts)
    * [Create Security Groups](#how-to-create-security-groups)
  * [Active Directory: Common Tasks and Troubleshooting](#active-directory-common-tasks-and-troubleshooting)
    * [Configure Password Policy (Domain Wide)](#configure-password-policy-domain-wide)
    *	[Delegate Password Reset Permissions to Helpdesk](#delegate-password-reset-permissions-to-helpdesk)
	   * [Forgot/Reset Password](#forgotreset-password)
   	* [TROUBLESHOOTING STEPS](#troubleshooting-steps)
   	* [Common troubleshooting problems: Client cannot join the domain](#common-troubleshooting-problems-client-cannot-join-the-domain)
   	* [Common troubleshooting problems: Login fails after domain join](#common-troubleshooting-problems-login-fails-after-domain-join)
   	* [Common troubleshooting problems: User can log in but lacks expected access](#common-troubleshooting-problems-user-can-log-in-but-lacks-expected-access)
## Connect with me:
🔗[Linkedin](https://www.linkedin.com/in/alfred-choi-58102b127/)

### Install and Configure Active Directory within Oracle VirtualBox VMs
I will guide you step by step through setting up a home lab running Active Directory with test users, following the diagram below to simulate an office environment. No prior experience with VMware or Active Directory is required. Credits to [Josh Madakor](https://www.youtube.com/@JoshMadakor)

### Diagram
<img width="1244" height="739" alt="diagram" src="https://github.com/user-attachments/assets/48c54cd1-f593-4585-8152-d0a5267739d6" />

### Oracle VirtualBox: Diagram, Installation, and Setup

#### Step 1. Set up and install your VMware (Oracle VirtualBox)
* [Click here](https://www.virtualbox.org/) to visit the Oracle VirtualBox official website to begin downloading.  
* Run the executable and follow the installation instructions.
#### Step 2. Download Windows Server 2019 ISO and Windows 10 ISO
* [Click here](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019) to download Windows Server 2019 ISO/  
* [Click here](https://www.microsoft.com/en-us/software-download/windows10) to download Windows 10 installation media.  
* Ensure you select "Create installation media (USB flash drive, DVD, or ISO file) for another PC", then choose the "ISO file" option when prompted.
#### Step 3. Set up the Server virtual machine (Windows Server 2019)
* Open Oracle VirtualBox. Click New (CTRL + N).  
* Under VM name, call it "DC" for this exercise (domain controller).  
* Under "specify virtual hardware", allocate an appriopriate amount of memory and CPU based on how much you have (keep in mind we will be running two virtual machines at the same time).  
* Click Finish.  
* Click your virtual machine "DC" and go into settings.  
* Under Network, click Adapter 2, then under Attached to click Internal Network. Finish by clicking OK.  
* Start "DC".  
* A pop up asking to select a virtual optical disk file or physical optical drive containing a disk to start should appear. Select the Windows Server 2019 ISO file.  
* DC should then begin the process of installing Windows Servers 2019.  
* After installing WIndows Server 2019 and landing on the home screen, open Control Panel -> Network and Internet -> Network Connections.  
* Right click each Ethernet individually -> Status -> Details. Rename the one with the automatically assigned IPv4 address 169.254.215.39 to Internal Network to distinguish the external NIC with internet access.  
<img width="815" height="648" alt="1" src="https://github.com/user-attachments/assets/6dc5ab54-4686-4713-aff0-eb52d290ee34" />

* Set the internal NIC IP and DNS address by right clicking Internal Network -> Properties -> Highlight Internet Protocol Version 4 -> Properties -> Set IP address to 172.16.0.1, Subnet mask to 255.255.255.0, DNS server to 127.0.0.1 (localhost)  
<img width="837" height="584" alt="2" src="https://github.com/user-attachments/assets/48aa7354-5e2a-47b3-b934-5d94e98cf5bf" />

#### Step 4. Create a Domain (Active Directory Domain Services)
* Open Server Manager -> Under Configure this local server click add roles and features -> Next until you reach server roles. Select AD DS -> Next until Install and close.  
<img width="879" height="642" alt="3" src="https://github.com/user-attachments/assets/66ec8830-06fe-4461-a729-b0773320ba93" />

* Top right of the UI/Screen is a flag and a yellow warning sign. Click to then promote this server to a domain controller.  
* Add a new forest -> Type mydomain.com then click Next -> Type a generic password for the sake of the experiment then Click Next all the way to Install.  
<img width="886" height="675" alt="4" src="https://github.com/user-attachments/assets/752a43cf-1ea4-4b1a-8e89-75dd6707f8c4" />

* Set up RAS/NAT (allows client on the private network to connect to the internet via the domain controller) by going to Server Manager Dashboard -> Add roles and features -> Ensure you select your server -> Next until you reach roles. Tick Remote Access -> Next until you can Tick DirectAccess and VPN (RAS) and Routing -> Next and Install.  
<img width="886" height="669" alt="5" src="https://github.com/user-attachments/assets/cfb1d240-2e7f-4b9d-abf8-004f2a67fabf" />

* To enable the RAS/NAT, on the Server Manager Dashboard select Tools -> Routing and Remote Access -> Right click your server -> Configure and Enable Routing and Remote Access -> Next, then Select Network address translation (NAT) -> Under Use this puiblic interface to connect to the internet select Ethernet (the network adapter that has internet connection.  
* If it is greyed out, exit out of the setup wizard and right click your server, then refresh) -> Next and Finish.  
<img width="608" height="441" alt="6" src="https://github.com/user-attachments/assets/23a463d0-dbd9-46af-999f-eacbbe22c92f" />

#### Step 5. Set up DHCP server on the Domain Controller
* On the Server Manager Dashboard select add roles and features -> Next then select DHCP server -> Add Features -> Next and Install.  
* Set up the scope by selecting tools from the Server Manager Dashboard -> DHCP -> Right click IPv4 -> New Scope -> Next -> Name the scope  ("172.16.0.100-200" for clarity and this lab) -> Start IP Address 172.16.0.100, End IP Address 172.16.0.200, Subnet mask 255.255.255.0 -> Next (skip exclusions and delay) -> Next (skip lease duration) -> Add IP address 172.16.0.1 for Router (default gateway) -> Next and Finish.  
* Right Click your DHCP server under DHCP, Authorize -> Right click IPv4 and click Refresh.  
#### Step 6. Set up the Client VM
* Open Oracle VirtualBox Manager -> New -> VM Name: CLIENT1  
* Under "specify virtual hardware", allocate an appriopriate amount of memory and CPU based on how much you have (keep in mind we will be running two virtual machines at the same time).  
* To set up CLIENT1's network adapter, highlight CLIENT1 and select settings -> Network -> Adapter 1 select Internal Network.  
* A pop up asking to select a virtual optical disk file or physical optical drive containing a disk to start should appear. Select the Windows 10 ISO file. 
* Continue installing Windows 10 OS. Ensure you install Windows Pro, not Home.  
* Join the domain "mydomain.com" by logging in on the login screen or Start Menu -> Systems -> Advanced System Settings -> Computer Name -> Change... -> Under the Member of... -> Enter domain name and credientials. Okay and Restart.  
* Ensure you have internet access by opening CMD and using ipconfig or ping.
#### Step 7. (OPTIONAL) Download and run PowerShell script to add 1k test users
* Log into the Domain Controller Server VM
* Open Server Manager Dashboard -> Click Configure this local server -> Search for IE Enhanced Security Configuration and click "On" -> Turn off both Administrators and Users option (you may turn it back on after downloading the PowerShell Script provided below).  
<img width="942" height="691" alt="8" src="https://github.com/user-attachments/assets/05fe7922-5b70-4f85-9d0c-2941e5e6a9a9" />

* Open Internet Explorer and click [here](https://github.com/joshmadakor1/AD_PS/archive/master.zip) to download the PowerShell Script (Credits to [Josh Madakor](https://www.youtube.com/@JoshMadakor)).  
* Extract file to desktop -> Start menu -> Windows PowerShell -> Windows PowerShell ISE -> On PowerShell ISE click File -> Open -> Locate the recently downloaded and extract folder -> Open 1_CREATE_USERS.ps1  
* In the blue section of PowerShell ISE, type the following ->  Set-ExecutionPolicy Unrestricted (only run this command safely in a VM setting) -> Yes to all -> Run Script (F5, run it once).  
### Active Directory: How to
#### How to Create AD Organizational Units
* Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers.  
* Right Click your domain root and Select New -> Organizational Unit -> Name it _Branches (Tick Protect container from accidental deletion).  
* Right Click _Branches -> Select New -> Organizational Unit -> Name it Toronto.  
* Right Click Toronto -> Select New -> Organizational Unit -> Name it Users. Repeat to create Workstations.  
<img width="638" height="347" alt="7" src="https://github.com/user-attachments/assets/4294c525-74c9-4d64-abe4-c687ed6f6cf0" />  

#### How to Create Domain User Accounts 
* Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers.  
* Navigate to your domain -> _Branches -> Toronto -> Users.  
* Right Click Users -> New -> User -> First name: Alice, Last name: Johnson, User logon name: ajohnson -> Enter a password for you to give to the new user.  Ensure you tick "User must change password at next logon".  
#### How to Create Security Groups  
* Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers.  
* Right Click domain root -> New -> OU -> Name it _Groups.  
* Right Click _Groups -> New -> Group -> Make three groups called Helpdesk, Accounting, and ITSupport.  
### Active Directory: Common Tasks and Troubleshooting  
#### Configure Password Policy (Domain Wide)  
* Open Group Policy Management -> Expand Forest -> Domains -> mydomain.com (your domain name) -> Right-click Default Domain Policy -> Edit -> Navigate to Computer Configuration, Policies, Windows Settings, Security Settings, Account Policies, Password Policy -> Configure Maximum password age and length.  
#### Delegate Password Reset Permissions to Helpdesk  
* Right-click the branch Users OU -> Select Delegate Control -> Add the Helpdesk group -> Select Reset user passwords and force password change at next logon.  
#### Forgot/Reset Password  
* Use the Find Users, Contacts, and Groups function and find the user -> Right-click reset password -> Right-click user -> Properties -> Account -> Tick User must change password on next logon.  
#### TROUBLESHOOTING STEPS:
* Verify Network connectivity  
* Verify DNS configuration  
* Confirm authentication credentials  
* Check group membership and tokens  
* Reboot when appropriate  
#### Common troubleshooting problems: Client cannot join the domain.  
* Client VM is on a different virtual network.  
* Client DNS is not pointing to the domain controller.  
* Domain Controller is powered off or unreachable.  
* On the client, verify DNS configuration using > ipconfig /all.  
#### Common troubleshooting problems: Login fails after domain join.
* Computer was not rebooted after joining the domain.  
* User logged in with a local account.  
* User format is incorrect.  
#### Common troubleshooting problems: User can log in but lacks expected access.
* User is not a member of the correct security group.  
* Group membership was added after the user logged in (user must relog in).  
* Verify user's security token using > whoami /groups.  
