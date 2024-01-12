## Lab Set Up Through Virtual Machine.

- Creating a VM in VirtualBox for Windows 10:
1. Open VirtualBox:

- Launch the VirtualBox application on your computer.
2. Click on "New":

- In the VirtualBox Manager, click on the "New" button.
3. Provide VM Name and OS Type:

- Enter a name for your VM (e.g., "Windows 10").
- Choose "Microsoft Windows" as the Type.
- Choose "Windows 10 (64-bit)" as the Version.
4. Assign Memory (RAM):

- Click "Next."
- Choose the amount of RAM. Set it to 4096 MB (4GB).
5. Create a Virtual Hard Disk:

- Choose "Create a virtual hard disk now" and click "Create."
6. Hard Disk File Type:

- Choose the default "VDI (VirtualBox Disk Image)" and click "Next."
7. Storage on Physical Hard Disk:

- Choose "Dynamically allocated" for flexibility and click "Next."
8. File Location and Size:

- Set the file location and size for the virtual hard disk.
- Set the size to 40 GB and click "Create."
9. Attach Windows 10 ISO:

- In the VirtualBox Manager, select your newly created VM.
- Click on "Settings."
- In the settings window, go to the "Storage" tab.
- In the "Controller: IDE" section, click on the empty disk icon under "Controller: IDE."
- In the Attributes panel on the right, click on the disk icon next to "Optical Drive," and choose "Choose a disk file..."
- Select the Windows 10 ISO file from your computer and click "OK."
10. Start the VM:

- With your VM selected in the VirtualBox Manager, click "Start."
11. Install Windows 10:

- The VM will boot from the Windows 10 ISO. Follow the on-screen instructions to install Windows 10 on your VM.
12. Complete the Installation:

- Once the installation is complete, Windows 10 will boot up in your Virtual Machine.

- ![windows10 vm](https://github.com/yakkalasaisumanth/SOC-automation-project/blob/main/windows%2010%20settings.png)


- installing sysmon

- click the link and download the sysmon tool

- link :- [sysmon](https://download.sysinternals.com/files/Sysmon.zip)

- now un zip the downloaded file.

- now for the config file please click the link down bellow.

- link :- [sysmon.conf](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)


- now click on raw. right click -> save as -> sysmon-config

- now open the downloads directory and copy the sysmon-config file to the sysmon extracted folder

- now right click -> open power shell and run as administrator.

- now pass the command to install sysmon
```
./sysmon64.exe -i sysmon-config.yml
```

- now your windows 10 machine is ready.

- we recommend you to take a snapshot.

- creating a vm in virtualbox for Ubuntu 22.04(wazuh and the hive)

- **please make sure to follow these instructions to create vm and make two Ubuntu vm's one is for wazuh and another one is for hive**

1. Open VirtualBox:

- Launch the VirtualBox application on your computer.
2. Click on "New":

- In the VirtualBox Manager, click the "New" button to create a new virtual machine.
3. Name and OS Type:

- Enter a name for your VM, for example, "Ubuntu 22.04."
- Choose "Linux" as the type.
- Choose "Ubuntu" as the version.
4. Memory Size (RAM):

- Select the amount of RAM you want to allocate to the VM. In this case, set it to 6GB (6144 MB).
5. Hard Disk:

- Choose "Create a virtual hard disk now" and click "Create."
6. Hard Disk File Type:

- Choose the file type for the hard disk. The default "VDI (VirtualBox Disk Image)" is suitable.
7. Storage on Physical Hard Disk:

- Choose "Dynamically allocated" for the storage on the physical hard disk.
8. File Location and Size:

- Set the location where you want to save the VM files.
- Set the size of the hard disk to 50GB.
9. Create:

- Click the "Create" button.
10. Configure VM Settings:

- With the new VM selected, click on "Settings."
11. System:

- In the "System" tab, uncheck the "Floppy" disk from the boot order as it's not needed.
12. Processor:

- In the "Processor" tab, you can adjust the number of processors as "2".
13. Display:

- In the "Display" tab, increase the "Screen" tab's video memory to a higher value (e.g., 128MB).
14. Select ISO File:

- In the "Storage" tab, under "Controller: IDE," click on the empty disk icon next to "Empty" under "Attributes."
- Click on the disk icon next to "Optical Drive" and choose "Choose a disk file..."
- Select the Ubuntu 22.04 ISO file that you've downloaded.
15. Start the VM:

- Click "OK" to save the settings.
With the VM selected, click "Start" to launch the VM.
16. Install Ubuntu:

- Follow the on-screen instructions to install Ubuntu on the VM using the ISO file.
17. Complete the Installation:

- Complete the Ubuntu installation process, including creating a user account and configuring the system

- ![wazuh vm](https://github.com/yakkalasaisumanth/SOC-automation-project/blob/main/wazuh%20settings.png)

- similar ubuntu 22.04 vm for the hive

- [thehive vm](https://github.com/yakkalasaisumanth/SOC-automation-project/blob/main/thehive%20settings.png)