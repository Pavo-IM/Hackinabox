
# Hackinabox

## MacOS VM installation using Unraid as host OS

### This guide is for the computer user who would like to run macOS 12 Monterey (or newer) as a VM from within Unraid Server OS.

### There are growing reasons for why someone would want to do so, for example:

-  When running macOS bare metal on an AMD cpu, a user is required to utilize patches made by AMD-OSX. Hackinabox abrogrates the need to use these patches.
-  When running macOS on an AMD cpu, a user has to use amdfriend to patch binaries for certain apps to run. Hackinabox mitigates this requirement.
-  Full performance/utilization of supported GPU passed through to VM.
-  Ethernet works OOB via setting up VirtIO networking device in VM settings, (no need to passthrough physical Ethernet controller).
-  No hassle updating.
-  macOS 13 Ventura compatibility

## Acknowledgements

 - [Lime Technology](https://twitter.com/limetechnology)
 - [UnRAID](https://unraid.net/)
 - [AcidAnthera, and the OpenCore Bootloader team](https://github.com/acidanthera/OpenCorePkg)
 - [Dortania Team for the OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)

## Authors of Guide / Code Contributors / Repo Maintainers

- [@Pavo-IM](https://www.github.com/Pavo-IM) (Creator of Original Repository / Code Contributor / README.md maintainer)
- [@osx86-ijb](https://www.github.com/osx86-ijb) (Creator of Modern README.md / Code Contributor / README.md maintainer)
- [@MattsCreative](https://www.github.com/RyzenDew) (Code Contributor / README.md maintainer)

## DISCLAIMER

##### THIS INFORMATION/RESEARCH HAS BEEN SHARED PURELY FOR EXPERIMENTAL AND RESEARCH PURPOSES. IT IS IN NO WAY MEANT TO PROMOTE THE CIRCUMVENTION OF ANYTHING THAT BELONGS TO AND/OR ANYTHING THAT IS THE CREATION/PRIVATE PROPERTY OF ANY CORPORATE ENTITY. THE INFORMATION THAT IS DOCUMENTED AND TRANSCRIBED HERE IS PURELY FOR EDUCATIONAL PURPOSES, AND PROOF OF CONCEPT. SHOULD YOU (OR ANYONE ELSE) CHOOSE TO UTILIZE THE INFORMATION THAT YOU'VE OBTAINED FROM THIS REPOSITORY AND THAT IS WRITTEN HERE IN ANY WAY, KNOW THAT THIS DISCLAIMER SERVES AS A LEGAL PROTECTION TO US AS THE CODE REPOSITORY CREATORS/MAINTAINERS, AND THAT WE ABSOLVE OURSELVES AS SUCH FROM ANY AND ALL RESPONSIBILITIES OR SITUATIONS THAT MIGHT ARISE FROM YOUR CHOOSING TO HAVE UTILIZED ANYTHING DISCUSSED IN THIS CODE REPOSITORY (LEGAL OR OTHERWISE).


#### Repository Hit Counter

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fosx86-ijb%2FHackinabox&count_bg=%23000000&title_bg=%234D0000&icon=github.svg&icon_color=%23E7E7E7&title=repo+views&edge_flat=false)](https://github.com/osx86-ijb/Hackinabox)


## TABLE OF CONTENTS

<br>[1) Requirements](#requirements)
<br>[2) Installation Procedurals](#installation-procedurals)
<br>- [2.1) Getting Started](#1-getting-started)
<br>- [2.2) Making the UnRAID USB](#2-making-the-unraid-usb)
<br>- [2.3) Setting Up Your Unraid Server OS Host](#3-setting-up-your-unraid-server-os-host)
<br>- [2.4) Setting up your macOS VM](#4-setting-up-your-macos-vm)
<br>[3) Making the Recovery based macOS Installer USB](#5-making-the-recovery-based-macos-installer-usb)
<br>[4) Obtaining and placing the EFI on the macOS USB Installer](#6-obtaining-and-placing-the-efi-on-the-macos-usb-installer) 
<br>[5) Installation of macOS from within your booted VM](#7-installation-of-macos-from-within-your-booted-vm)
<br>[6) Post-Installation Finalization](#8-post-installation-finalization)
<br>[7) SSDT Setup Examples - Before & After](#9-ssdt-setup-examples---before--after)
<br>[8) FAQ](#faq)
<br>- [8.1) This sounds great! Where do I begin?](#1-this-sounds-great-where-do-i-begin)
<br>- [8.2) This is great, but UnRaid says that it costs money on the website?](#2-this-is-great-but-unraid-says-that-it-costs-money-on-the-website)
<br>- [8.3) How do I access this server once I boot it up, and what's the deal with me not being able to boot into GUI mode?](#3-how-do-i-access-this-server-once-i-boot-it-up-and-whats-the-deal-with-me-not-being-able-to-boot-into-gui-mode)
<br>- [8.4) Why won't my UnRaid installation boot after I install it the first time?](#4-why-wont-my-unraid-installation-boot-after-i-install-it-the-first-time)
<br>- [8.5) Why can't I access some devices attached to my SATA controllers?](#5-why-cant-i-access-some-devices-attached-to-my-sata-controllers)
<br>- [8.6) Why don't some of my attached USB devices work?](#6-why-dont-some-of-my-attached-usb-devices-work)
<br>- [8.7) Why am I getting "VM Creation Error - XML error: Attempted double use of PCI address 0000:03:00.0"?](#7-why-am-i-getting-vm-creation-error---xml-error-attempted-double-use-of-pci-address-000003000)
<br>- [8.8) If I don't have an existing macOS installation to use to create an offline installer of macOS, yet am already booted into unRAID, what can I do to achieve such?](#8-if-i-dont-have-an-existing-macos-installation-to-use-to-create-an-offline-installer-of-macos-yet-am-already-booted-into-unraid-what-can-i-do-to-achieve-such)
<br>- [8.9) If my VM freezes and I cannot restart it properly from within the unRAID backend and am faced with the choices of hard restarting my computer, what can/should I do?](#9-if-my-vm-freezes-and-i-cannot-restart-it-properly-from-within-the-unraid-backend-and-am-faced-with-the-choices-of-hard-restarting-my-computer-what-canshould-i-do)
<br>[9) Support](#support)

## Requirements

- Any computer running UnRAID Server OS. This *technically* will work for AMD *OR* Intel, as the OpenCore config settings and XML will work for both (just bear in mind that there will need to be modifications done to the XML file for it to work properly on Intel, and that won't be discussed here in greater detail for the time being, however that might change at a later date)
- A USB Flash Key larger than 1GB, no larger than 32GB (PS: It will need to be left inserted at all times, as this is required by UnRaid Server OS)
- [The Unraid USB Flash Creator application to properly create your UnRaid Server USB key that your machine will be booted from every time.](https://unraid.net/download)
- A dedicated drive for the UnRaid Server OS to use an "Array Device". (SSD is preferrable!)
- 1 SATA controller that you don't mind having allocated to the Host OS, as it will be unusable by the VM.
- 1 USB controller that you don't mind having allocated to the Host OS, as it will be unusable by the VM.
- macOS compatible hardware to be used in macOS via hardware passthrough. (GPU, etcetera)
- [A copy of the IORegistryExplorer application for macOS that can be obtained from here:](https://github.com/khronokernel/IORegistryClone)
- [A copy of the MacIASL application that can be obtained from here:](https://github.com/acidanthera/MaciASL)
- An ability to follow detailed instructions down to a meticulously accurate and minute level.
- An ability to stay extremely calm as you follow a lengthy set of instructions.
- An unbridled/unmatched zest for all things technological.


## Installation Procedurals

### 1) Getting Started:

- 1.1) **Double check and make sure that you can adhere to the requirements listed above. If so, then proceed to follow the rest of the instructions.**
- 1.2) [Download Unraid USB Flash Creator for Mac/Windows](https://unraid.net/download)
- 1.3) **Open/Run the Unraid USB Flash Creator and follow any prompts for entering your password if asked.**
- 1.4) **Proceed with Section 2 of this guide, and continue the process of creating your Unraid USB.**


### 2) Making the Unraid USB:

- 2.1) **Under "select version", select "Stable", then Unraid 6.9.2 or whichever the newest version may be for you at your time of reading this guide.** 
<br> 

https://user-images.githubusercontent.com/67184728/198156236-db2e51e9-3bd4-4595-891c-16a8f1520ba6.mp4


- 2.2) **Next, we're going to make sure that we click on the "Allow UEFI Boot" checkbox.**
<br> 

https://user-images.githubusercontent.com/67184728/198155944-6af6bc2f-e946-4718-a72d-4125400828cf.mp4


- 2.3) **Select USB Drive that you want to create as your Unraid USB, and then click on the "Write" button under Write Image**
<br> 

https://user-images.githubusercontent.com/67184728/198157504-60163ad5-a1fb-44f7-8333-b324c92670e3.mp4


- 2.4) **Wait for USB Creator to finish creating Unraid USB and then restart machine and attempt to boot from newly created Unraid USB**


### 3) Setting Up Your Unraid Server OS Host:

- 3.1) **Boot from your Unraid USB**
- 3.2) **Select the first option to boot Unraid**
- 3.3) **Enter the username and password which will be "root"**
- 3.4) From another device connected to the same network as your booted Unraid Server OS, go to http://tower.local in your Browser of choice
- 3.5) **Select the disk to install/store the Unraid Server OS files to from the drop down menu of "Disk 1"**
<br> 

