
# Hackinabox

## MacOS install using UnRaid

This guide is for the AMD motherboard/cpu user who would like to run macOS from a VM inside of UnRAID Server OS, in efforts of circumventing the need for multiple patches on AMD based hardware and motherboards when booting bare metal from OpenCore.

## Acknowledgements

 - [Lime Technology](https://twitter.com/limetechnology)
 - [UnRAID](https://unraid.net/)
 - [AcidAnthera, and the OpenCore Bootloader team](https://github.com/acidanthera/OpenCorePkg)
 - [Dortania Team for the OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
  
## Authors of Guide

- [@Pavo-IM](https://www.github.com/Pavo-IM)
- [@osx86-ijb](https://www.github.com/osx86-ijb)
- [@MattsCreative](https://www.youtube.com/c/MattsCreative)

## DISCLAIMER

THIS INFORMATION/RESEARCH HAS BEEN DONE AND SHARED PURELY FOR EXPERIMENTAL AND RESEARCH PURPOSES, AND IS IN NO MAY MEANT TO PROMOTE CIRCUMVENTING OF ANYTHING THAT IS SOMEONE ELSE'S CORPORATE PRIVATE PROPERTY. THE INFORMATION LISTED HERE IS PURELY FOR EDUCATIONAL PURPOSES AND SHOULD YOU CHOOSE TO UTILIZE IT IN ANY WAY, I AM IN NO WAY RESPONSIBLE FOR ANY INJUNCTIONS/PROBLEMS THAT MAY OR MAY NOT ARISE AND/OR BE BROUGHT AGAINST ANOTHER FOR THEIR CHOOSING TO HAVE DONE SO.

## Appendix

- [1) Getting Started](https://github.com/osx86-ijb/Hackinabox#1-getting-started)
- [2) Making the UnRAID USB](https://github.com/osx86-ijb/Hackinabox#2-making-the-unraid-usb)
- [3) Setting up your Host Device](https://github.com/osx86-ijb/Hackinabox#3-setting-up-your-unraid-server-os-host)
- [4) Setting up your macOS VM](https://github.com/osx86-ijb/Hackinabox#4-setting-up-your-macos-vm)
- [5) Making the Recovery USB on Linux](https://github.com/osx86-ijb/Hackinabox#5-making-the-recovery-usb-on-linux)
- [6) Making the EFI](https://github.com/osx86-ijb/Hackinabox#6-making-the-efi) 
- [7) Installation of macOS](https://github.com/osx86-ijb/Hackinabox#7-installation-of-macos)
- [8) Post-Installation Finalization](https://github.com/osx86-ijb/Hackinabox#8-post-installation-finalization--ssdt-setup)
- [9) SSDT Setup Examples - Before & After](https://github.com/osx86-ijb/Hackinabox#9-ssdt-setup-examples---before--after)

## FAQ

### 1) This sounds great! Where do I begin?
You can begin by making sure that you can adhere to the requirements outlined in this guide

### 2) This is great, but UnRaid says that it costs money on the website?
That is correct, but as with all good things in life, there is a 30 day free trial, and the cheapest version costs a one time $60 USD payment. Gotta pay to play the game, right?

### 3) How do I access this server once I boot it up, and what's the deal with me not being able to boot into GUI mode?
In order to access your UnRaid Server OS page for the UnRaid server via tower.local (default name) or the IP address given by your network, you must connect to such from a browser on another device attached to the same network

### 4) Why won't my UnRaid installation boot after I install it the first time?
Due to requirements from the creators of UnRaid, the USB boot key created to boot UnRaid the first time around must be both booted from every time, and left inserted in the USB port at all times

### 5) Why can't I access some devices attached to my SATA controllers?
Per how UnRaid operates, one SATA controller must be allocated to the Host OS, thereby making it unsuable by a VM

### 6) Why don't some of my attached USB devices work?
Per how UnRaid operates, one USB controller must be allocated to the Host OS, thereby making it unusable by a VM

### 7) Why am I getting "VM Creation Error - XML error: Attempted double use of PCI address 0000:03:00.0"?
If you're seeing this error, maybe with a different number in the set of numbers as well, it's because function is set on the incorrect line, and bus might be set incorrectly as well. Double check your double check, and set them to the appropriate correct lines.

### 8) If I don't have an existing macOS installation to use to create an offline installer of macOS, yet am already booted into unRAID, what can I do to achieve such?
[Use Macinabox from SpaceinvaderOne](https://github.com/SpaceinvaderOne/Macinabox)

### 9) If my VM freezes and I cannot restart it properly from within the unRAID backend and am faced with the choices of hard restarting my computer, what can/should I do?


**NOTE:** 

*Forcefully restarting the machine and or hard resetting your machine and not choosing to shut down using the option to do so in the unRAID backend can result in data corruption, and the potential need to remake the unRAID USB drive. At all costs, one should always make sure to use the SHUTDOWN button within the unRAID backend to shutdown your computer, instead of hard restarting. It also would be wise to make sure to have a 1/1 clone or backup of your unRAID installation, just in case the need should arise.*
*If you find yourself needing to hard restart your machine, there is a potential that the VM's tab will not be present when you go to look for it. If that ends up being the case/situation for you, just re-enable it from the appropriate location nested within Settings.*
  
## Features

- No need to use ANY of the AMD patches
- Full performance/utilization of supported GPU
- Ethernet works OOB via setting up VirtIO networking device, no need to passthrough physical Ethernet controllers
- No hassle updating
- macOS 12 Monterey Compatible

## Requirements

- An AMD based motherboard (AM4 or newer?)
- An AMD based central processing unit (Ryzen or newer?)
- A USB Flash Key larger than 1GB, no larger than 32GB (PS: It will need to be left inserted at all times, as this is required by UnRaid Server OS)
- [The Unraid Server OS USB Creator application to properly create your UnRaid Server USB key that your machine will be booted from every time.](https://unraid.net/download)
- A dedicated drive for the UnRaid Server OS (SSD?)
- One SATA controller that you don't mind having allocated to the Host OS, as it will be unusable by the VM.
- One USB controller that you don't mind having allocated to the Host OS, as it will be unusable by the VM.
- [A copy of the IORegistryExplorer application for macOS that can be obtained from here:](https://github.com/khronokernel/IORegistryClone)
- [A copy of the MacIASL application that can be obtained from here:](https://github.com/acidanthera/MaciASL)
- An ability to follow detailed instructions down to a meticulously accurate and finite level.
- An ability to stay extremely calm as you follow a lengthy set of instructions.
- An unbridled/unmatched zest for all things technological.

# Installation Procedurals

## 1) Getting Started:

- 1.1) **Double check and make sure that you can adhere to the requirements listed above. If so, then proceed to follow the rest of the instructions.**
- 1.2) [Download Unraid Server OS USB Creator for Mac/Windows](https://unraid.net/download)
- 1.4) **Open/Run the Unraid Server OS USB Creator.**
- 1.5) **Proceed with the next step (#2), and continue from there.**

## 2) Making the Unraid USB:

- 2.1) **Under "select version", select "Stable", then Unraid 6.9.2 or newer version depending on when you read this** 
<br> ![UnraidUSBSelectVersion_01](https://i.ibb.co/cJ5ST55/Unraid-USBCreator-01.png)
- 2.2) **Click on "Customize" and check the "Allow UEFI Boot" checkbox**
<br> ![UnraidUSBSelectVersion_02](https://i.ibb.co/gjtpsd0/Unraid-USBCreator-02.png)
- 2.3) **Select USB Drive that you want to create as your Unraid USB, and then click on the "Write" button under Write Image**
<br> ![UnraidUSBSelectVersion_03](https://i.ibb.co/bJwHGVk/Unraid-USBCreator-03.png)
- 2.4) **Wait for USB Creator to finish creating Unraid USB and then restart machine and attempt to boot from newly created Unraid USB**


## 3) Setting Up Your Unraid Server OS Host:

- 3.1) **Boot From Your Unraid Server OS USB**
- 3.2) **Select The First Option To Boot Unraid**
- 3.3) **Enter the Username and Password = root**
- 3.4) From another device connected to the same network as your booted Unraid Server OS, go to http://tower.local in your Browser of choice
- 3.5) **Select the disk to install/store the Unraid Server OS files to from the drop down menu of "Disk 1"**
<br> ![3.5](https://i.ibb.co/pychKXD/01.png)
- 3.6) **Go the bottom of the page after selecting your disk in Disk 1 drop down menu, and click on "START" button**
<br> ![3.6](https://i.ibb.co/NndYd5w/02.png)
- 3.7) **(It will then prompt you around the same time to confirm that you want to Format the drive, make sure to agree to such!)**
- 3.8) **After it's finished, you're going to click on the clickable text that says "Flash"** 
<br> ![3.8](https://i.ibb.co/wBd6p1m/03.png)
- 3.9) **In the page that loads afterwards, scroll down to the green text box that's labeled "Unraid OS"**
<br> ![3.9](https://i.ibb.co/fQjQLQN/04.png)
- 3.10) **Inside of the green Unraid OS text entry box field you will see** `apend initrd=/bzroot` **We're going to change that to** `append pcie_acs_override=downstream,multifunction video=efifb:off initrd=/bzroot` 
<br> ![3.10](https://i.ibb.co/JzJhxv8/05.png)
- 3.11) **Scroll down to the bottom of the page and click the "APPLY" button**
<br> ![3.11](https://i.ibb.co/wwBTKKD/06.png)
- 3.12) **Click the button called "MAIN" at the top, and when that page loads, scroll down to the bottom, and click on the REBOOT button**
<br> ![3.12](https://i.ibb.co/TPP03Vw/07.png)
- 3.13) **Once you're booted back up into your Unraid Server OS from your Unraid Server OS USB Key, click on "TOOLS", and then "System Devices"**
<br> ![3.13](https://i.ibb.co/wcBNDqk/08.png)
- 3.14) **Once System Devices is loaded, you'll want to make sure to isolate your Unraid USB on it's own USB controller, away from all of the other peripherals. This should be able to be done plug and play without having to reboot, but if it doesn't end up being possible to plug and play refresh the device list for you, then you can shut down and restart between each switching of the port, testing to see which port will allow for the Unraid USB to be isolated by itself. If needed/possible, use a rear lower port. (Feel free to use a USB hub with Unraid USB plugged into it if hot plugging it bare won't detect the USB when doing the process, or when attempting to reboot from Unraid USB)** 
<br> ![3.14](https://i.ibb.co/2tqmPBB/09.png)
- 3.15) **Once that has been accomplished, make sure to select the check boxes for your GPU, GPU audio, any potential supported onboard audio chipset (if needed), as well as your network controller (WiFi), and your NVME controller (or if you have two SATA groups, bind the SSD/HDD you want), and hit the "BIND SELECTED TO VFIO AT BOOT" button at the bottom**
- 3.16) **Once that is done, you will want to go back to the "MAIN" tab and go to the bottom of the page and press the "REBOOT" button.
- 3.17) **Once you are rebooted into your Unraid Server OS from your Unraid USB again, head to the bottom of the MAIN tab and make sure to press the "START" button to start your array.**
- 3.18) **After that, we're going to go to "SETTINGS", and then select "Disk Settings"** 
<br> ![3.18](https://i.ibb.co/Yj5GDG7/10.png)
- 3.19) **Once we're in the Disk Settings page, we're going to change "Enable auto start" to "Yes", then click the "APPLY" button** 
<br> ![3.19](https://i.ibb.co/9cW1Ltr/11.png)


## 4) Setting Up Your macOS VM

# !! **DISCLAIMER: YOUR LINE COUNT PLACINGS MIGHT NOT BE THE SAME AS IN THE GUIDE DUE TO ANY POTENTIAL DIFFERENCES IN HARDWARE CONFIGURATION, TAKE NOTE OF THIS AND BE ON THE LOOK OUT FOR THE DIFFERENCES** !!

- 4.1) **Now we're going to head to the "VMS" tab of the backend and click on the "ADD VM" button**
<br> ![4.1](https://i.ibb.co/hLhL1jR/12.png)
- 4.2) **When the Add VM page loads, we're going to select FreeBSD** 
<br> ![4.2](https://i.ibb.co/NCYytQv/13.png)
- 4.3) **Once loaded, click "EDIT" button load the edit page and edit VM settings. When that next page loads, go to the "Logical CPUs" section, and select every other CPU thread combination except for CPU 0 / X (where as X is the variable for your specific core / thread count). After that, make sure to set your Max Memory. Do keep in mind that Unraid needs 4GB of RAM, so anything 4GB less than your Maximum installed RAM should be sufficient if you don't need to allocate more elsewhere.** 
<br> ![4.3](https://i.ibb.co/0Qdsk5J/14-5.png)
- 4.4) **Next, scroll down on the page and select "3.0 qemu XHCI" from the drop down menu for USB Controller. This is really personal preference regarding the selection of USB Controller, and doesn't really matter since macOS doesn't support any of them. Then make sure to select your GPU from the Graphics Card drop down menu. Then select your appropriate Audio Chipset from the drop down menu in the selection named Sound Card.** 
<br> ![4.4](https://i.ibb.co/ChvQdtR/14-6.png)
- 4.5) **Make sure that Network Bridge is set to "br0" from the dropdown menu, and Network Model is set to "virtio-net" from the appropriate drop down selection as well. Then place check marks next to the two(or more?) devices in the "Other PCI Devices" section that you want to pass through. Uncheck "Start VM after creation", and then click on the "CREATE" button PS: This is only to be done if the user isn't already passing through, or able to pass through their Ethernet Controller** 
<br> ![4.5](https://i.ibb.co/wdP7V24/14-7.png)
- 4.6) **Click on FreeBSD icon, and choose "Edit". In the top right corner when the Edit page loads, you'll see a slider with the words "FORM VIEW" next to it.**  
<br> ![4.6.1](https://i.ibb.co/4jFdyLP/14-8.png)  
**Then, click on that to change it to "XML VIEW".**
<br> ![4.6.2](https://i.ibb.co/t826xfx/14-9.png)
- 4.7) **After the XML view has loaded, we're going to go to line 37 and remove it (the entire line, that is) per what we have selected in the image here. This is done because QEMU's topology isn't read by macOS correctly and would require the topology kernel patch from the AMD kernel patches in order for it to work otherwise**  
<br> ![4.7](https://i.ibb.co/Qbq5j1R/15.png)
- 4.8) **Next, we're going to go to line 40 and select the text "utc",** ![4.8.1](https://i.ibb.co/xYFgx8D/17.png)  
**and change it to "localtime"**  
<br> ![4.8.2](https://i.ibb.co/5ry53yH/18.png)
- 4.9) **Scroll down to what should be line 63 and copy multifunction =‘on’,** <br> ![4.9.1](https://i.ibb.co/GTCJncP/19.png) <br> **and paste it at the end of line 118 as shown in the corresponding picture, making sure to change the last zero in function='0x0' to function='0x1' after you're done doing the prior pasting job, since it needs to be changed, also.** 
<br>![4.9.2](https://i.ibb.co/MZDrV16/20.png)
- 4.10) **The line below "hostdev1" should be your GPU. On 174 where it says bus=‘0x04’, we're going to change bus=‘0x04’ to 0x03 to match up with our GPU so that it will make the audio output of the GPU work within macOS. Do also make sure to change function='0x0' to '0x1' while we're at it.**
<br> ![4.10](https://i.imgur.com/DizvGhc.png) 
- **Steps number 9 and number 10 are done to make your GPU and GPU audio device be seen to macOS as one device. The multifunction argument tells the hypervisor to allow multiple devices to operate on the same bus. Only the GPU audio device must match the GPU device bus and slot. The GPU audio device must use the function 0x1 as shown above.**
- 4.11) **Head to the bottom of the document and on line 144 click at the end of </devices> and hit enter/return to make a new line (145) and paste this:**  
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
- 4.12) **After pasting in the required text into new line 145, please hit the "UPDATE" button. Now after hitting update button we can shut down and reboot into our Linux Live Distro of choice and continue with making the macOS Installer, if one already doesn't have one made previously.**


## 5) Making the Recovery USB on Linux

- 5.1) [Head to the OpenCore Install Guide page for making a macOS Installer USB via Linux to continue making your USB, and once finished, return here to continue](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html)


