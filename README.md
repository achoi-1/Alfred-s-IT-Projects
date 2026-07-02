# Hi, I'm Alfred. Here are my current projects:

## Information Technology Projects 💻: 
* Install and Configure Active Directory within Oracle VirtualBox VMs
  * Oracle VirtualBox: Diagram, Installation, and Setup
    * Setting up Domain Controller VM
    * Setting up Client VM
    * Setting up DHCP server
  * Active Directory: How to
    * Create AD Organizational Units
    * Create Domain User Accounts
    * Create Security Groups
  * Active Directory: Group Policies and Troubleshooting
## Connect with me:
🔗[Linkedin](https://www.linkedin.com/in/alfred-choi-58102b127/)

### Install and Configure Active Directory within Oracle VirtualBox VMs
I will guide you step by step through setting up a home lab running Active Directory with test users, following the diagram below to simulate an office environment. No prior experience with VMware or Active Directory is required. Credits to [Josh Madakor](https://www.youtube.com/@JoshMadakor)

### Diagram
<img width="1244" height="739" alt="diagram" src="https://github.com/user-attachments/assets/48c54cd1-f593-4585-8152-d0a5267739d6" />

### Oracle VirtualBox: Diagram, Installation, and Setup

#### Step 1. Set up and install your VMware (Oracle VirtualBox)
[Click here](https://www.virtualbox.org/) to visit the Oracle VirtualBox official website to begin downloading. Run the executable and follow the installation instructions.
#### Step 2. Download Windows Server 2019 ISO and Windows 10 ISO
[Click here](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019) to download Windows Server 2019 ISO
[Click here](https://www.microsoft.com/en-us/software-download/windows10) to download Windows 10 installation media. Ensure you select "Create installation media (USB flash drive, DVD, or ISO file) for another PC", then choose the "ISO file" option when prompted.
#### Step 3. Set up the Server virtual machine (Windows Server 2019)
Open Oracle VirtualBox. Click New (CTRL + N). 
Under VM name, call it "DC" for this exercise (domain controller). 
Under "specify virtual hardware", allocate an appriopriate amount of memory and CPU based on how much you have (keep in mind we will be running two virtual machines at the same time). 
Click Finish. 
Click your virtual machine "DC" and go into settings.
Under Network, click Adapter 2, then under Attached to click Internal Network. Finish by clicking OK.
Start "DC"
A pop up asking to select a virtual optical disk file or physical optical drive containing a disk to start should appear. Select the Windows Server 2019 ISO file.
DC should then begin the process of installing Windows Servers 2019.
After installing WIndows Server 2019 and landing on the home screen, open Control Panel -> Network and Internet -> Network Connections.
Right click each Ethernet individually -> Status -> Details. Rename the one with the automatically assigned IPv4 address 169.254.215.39 to Internal Network to distinguish the external NIC with internet access. PIC 1
Set the internal NIC IP and DNS address by right clicking Internal Network -> Properties -> Highlight Internet Protocol Version 4 -> Properties -> Set IP address to 172.16.0.1, Subnet mask to 255.255.255.0, DNS server to 127.0.0.1 (localhost) PIC 2
#### Step 4. Create a Domain (AD DS)
Open Server Manager -> Under Configure this local server click add roles and features -> Next until you reach server roles. Select AD DS -> Next until Install and close. PIC 3
Top right of the UI/Screen is a flag and a yellow warning sign. Click to then promote this server to a domain controller.
Add a new forest -> Type mydomain.com then click Next -> Type a generic password for the sake of the experiment then Click Next all the way to Install. Pic 4
Set up RAS/NAT (allows client on the private network to connect to the internet via the domain controller) by going to Server Manager Dashboard -> Add roles and features -> Ensure you select your server -> Next until you reach roles. Tick Remote Access -> Next until you can Tick DirectAccess and VPN (RAS) and Routing -> Next and Install PIC 5
To enable the RAS/NAT, on the Server Manager Dashboard select Tools -> Routing and Remote Access -> Right click your server -> Configure and Enable Routing and Remote Access -> Next, then Select Network address translation (NAT) -> Under Use this puiblic interface to connect to the internet select Ethernet (the network adapter that has internet connection. If it is greyed out, exit out of the setup wizard and right click your server, then refresh) -> Next and Finish. PIC 6
#### Step 5. Set up DHCP server on the Domain Controller
On the Server Manager Dashboard select add roles and features -> Next then select DHCP server -> Add Features -> Next and Install.
Set up the scope by selecting tools from the Server Manager Dashboard -> DHCP -> Right click IPv4 -> New Scope -> Next -> Name the scope ("172.16.0.100-200" for clarity and this lab) -> Start IP Address 172.16.0.100, End IP Address 172.16.0.200, Subnet mask 255.255.255.0 -> Next (skip exclusions and delay) -> Next (skip lease duration) -> Add IP address 172.16.0.1 for Router (default gateway) -> Next and Finish.
Right Click your DHCP server under DHCP, Authorize -> Right click IPv4 and click Refresh. 
# Step 6. Set up the Client virtual machine
Open Oracle VirtualBox Manager -> New -> VM Name: CLIENT1
Under "specify virtual hardware", allocate an appriopriate amount of memory and CPU based on how much you have (keep in mind we will be running two virtual machines at the same time). 
To set up CLIENT1's network adapter, highlight CLIENT1 and select settings -> Network -> Adapter 1 select Internal Network.
A pop up asking to select a virtual optical disk file or physical optical drive containing a disk to start should appear. Select the Windows 10 ISO file.
Continue installing Windows 10 OS. Ensure you install Windows Pro, not Home.
Join the domain "mydomain.com" by logging in on the login screen or Start Menu -> Systems -> Advanced System Settings -> Computer Name -> Change... -> Under the Member of... -> Enter domain name and credientials. Okay and Restart.
Ensure you have internet access by opening CMD and using ipconfig or ping.

# How to Create AD Organizational Units
Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers
Right Click your domain root and Select New -> Organizational Unit -> Name it _Branches (Tick Protect container from accidental deletion)
Right Click _Branches -> Select New -> Organizational Unit -> Name it Toronto.
Right Click Toronto -> Select New -> Organizational Unit -> Name it Users. Repeat to create Workstations. PIC 7

# How to Create Domain User Accounts 
Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers
Navigate to your domain -> _Branches -> Toronto -> Users
Right Click Users -> New -> User -> First name: Alice, Last name: Johnson, User logon name: ajohnson -> Enter a password for you to give to the new user. Ensure you tick "User must change password at next logon".

# How to Create Security Groups
Logon to the domain controller, Start Menu -> Windows Administrative Tools -> Active Directory Users and Computers
Right Click domain root -> New -> OU -> Name it _Groups.
Right Click _Groups -> New -> Group -> Make three groups called Helpdesk, Accounting, and ITSupport.