https://user-images.githubusercontent.com/67184728/198149890-3da23bca-c436-45fa-9726-a382860e9005.mp4


- 3.6) **Go the bottom of the page after selecting your disk in Disk 1 drop down menu, and click on "START" button**
<br> 

https://user-images.githubusercontent.com/67184728/198150060-8770b470-4727-4f0e-a088-424d4457c44d.mp4


- 3.7) **(It will then prompt you around the same time to confirm that you want to Format the drive, make sure to agree to such!)**
- 3.8) **After it's finished, you're going to click on the clickable text that says "Flash"** 
<br> 

https://user-images.githubusercontent.com/67184728/198150015-6b475616-b2b8-4d3a-ba7b-bc8ef3acb4e2.mp4


- 3.9) **In the page that loads afterwards, scroll down to the green text box that's labeled "Unraid OS"**
<br> ![3.9](https://i.ibb.co/fQjQLQN/04.png)
- 3.10) **Inside of the green Unraid OS text entry box field you will see** `append initrd=/bzroot` **We're going to change that to** `append pcie_acs_override=downstream,multifunction video=efifb:off initrd=/bzroot` 
<br> 

https://user-images.githubusercontent.com/67184728/198157641-7816236b-13f6-4fec-ac36-3073dfbd016a.mp4