## 6) Obtaining and placing the EFI on the macOS USB Installer

- 6.1) [So after you've made your macOS Installer USB, please apply the OpenCore EFI from here to your macOS USB Installer's ESP/EFI partition, utilizing the proper folder hierarchies.](https://cdn.discordapp.com/attachments/469592019384270858/855930439343931452/EFI.zip)


## 7) Installation of macOS from within your booted VM

- 7.1) **Create your USB based macOS installer as you normally would. I suggest using the ```createinstallmedia``` method on macOS.**
- 7.2) **Apply the EFI that can be obtained from this repo and in the previous step to the ESP partition of the created USB installer.**
- 7.3) **Start up your macOS VM, and boot from the created installer once inside of the OpenCore bootloader menu/screen.**
- 7.4) **Install your macOS version, making sure to reboot your UnRaid server if necessary along the way, and then boot into it after everything has finished!**


## 8) Post Installation Finalization

- 8.1) **Boot into your newly installed VM instance of macOS, yet again.**
- 8.2) **Once you've gone through the first time user set up and set everything up the way that you prefer and have reached the desktop UI, make sure to obtain and run "EFI Agent" by HeadKaze from their appropriate GitHub repo, making sure to grant it the permissions that it asks for when it asks.**
- 8.3) **Mount both the macOS Installer USB EFI/ESP partition that contains the OpenCore EFI files that you've booted from, and mount the Internal EFI/ESP of the installed macOS drive as well.**
- 8.4) **Copy the "EFI" folder on the root of the macOS Installer USB to the root of the installed macOS drive.**
- 8.5) **Next, we're going to want to set up our hardware via inputting the correct information and values into the correct locations using both IORegistryExplorer to obtain the appropriate Address locations and naming, and MaciASL to edit the SSDT files and place in our hardware's corresponding information. Remember to test your changes non destructively so you don't bork your EFI, and have a working backup EFI to boot from!**
- 8.6) **Mount your installed macOS EFI/ESP partiton again using EFI Agent.**
- 8.7) **Obtain and open IORegistryExplorer if you haven't already. (Preferrably the newest one if one has an Apple Developer account and it's possible, although any version of at least 2.x should suffice.)**
- 8.8) **Obtain and open MaciASL if you haven't already.(Preferrably the version from Acidanthera's GitHub repo.)**
- 8.9) **Load each of the SSDT's in MaciASL, working on them one at a time, replacing the information per your own hardwares addresses and devices names, so as not to convolute the process.**
- 8.10) **In each loaded SSDT, look for the corresponding Address and Device Name, and copy both sets of information to the corresponding SSDT that you are working on. (See below in Section #9 for SSDT Setup Examples)**
- 8.11) **After all have been replaced, you should be good to go, so you'll restart your VM and UnRaid Server OS again, if need be. (For Example: if one is using a Navi based GPU, as there's a Reset Bug that exists. Plus it's just a good practice to do so, anyway).**
- 8.12) **Go back into your UnRaid Server VM's, and start the macOS VM.**
- 8.13) **Boot from your installed version of macOS without having to use the Installer USB, and profit!**


