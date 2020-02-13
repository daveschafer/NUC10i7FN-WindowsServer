# NUC10i7FN Drivers for Windows Server

## Situation & Problem

The Intel NUC10iFN is a powerful little System from Intel with an Intel 10th Gen i7-10710U Processor.  
This device would be optimal to run as a Server for virtualization.  
Unfortunately, Intel only provides Drivers for Windows 10. A Server Operating System like Windows Server 2019 would be more suitable.  
You can install Windows Server 2019, but you will have struggles with some Drivers. A lot of Drivers from the official Driverpack are supported, but some are not.

## Solution (Drivers)

I created some custom Drivers (some hacks in the INF Files) for the missing ones.  

Here is how you can install (almost) all the Drivers in Windows Server 2019

1. Download the newest Windows10 Driver Bundle and try to install **all** the Drivers in the bundle manually (some will fail, just ignore this for the moment).

https://downloadcenter.intel.com/product/188811/Intel-NUC-10-Performance-kit-NUC10i7FNH

2. Download all the content from this repository's "Drivers" Folder

3. Disable Driver Signature Enforcement (since this CustomDrivers have no valid signature anymore)

- Open Windows Settings --> Update&Security --> Recovery --> Restart now (you will restart into the Recovery Mode)
- Select "Troubleshoot"
- Select "Startup Settings and klick Restart
- On the boot screen select "Disable Driver Signature Enforcement" and press Enter

You will now start into advanced mode.  

4. Now start Windows Device Manager and select the Device under "Other Devices" with the missing Drivers.  
Open Device Properties, switch to "Details" and select the property "Hardware-ID". There should be 5 Devices with missing Drivers:  

**VEN_8086&DEV_0D4F**

    Driver: Network (LAN)  
    Custom Driver:  .\Drivers\CustomNIC  
    Version:        24.3


**VEN_8086&DEV_15E8**

    Driver:         Thunderbolt 3  
    Custom Driver:  .\Drivers\CustomThunderbolt  
    Version:        17.4.77.400

**BTH\MS_BTHPAN**

    Driver: Bluetooth PAN  
    Custom Driver:  -  

This is the Bluetooth Personal Area Network Driver.  
Select this Device in devmgr --> Update Driver --> Choose driver from my computer --> Choose from List:  
Scroll down to the provider "Microsoft" and select the Driver with the name "Personal Area Network Service".  

**VEN_8086&DEV_02F0**

    Driver: Wireless-AC 9560 Driver  
    Custom Driver:  -  

This Driver is a bit special. You first have to add the Windows Wireless Feature to your Server with the following Powershell cmd:  

    Add-WindowsFeature -Name Wireless-Networking

Then restart your NUC and install the normal Driver file (wifi_21.x.x_driver64_win10.exe) again, this should now succeed.  

**VEN_INT&DEV_34BB**

    Driver: Serial IO GPIO Host Controller (INT34BB)  
    Custom Driver:  -  

This one is also a bit special.  

- Download the SIO Driver for **NUC8** from here: https://downloadcenter.intel.com/download/28103/Intel-Serial-IO-Driver-for-Intel-NUC
- unzip it
- In device manager click on the device, select update from file and select the unzipped folder
- the driver should now be installed

5. Now for the other Drivers - click on a Device with missing Driver, select "Update Driver" and choose "Driver on Filesystem". Head to the corresponding Folder where you downloaded the custom Drivers and install it.  
If you get a "Driver not signed" warning message, just ignore it and click "install anyway".  

6. After you installed all the missing Drivers restart your NUC to exit the Recovery mode.

7. All except one driver (BT-PAN) should now be installed, congrats!


# Miscellaneous

I was **not** able to get the Bluetooth PAN Driver working till this day. But I did not notice any downsides for now

## Further Informations

https://www.niallbrady.com/2019/06/26/how-to-enable-thunderbolt-on-windows-server-2019/  
https://www.reddit.com/r/intelnuc/comments/b0emtf/windows_server_2019_core_on_nuc8/