- 3.11) **Scroll down to the bottom of the page and click the "APPLY" button**
<br> ![3.11](https://i.ibb.co/wwBTKKD/06.png)
- 3.12) **Click the button called "MAIN" at the top, and when that page loads, scroll down to the bottom, and click on the REBOOT button**
<br> 

https://user-images.githubusercontent.com/67184728/198150094-00f75a70-935b-4a2a-8f59-69d3f6a352ec.mp4


- 3.13) **Once you're booted back up into your Unraid Server OS from your Unraid Server OS USB Key, click on "TOOLS", and then "System Devices"**
<br> 

https://user-images.githubusercontent.com/67184728/198150143-afab68af-bc41-44a6-9105-e074332e57d3.mp4


- 3.14) **Once System Devices is loaded, you'll want to make sure to isolate your Unraid USB on it's own USB controller, away from all of the other peripherals. This should be able to be done in a "plug and play" way without having to reboot. However, if you find that it doesn't end up being possible to "plug and play" refresh the device list, then you can shut down and restart between each switching of the port. The reason this is done is to make the UnRaid USB isolated on it's own USB chipset.** 
<br> ![3.14](https://i.ibb.co/CBctrwh/68747470733a2f2f692e6962622e636f2f3274716d5042422f30392e706e67.png)
- 3.15) **Once that has been accomplished, make sure to select the check boxes for your GPU, GPU audio, any potential supported onboard audio chipset (if needed), as well as your network controller (WiFi), and your NVME controller (or if you have two SATA groups, bind the SSD/HDD you want), and hit the "BIND SELECTED TO VFIO AT BOOT" button at the bottom**
- 3.16) **Once that is done, you will want to go back to the "MAIN" tab and go to the bottom of the page and press the "REBOOT" button.
- 3.17) **Once you are rebooted into your Unraid Server OS from your Unraid USB again, head to the bottom of the MAIN tab and make sure to press the "START" button to start your array.**
- 3.18) **After that, we're going to go to "SETTINGS", and then select "Disk Settings"** 
<br> 

