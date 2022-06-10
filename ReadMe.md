# 01 - Install VMs

*   Installed Windows Server 2022 Core as a VM in Vmware workstation named this VM ```Windows Server 2022_Template```
*   Installed Windows 11 as a VM in VMware Workstation named this VM ```Windows11_Template```
    *   During the install process hitting Shift-F10 to pull up the command prompt and typed regedit.
    *   Navigate to Computer\HKEY_LOCAL_MACHINE\SYSTEM\Setup and create a New Key and name it LabConfig
    *  Add a new DWORD(32) value BypassTPMCheck and set the value data to 1.
    *  Add a new DWORD(32) value BypassRAMCheck and set the value data to 1.
    *  Add a new DWORD(32) value BypassSecureBootCheck and set the value data to 1

Ref: (https://winbuzzer.com/2021/10/07/how-to-install-windows-11-without-tpm-2-xcxwbt/)

*   Created a Snapshot for both VMs and tunred on the setting to allow using these hosts as clones.
*   Made one Linked clone of the Windows Server 2022 and named it DC1 TestBed
*   Made one Linked clone of the Windows 11 host and named it IT Manager.

Ref: (https://us.informatiweb-pro.net/virtualization/vmware/vmware-workstation-15-clone-virtual-machines.html)


# 02 - Setup the Domain Controller

*   Changed the hostname of the cloned Win2022 VM to DC1
*   Used SConfig to set the network configurations
    *   Setting DC1 IP to a Static IP
    *   Entering the default gateway
    *   Setting DC1's IP as the new DNS and did not set an alternative DNS.


# 03 - Setup Active Directory

*   Install Actice Directory Windows Feature

```powershell
Install-WindowsFeature AD-Domain-Services -IncludemanagementTools
```

*  Install ADDSForest

```powershell
import-Module ADDSDeployment
install-ADDSForest
```
   *  Entered in the domain you want to use. i.e. Testbed.com

*  After it reboots it will take sometime for the appliying settings to finish.
*  Next you will need to set the DNS back to the DC1 IP.

```powershell
Set-DNSClientServerAddress -InterfaceIndex 4 -ServerAddresses x.x.x.x
```
Replace x.x.x.x with the IPv4 of your DC1 host.

*  Shutdown DC1 and take a Snapshot to save the current state of the host.

#  04 - Adding Clients/ Joining a Domain

*  Create a linked clone based on ```Windows11_Template```
*  Named this linked clone ``Client1```

*  Once the ```Client1``` boot up log into the host.
*  Change the DNS to be the DC1 IP address.
```powershell
Get-DNSClientServer
```

The above will show you the current DNS and show you the Interface Index number we will need to use.
```powershell
Set-DNSClientServerAddress -InterfaceIndex X -ServerAddresses x.x.x.x
```
Replace x.x.x.x with the IPv4 of your DC1 host. Also, replace X with the Interface Index.

*  Add ```Client1``` to the Domain.
   *  PowerShell option:
   ```powershell 
   Add-Computer -DomainName Domain01 -Restart
   ```
   *  GUI option:
     Start -> Access work or school -> Connect -> "Join this device to a local Active Directory domain"

*  Shutdown ```Client1``` and take a SnapShot.



