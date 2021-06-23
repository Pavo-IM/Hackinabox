
# Hackinabox

## MacOS install using UnRaid

This guide is for the computer user who would like to run macOS inside of a VM on UnRAID in efforts of circumventing the need for multiple patches on AMD based hardware and motherboards, whilst enhancing the goals of providing the most native experience possible.

## Acknowledgements

 - [Lime Technology](https://twitter.com/limetechnology)
 - [UnRAID](https://unraid.net/)
 - [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
 - [Dortania Team](https://dortania.github.io/)

  
## Authors of Guide

- [@Pavo-IM](https://www.github.com/Pavo-IM)
- [@osx86-ijb](https://www.github.com/osx86-ijb)
- [@MattsCreative](https://twitter.com/MattsCreative1)

## Appendix

- [1) Getting Started](https://github.com/osx86-ijb/Hackinabox#1-getting-started)
- [2) Making the UnRAID USB](https://github.com/osx86-ijb/Hackinabox#2-making-the-unraid-usb)
- [3) Setting up your Host Device](https://github.com/osx86-ijb/Hackinabox#3-setting-up-your-unraid-server-os-host)
- [4) Setting up your macOS VM](https://github.com/osx86-ijb/Hackinabox#4-setting-up-your-macos-vm)
- [5) Making the Recovery USB on Linux](https://github.com/osx86-ijb/Hackinabox#5-making-the-recovery-usb-on-linux)
- [6) Making the EFI](https://github.com/osx86-ijb/Hackinabox#6-making-the-efi) 
- [7) Installation of macOS](https://github.com/osx86-ijb/Hackinabox#7-installation-of-macos)
- [8) Post-Installation](https://github.com/osx86-ijb/Hackinabox#8-post-installation-finalization--ssdt-setup)

## FAQ

#### 1) If I don't have an existing macOS installation to use to create an offline installer of macOS, yet am already booted into unRAID, what can I do to achieve such?
[Use Macinabox from SpaceinvaderOne](https://github.com/SpaceinvaderOne/Macinabox)

#### 2) If my VM freezes and I cannot restart it properly from within the unRAID backend and am faced with the choices of hard restarting my computer, what can/should I do?
**WARNING:** 

*Forcefully restarting the machine and or hard resetting your machine and not choosing to shut down using the option to do so in the unRAID backend can result in data corruption, and the potential need to remake the unRAID USB drive. At all costs, one should always make sure to use the SHUTDOWN button within the unRAID backend to shutdown your computer, instead of hard restarting. It also would be wise to make sure to have a 1/1 clone of your unRAID installation, just in case the need would arise.*

#### 3) 
  
## Features

- No need to use ANY of the AMD patches
- Full performance/utilization of supported GPU
- Ethernet works OOB via setting up VirtIO networking device, no need to passthrough physical Ethernet controllers
- No hassle updating
- macOS 12 Monterey Compatible

  
# Installation Procedurals

## 1) Getting Started:
- 1) [Download Unraid Server OS USB Creator for Mac/Windows](https://unraid.net/download)
- 2) **Run Unraid Server OS USB Creator application**

## 2) Making the Unraid USB:
- 1) **Under "select version", select "Stable", then Unraid 6.9.2 or newer version depending on when you read this** ![UnraidUSBSelectVersion_01](https://i.ibb.co/mX8cB7j/Unraid-USB-Creator-01.png)
- 2) **Click on "Customize" and check the "Allow UEFI Boot" checkbox**![UnraidUSBSelectVersion_02](https://i.ibb.co/tBkb1XG/Unraid-USB-Creator-02.png)
- 3) **Select USB Drive that you want to create as your Unraid USB, and then click on the "Write" button under Write Image**![UnraidUSBSelectVersion_03](https://i.ibb.co/CHb1j1m/Unraid-USB-Creator-03.png)
- 4) **Wait for USB Creator to finish creating Unraid USB and then restart machine and attempt to boot from newly created Unraid USB**

## 3) Setting Up Your Unraid Server OS Host:
- 1) **Boot From Your Unraid Server OS USB**
- 2) **Select The First Option To Boot Unraid**
- 3) **Enter the Username and Password = root**
- 4) From another device connected to the same network as your booted Unraid Server OS, go to http://tower.local in your Browser of choice
- 5) **Select the disk to install/store the Unraid Server OS files to from the drop down menu of "Disk 1"** ![3.5](https://i.ibb.co/pychKXD/01.png)
- 6) **Go the bottom of the page after selecting your disk in Disk 1 drop down menu, and click on "START" button** ![3.6](https://i.ibb.co/NndYd5w/02.png)
- 7) **(It will then prompt you around the same time to confirm that you want to Format the drive, make sure to agree to such!)**
- 8) **After it's finished, you're going to click on the clickable text that says "Flash"** ![3.8](https://i.ibb.co/wBd6p1m/03.png)
- 9) **In the page that loads afterwards, scroll down to the green text box that's labeled "Unraid OS"** ![3.9](https://i.ibb.co/fQjQLQN/04.png)
- 10) **Inside of the green Unraid OS text entry box field you will see** `apend initrd=/bzroot` **We're going to change that to** `append pcie_acs_override=downstream,multifunction video=efifb:off initrd=/bzroot` ![3.10](https://i.ibb.co/JzJhxv8/05.png)
- 11) **Scroll down to the bottom of the page and click the "APPLY" button** ![3.11](https://i.ibb.co/wwBTKKD/06.png)
- 12) **Click the button called "MAIN" at the top, and when that page loads, scroll down to the bottom, and click on the REBOOT button** ![3.12](https://i.ibb.co/TPP03Vw/07.png)
- 13) **Once you're booted back up into your Unraid Server OS from your Unraid Server OS USB Key, click on "TOOLS", and then "System Devices"**  
![3.13](https://i.ibb.co/wcBNDqk/08.png)
- 14) **Once System Devices is loaded, you'll want to make sure to isolate your Unraid USB on it's own USB controller, away from all of the other peripherals. This should be able to be done plug and play without having to reboot, but if it doesn't end up being possible to plug and play refresh the device list for you, then you can shut down and restart between each switching of the port, testing to see which port will allow for the Unraid USB to be isolated by itself. If needed/possible, use a rear lower port, and a USB hub with Unraid USB plugged into it if plugging it bare won't detect the USB when doing the process and attempting to reboot from Unraid USB)** ![3.14](https://i.ibb.co/2tqmPBB/09.png)
- 15) **Once that has been accomplished, make sure to select the check boxes for your GPU, GPU audio, any potential supported onboard audio chipset (if needed), as well as your network controller (WiFi), and your NVME controller (or if you have two SATA groups, bind the SSD/HDD you want), and hit the "BIND SELECTED TO VFIO AT BOOT" button at the bottom**
- 16) **Once that is done, you will want to go back to the "MAIN" tab and go to the bottom of the page and press the "REBOOT" button.
- 17) **Once you are rebooted into your Unraid Server OS from your Unraid USB again, head to the bottom of the MAIN tab and make sure to press the "START" button to start your array.**
- 18) **After that, we're going to go to "SETTINGS", and then select "Disk Settings"** ![3.18](https://i.ibb.co/Yj5GDG7/10.png)
- 19) **Once we're in the Disk Settings page, we're going to change "Enable auto start" to "Yes", then click the "APPLY" button** ![3.19](https://i.ibb.co/9cW1Ltr/11.png)

## 4) Setting Up Your macOS VM

- 1) **Now we're going to head to the "VMS" tab of the backend and click on the "ADD VM" button** ![4.1](https://i.ibb.co/hLhL1jR/12.png)
- 2) **When the Add VM page loads, we're going to select FreeBSD** ![4.2](https://i.ibb.co/NCYytQv/13.png)
- 3) **Once loaded, click "EDIT" button load the edit page and edit VM settings. When that next page loads, go to the "Logical CPUs" section, and select every other CPU thread combination except for CPU 0 / X (where as X is the variable for your specific core / thread count). After that, make sure to set your Max Memory. Do keep in mind that Unraid needs 4GB of RAM, so anything 4GB less than your Maximum installed RAM should be sufficient if you don't need to allocate more elsewhere.** ![4.3](https://i.ibb.co/0Qdsk5J/14-5.png)
- 4) **Next, scroll down on the page and select "3.0 qemu XHCI" from the drop down menu for USB Controller. This is really personal preference regarding the selection of USB Controller, and doesn't really matter since macOS doesn't support any of them. Then make sure to select your GPU from the Graphics Card drop down menu. Then select your appropriate Audio Chipset from the drop down menu in the selection named Sound Card.** ![4.4](https://i.ibb.co/ChvQdtR/14-6.png)
- 5) **Make sure that Network Bridge is set to "br0" from the dropdown menu, and Network Model is set to "virtio-net" from the appropriate drop down selection as well. Then place check marks next to the two(or more?) devices in the "Other PCI Devices" section that you want to pass through. Uncheck "Start VM after creation", and then click on the "CREATE" button PS: This is only to be done if the user isn't already passing through, or able to pass through their Ethernet Controller** ![4.5](https://i.ibb.co/wdP7V24/14-7.png)
- 6) **Click on FreeBSD icon, and choose "Edit". In the top right corner when the Edit page loads, you'll see a slider with the words "FORM VIEW" next to it.**  \
![4.6.1](https://i.ibb.co/4jFdyLP/14-8.png)  
**Then, click on that to change it to "XML VIEW".**  \
![4.6.2](https://i.ibb.co/t826xfx/14-9.png)
- 7) **After the XML view has loaded, we're going to go to line 37 and remove it (the entire line, that is) per what we have selected in the image here. This is done because QEMU's topology isn't read by macOS correctly and would require the topology kernel patch from the AMD kernel patches in order for it to work otherwise**  
![4.7](https://i.ibb.co/Qbq5j1R/15.png)
- 8) **Next, we're going to go to line 40 and select the text "utc",** ![4.8.1](https://i.ibb.co/xYFgx8D/17.png)  
**and change it to "localtime"**  
![4.8.2](https://i.ibb.co/5ry53yH/18.png)
- 9) **Scroll down to what should be line 63 and copy multifunction =‘on’,** ![4.9.1](https://i.ibb.co/GTCJncP/19.png) **and paste it at the end of line 118 as shown in the corresponding picture, making sure to change the last zero in function='0x0' to function='0x1' after you're done doing the prior pasting job, since it needs to be changed, also.** ![4.9.2](https://i.ibb.co/MZDrV16/20.png)
- 10) **On the line below 120 it says bus=‘0x03’ well at 127 change bus=‘0x04’ to 0x03 to match the gpu thats sets it as one device so the audio works.** ![4.10](https://i.ibb.co/MZDrV16/20.png) **Steps number 9 and number 10 are done to make your GPU and GPU audio device be seen to macOS as one device. The multifunction argument tells the hypervisor to allow multiple devices to operate on the same bus. Only the GPU audio device must match the GPU device bus and slot. The GPU audio device must use the function 0x1**
- 11) **Head to the bottom on line 144 click the end of </devices> and hit enter to make a new line (145) and paste this:** ![4.11](https://i.ibb.co/P5YDmZy/22.png) 
```
     <qemu:commandline>  
     <qemu:arg value='-device'/>  
     <qemu:arg value='isa-applesmc,osk=ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc'/>  
     <qemu:arg value='-smbios'/>  
     <qemu:arg value='type=2'/>  
     <qemu:arg value='-cpu'/>  
     <qemu:arg value='host,vendor=GenuineIntel,+invtsc,kvm=on'/>  
     </qemu:commandline>  
```
- 12) **After pasting in the required text into new line 145, please hit the "UPDATE" button. Now after hitting update button we can shut down and reboot into our Linux Live Distro of choice and continue with making the macOS Installer, if one already doesn't have one made previously.**

## 5) Making the Recovery USB on Linux

- 1) [Head to the OpenCore Install Guide page for making a macOS Installer USB via Linux to continue making your USB, and once finished, return here to continue](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html)

## 6) Making the EFI

- 1) [So after you've made your macOS Installer USB, please apply the OpenCore EFI from here to your macOS USB Installer's ESP/EFI partition, utilizing the proper folder hierarchies.](https://cdn.discordapp.com/attachments/469592019384270858/855930439343931452/EFI.zip)

## 7) Installation of macOS

- 1) 
- 2)
- 3)
- 4)

## 8) Post Installation Finalization / SSDT Setup

- 1)
- 2)
- 3)
- 4)
- 5)
- 6)
- 7)
- 8)
- 9)
- 10)

# Support

For support, please join the AMD-OSX Discord Server and ask your question there, or join and ask at the AMD-OSX.com forums! One can also seek out assistance regarding Unraid related questions at the Unraid forums! Thank you, enjoy, and regards!
