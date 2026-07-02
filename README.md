# Active-Directory-Home-Lab
Step by step directions on how to set up a home lab running Active Directory with test users to mimic an office environment. No prior VMware or Active Directory experience required.
# Step 1. Set up and install your VMware (Oracle VirtualBox)
[Click here](https://www.virtualbox.org/) to visit the Oracle VirtualBox official website to begin downloading. Run the executable and follow the installation instructions.
# Step 2. Download Windows Server 2019 ISO and Windows 10 ISO
[Click here](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019) to download Windows Server 2019 ISO
[Click here](https://www.microsoft.com/en-us/software-download/windows10) to download Windows 10 installation media. Ensure you select "Create installation media (USB flash drive, DVD, or ISO file) for another PC", then choose the "ISO file" option when prompted.
# Step 3. Set up the first virtual machine (Windows Server 2019)
Open Oracle VirtualBox. Click New (CTRL + N). 
Under VM name, call it DC for this exercise (domain controller). 
Under "specify virtual hardware", allocate an appriopriate amount of memory and CPU based on how much you have (keep in mind we will be running two virtual machines at the same time). 
Click Finish. 
Click your virtual machine "DC" and go into settings.
Under Network, click Adapter 2 and click