https://user-images.githubusercontent.com/67184728/198150182-a571460f-b46b-452d-a37e-7c40439c389f.mp4


- 3.19) **Once we're in the Disk Settings page, we're going to change "Enable auto start" to "Yes", then click the "APPLY" button** 
<br> 

https://user-images.githubusercontent.com/67184728/198005859-2fb95000-818e-4c16-ac3e-f688ceba477c.mov




### 4) Setting Up Your macOS VM

#### !! **DISCLAIMER: DO TAKE NOTE OF LINE COUNT PLACINGS. YOURS MIGHT NOT BE THE SAME AS IN THE GUIDE DUE TO ANY POTENTIAL DIFFERENCES IN HARDWARE CONFIGURATION. TAKE NOTE OF THIS AND BE ON THE LOOK OUT FOR THE DIFFERENCES** !!

- 4.1) **Now we're going to head to the "VMS" tab of the backend and click on the "ADD VM" button**
<br> 

https://user-images.githubusercontent.com/67184728/199256049-12d22033-fceb-4fcf-a3da-100dc5250d62.mov


- 4.2) **When the Add VM page loads, we're going to select FreeBSD** 
<br> 

https://user-images.githubusercontent.com/67184728/199256099-1ac0904d-06be-46e2-b848-cdada1c9f023.mov


- 4.3) **Once loaded, click "EDIT" button load the edit page and edit VM settings. When that next page loads, go to the "Logical CPUs" section, and select every other CPU thread combination except for CPU 0 / X (where as X is the variable for your specific core / thread count). After that, make sure to set your Max Memory. Do keep in mind that Unraid needs 4GB of RAM, so anything 4GB less than your Maximum installed RAM should be sufficient if you don't need to allocate more elsewhere. Remember to select “Q35-6.1” from the drop down selection list for “Machine” (this isn’t shown in the video below, but is still necessary!)** 
<br> 

https://user-images.githubusercontent.com/67184728/199256143-1f795f1e-9119-4941-9bd3-430f589ffd1f.mov


- 4.4) **Next, scroll down on the page and select "3.0 qemu XHCI" from the drop down menu for USB Controller. This is really personal preference regarding the selection of USB Controller, and doesn't really matter since macOS doesn't support any of them. Then make sure to select your GPU from the Graphics Card drop down menu. Then select your appropriate Audio Chipset from the drop down menu in the selection named Sound Card.** 
<br> 

https://user-images.githubusercontent.com/67184728/199256187-a3b91c7a-03a1-4df9-8bb8-e98ea8a598b3.mov


- 4.5) **Make sure that Network Bridge is set to "br0" from the dropdown menu, and Network Model is set to "virtio-net" from the appropriate drop down selection as well. Then place check marks next to the two(or more?) devices in the "Other PCI Devices" section that you want to pass through. Uncheck "Start VM after creation", and then click on the "CREATE" button PS: This is only to be done if the user isn't already passing through, or able to pass through their Ethernet Controller** 
<br> 

https://user-images.githubusercontent.com/67184728/199256227-74b9d99a-86f6-442f-b339-5661fb188a82.mov


- 4.6) **Click on FreeBSD icon, and choose "Edit". In the top right corner when the Edit page loads, you'll see a slider with the words "FORM VIEW" next to it. Then, click on that to change it to "XML VIEW".**  
<br> 

https://user-images.githubusercontent.com/67184728/199260805-f25efc51-8776-452b-9bb3-2b113fad944e.mov


- 4.7) **After the XML view has loaded, we're going to go to line 37 and remove it (the entire line, that is) per what we have selected in the image here. This is done because QEMU's topology isn't read by macOS correctly and would require the topology kernel patch from the AMD kernel patches in order for it to work otherwise**  
<br> 

https://user-images.githubusercontent.com/67184728/199260951-bb050823-f3a3-47a5-bd77-5380b89a8776.mov


- 4.8) **Next, we're going to go to line 40 and select the text ``utc``, and change it to ``localtime``:**
<br> 