## 9) SSDT Setup Examples - Before & After

- 9.1a) **SSDT-ARPT.aml Before Editing:** ![9.1a](https://i.ibb.co/1bVw8XP/SSDT-ARPT-aml.png)  
- 9.1b) **SSDT-ARPT.aml After Editing:** ![9.1b](https://i.ibb.co/3m211Ft/SSDT-ARPT-aml.png)
- 9.2a) **SSDT-GFX.aml Before Editing:** ![9.2a](https://i.ibb.co/sJw0fpp/SSDT-GFX-aml.png)
- 9.2b) **SSDT-GFX.aml After Editing:** ![9.2b](https://i.ibb.co/XD6Wn46/SSDT-GFX-aml.png)
- 9.3a) **SSDT-NVME.aml Before Editing:** ![9.3a](https://i.ibb.co/sWBjgNB/SSDT-NVME-aml.png)
- 9.3b) **SSDT-NVME.aml After Editing:** ![9.3b](https://i.ibb.co/zXC19jX/SSDT-NVME-aml.png)
- 9.4a) **SSDT-PLUG.aml Before Editing:** ![9.4a](https://i.ibb.co/9Y8XyCT/SSDT-PLUG-aml.png)
- 9.4b) **SSDT-PLUG.aml After Editing:** ![9.4b](https://i.ibb.co/TBTmc5s/SSDT-PLUG-aml.png)
- 9.5a) **SSDT-XGE.aml Before Editing:** ![!9.5a](https://i.ibb.co/j5ssrxd/SSDT-XGE-aml.png)
- 9.5b) **SSDT-XGE.aml After Editing:** ![9.5b](https://i.ibb.co/mqyPbc0/SSDT-XGE-aml.png)
- 9.6a) **SSDT-XHC.aml Before Editing:** ![9.6a](https://i.ibb.co/cT5RmmF/SSDT-XHC-aml.png)


# Support

For support, please join the AMD-OSX Discord Server and ask your question there, or you could also join the UnRaid Server OS Discord Server! One can also seek out assistance regarding Unraid related questions at the Unraid forums! Thank you, enjoy, and regards!