https://user-images.githubusercontent.com/67184728/199261008-daaf98bb-edf2-460a-a09d-6720563d74fd.mov


- 4.9) **Scroll down to what should be line 63 and copy ``multifunction =‘on’``.**
<br> 

https://user-images.githubusercontent.com/67184728/199261077-55b1900f-30d6-4499-ad06-7f9af89ee203.mov


- 4.10) **Head to lines 130 and 132 as shown in the video and change the last zero in ``function='0x0'`` to ``function='0x1'`` to match each other since they need to be changed, also. After doing this, we're going to insert a space and paste the ``multifunction ='on'`` that we copied in the previous step.** 
<br> 

https://user-images.githubusercontent.com/67184728/199261436-3adb9436-88d9-4d99-b747-b5986ff587e6.mov


- 4.11) **Do make sure at this time to go ahead and change the `bus=0x4` to `bus=0x3` on the line for HDMI audio in order to have it match the same bus that the GPU is located on.**
<br> 

https://user-images.githubusercontent.com/67184728/199261992-4571eed0-b8d8-4e68-a1a9-8d25d5ee9f3f.mov


- 4.12) **ATTENTION: Section 4.12 is listed here to explain in further detail that Section 4.11 above is done to make your GPU and GPU audio device be seen in the macOS VM and work correctly. Therefore, in order to achieve such, we changed our GPU audio device's logical address in this XML file to match that of the GPU's. Do note that the `source` part of the  devices does not change, only the logical address under the source.**
- 4.13) **Head to the bottom of the document and click at the end of ```devices``` and hit enter/return to make a new line and paste this:**  
```
    <qemu:commandline>
    <qemu:arg value='-global'/>
    <qemu:arg value='ICH9-LPC.acpi-pci-hotplug-with-bridge-support=off'/>
    <qemu:arg value='-device'/>
    <qemu:arg value='isa-applesmc,osk=ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc'/>
    <qemu:arg value='-cpu'/>
    <qemu:arg value='Cascadelake-Server,vendor=GenuineIntel'/>
  </qemu:commandline>
```
- 4.14) **After pasting in the required text into new line, please hit the "UPDATE" button. Now after hitting update button we can shut down and reboot into our Linux Live Distro of choice and continue with making the macOS Installer, if you haven't done so previously.**


### 5) Making the Recovery based macOS Installer USB

- 5.1) [Head to the relevant Dortania OpenCore Install Guide page for making a macOS Installer USB via Linux to continue making your USB, and once finished, return here to continue on to the next section of the guide.](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html)


### 6) Obtaining and placing the EFI on the macOS USB Installer

- 6.1) **So after you've made your macOS Installer USB, please follow the OpenCore Install Guide part of their guide where one creates the OpenCore USB and places the needed files on one's USB installer EFI/ESP partition. After doing so and making sure that has been successfully done, then apply/copy/merge the OpenCore folder downloaded from this repository to your newly created macOS USB Installer's OpenCore EFI/ESP partition, making sure to MERGE and not OVERWRITE, AND to be ever mindful of the structing of both files and folder hierarchies.**


### 7) Installation of macOS from within your booted VM

- 7.1) **Create your USB based macOS installer as you normally would. I suggest using the ```createinstallmedia``` method on macOS.**
- 7.2) **Apply the EFI that can be obtained from this repo and in the previous step to the ESP partition of the created USB installer.**
- 7.3) **Start up your macOS VM, and boot from the created installer once inside of the OpenCore bootloader menu/screen.**
- 7.4) **Install your macOS version, making sure to reboot your UnRaid server if necessary along the way, and then boot into it after everything has finished!**


### 8) Post Installation Finalization

- 8.1) **Boot into your newly installed VM instance of macOS, yet again.**
- 8.2) **Once you've gone through the first time user set up and set everything up the way that you prefer and have reached the desktop UI, make sure to obtain and run "EFI Agent" by HeadKaze from their appropriate GitHub repo, making sure to grant it the permissions that it asks for when it asks.**
- 8.3) **Mount both the macOS Installer USB EFI/ESP partition that contains the OpenCore EFI files that you've booted from, and mount the Internal EFI/ESP of the installed macOS drive as well.**
- 8.4) **Copy the "EFI" folder on the root of the macOS Installer USB to the root of the installed macOS drive.**
- 8.5) **Next, we're going to want to set up our hardware via inputting the correct information and values into the correct locations using both IORegistryExplorer to obtain the appropriate Address locations and naming, and MaciASL to edit the SSDT files and place in our hardware's corresponding information. Remember to test your changes non destructively so you don't bork your EFI, and have a working backup EFI to boot from!**
- 8.6) **Mount your installed macOS EFI/ESP partiton again using EFI Agent.**
- 8.7) **Obtain and open IORegistryExplorer if you haven't already. (Preferrably the newest version if one has an Apple Developer account, although any version of at least 2.x should suffice.)**
- 8.8) **Obtain and open MaciASL if you haven't already.(Preferrably the version from Acidanthera's GitHub repo.)**
- 8.9) **Load each of the SSDT's in MaciASL, working on them one at a time, replacing the information per your own hardwares addresses and devices names, so as not to convolute the process.**
- 8.10) **In each loaded SSDT, look for the corresponding Address and Device Name, and copy both sets of information to the corresponding SSDT that you are working on. (See below in Section #9 for SSDT Setup Examples)**
- 8.11) **After all have been replaced, you should be good to go, so you'll want to go ahead and shut down your VM and restart your UnRaid Server OS again, if need be. (For Example: if one is using a Navi based GPU, there's a Vendor Reset Bug that exists. This can be mitigated by using the Vendor Reset Bug fix plugin that's availble via the appropriate location).**
- 8.12) **Go back into your UnRaid Server VM's, and start the macOS VM.**
- 8.13) **Boot from your installed version of macOS without having to use the Installer USB, and profit!**


### 9) SSDT Setup Examples - Before & After

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


### FAQ

#### 1) This sounds great! Where do I begin?
You can begin by making sure that you can adhere to the requirements outlined in this guide

#### 2) This is great, but UnRaid says that it costs money on the website?
That is correct, however there is a 30 day free trial, and the cheapest version costs $60 USD. Gotta pay to play the game, right?

#### 3) How do I access this server once I boot it up, and what's the deal with me not being able to boot into GUI mode?
In order to access your UnRaid Server OS page for the UnRaid server via tower.local (default name) or the IP address given by your network, you must connect to such from a browser on another device attached to the same network

#### 4) Why won't my UnRaid installation boot after I install it the first time?
Due to requirements from the creators of UnRaid, the USB boot key created to boot UnRaid the first time around must be both booted from every time, and left inserted in the USB port at all times

#### 5) Why can't I access some devices attached to my SATA controllers?
Per how UnRaid operates, 1 SATA controller must be allocated to the Host OS, thereby making it unsuable by a VM

#### 6) Why don't some of my attached USB devices work?
Per how UnRaid operates, 1 USB controller must be allocated to the Host OS, thereby making it unusable by a VM

#### 7) Why am I getting "VM Creation Error - XML error: Attempted double use of PCI address 0000:03:00.0"?
If you're seeing this error, maybe with a different number in the set of numbers as well, it's because function is set on the incorrect line, and bus might be set incorrectly as well. Double check your double check, and set them to the appropriate correct lines.

#### 8) If I don't have an existing macOS installation to use to create an offline installer of macOS, yet am already booted into unRAID, what can I do to achieve such?
[Use Macinabox from SpaceinvaderOne](https://github.com/SpaceinvaderOne/Macinabox)

#### 9) If my VM freezes and I cannot restart it properly from within the unRAID backend and am faced with the choices of hard restarting my computer, what can/should I do?

*Forcefully restarting the machine and or hard resetting your machine and not choosing to shut down using the option to do so in the unRAID backend can result in data corruption, and the potential need to remake the unRAID USB drive. At all costs, you should always make sure to use the SHUTDOWN button within the unRAID backend to shutdown your computer, instead of hard restarting. It also would be wise to make sure to have a 1/1 clone or backup of your unRAID installation, just in case the need should arise. However, if you do find yourself needing to hard restart your machine, there is a potential that the VM's tab will not be present when you go to look for it. If that ends up being the case/situation for you, just re-enable it from the appropriate location nested within Settings.*
  
  
### Support

For support, please join the [Official UnRaid Discord Server](https://discord.gg/unraid) or the [UnRaid Forums](https://forums.unraid.net/).
