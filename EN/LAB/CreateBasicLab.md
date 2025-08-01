# Create a Basic Active Directory Lab
This guide walks you through setting up a minimal Active Directory lab environment for simulating Windows domain attacks and defensive detection. It includes one domain controller, two Windows 11 workstations, and one Kali Linux machine — all placed within the same subnet.

>⚠️ Note: This is a quick and pragmatic setup guide intended for readers with basic IT and networking knowledge (e.g. IP addressing, virtualization, Windows installation). If you struggle to follow along, it's strongly recommended to solidify those fundamentals first, as they are essential for understanding the techniques demonstrated in later stages.


## 1. Lab Overview

### 1.1 Lab Objectives
- Simulate real-world domain environments
- Demonstrate credential theft and detection
- Serve as foundation for blue and red team exercises

### 1.2 Lab Topology
![Lab Topology](./Screenshots/basic_topology.png)

## 2. Resources

### Operating System ISOs

| Name                | Link                                                                 |
|---------------------|----------------------------------------------------------------------|
| Windows Server 2025 | https://www.microsoft.com/en-us/evalcenter/download-windows-server  |
| Windows 11 Pro      | https://www.microsoft.com/software-download/windows11               |
| Kali Linux          | https://www.kali.org/get-kali/                                       |

### Microsoft Tools

| Name              | Link                                                                                  |
|-------------------|----------------------------------------------------------------------------------------|
| Sysinternals Suite | https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite          |
| RSAT Tools*         | https://learn.microsoft.com/en-us/windows-server/remote/remote-server-administration-tools |

\* RSAT Tools are optional as we config the DC locally

### Security Tools

| Name      | Link                                                      |
|-----------|-----------------------------------------------------------|
| Responder | https://github.com/lgandx/Responder                       |
| Wireshark | https://www.wireshark.org/                                |
| Hashcat   | https://hashcat.net/hashcat/                              |
| Mimikatz  | https://github.com/gentilkiwi/mimikatz                    |


## 3. Virtual Machine Setup

### 3.1 Platform Requirements
- Recommended: VirtualBox, VMware Workstation, or Hyper-V

### 3.2 VM Configuration

| VM Name     | Role                | OS Version            | CPU | RAM  | Disk  |
|-------------|---------------------|------------------------|-----|------|--------|
| DC01        | Domain Controller   | Windows Server 2025    | 2   | 4 GB | 40 GB  |
| WIN11-CL01  | Workstation Client  | Windows 11 Pro         | 2   | 4 GB | 40 GB  |
| KALI01      | Attacker VM         | Kali Linux (latest)    | 2   | 2 GB | 20 GB  |


## 4. Network Configuration

### 4.1 Network Options in VMware Workstation

VMware offers several types of virtual networks. Choosing the right one is critical to how your lab behaves — especially for isolation, visibility, and control.

| Network Type         | Description                                                                                   | Internet Access | Use Case                            |
|----------------------|-----------------------------------------------------------------------------------------------|------------------|--------------------------------------|
| [**NAT**](https://docs.vmware.com/en/VMware-Workstation-Pro/17.0/com.vmware.ws.using.doc/GUID-9D66C22F-4B79-4CE1-866E-9BA62F68E1F2.html) | VMs share the host's IP and access the internet through a network address translation (NAT) layer. | Yes              | For general use with updates         |
| **Bridged**          | VMs appear as full devices on the same network as the host.                                   | Yes              | For integration with LAN             |
| **Host-Only**        | VMs can communicate with each other and the host, but **not the internet**.                   | No               | Isolated testing with host access    |
| **Custom (VMnetX)**  | Manually created host-only or isolated networks                                               | Depends          | Used for multiple lab setups         |
| **LAN Segment**      | VMs can communicate only with other VMs in the same segment — completely isolated from host.  | No               | Ideal for isolated internal subnets  |


### 4.2 Why We Use LAN Segments for This Lab

For this lab, we intentionally isolate all virtual machines from the internet, the host, and any external network by using **VMware LAN segments**. This offers:

- **Full isolation:** No network traffic can leave the segment — perfect for red teaming or malware testing
- **Segmented subnets:** Each LAN segment acts like its own internal switch — useful for modeling multi-subnet topologies
- **Control:** You define routing, DHCP, and DNS manually (e.g. via a domain controller or firewall VM)
- **Safety:** Absolutely no accidental traffic leaks to the host or outside world

This setup is ideal for simulating internal attack paths (e.g. lateral movement, privilege escalation) or testing intrusion detection — all within a fully contained environment.
> Note: There is a way to enable Internet for this setup. This will be explained in-detail in the Guide for an advanced Lab Setup where we'll extend with Firewalls and a DMZ.

### 4.3 Create a LAN Segment in VMware

1. Open **VMware Workstation**
2. Go to the VM’s **Settings > Network Adapter**
3. Select **“LAN Segment”**
4. Click **“LAN Segments...”** and create a new segment (e.g., `INTERNAL_NET`)
5. Repeat for each VM, assigning the same segment name

> Unlike Host-Only or NAT networks, LAN segments do not use the Virtual Network Editor and offer true VM-to-VM isolation without host connectivity.

<pic>

### 4.4 Assign the Network to All VMs

- Open settings for each VM: `DC01`, `WIN11-CL01`, and `KALI01`
- Set **Network Adapter** to **"Custom" > VMnet2** (or your selected VMnet)
- Ensure **all three VMs** use the same custom host-only network


### 4.5 Assign Static IP Addresses

Assign the following IPs manually inside each operating system:

| Hostname     | IP Address       | Notes                      |
|--------------|------------------|----------------------------|
| DC01         | 192.168.56.10    | Domain Controller          |
| WIN11-CL01   | 192.168.56.11    | Windows workstation        |
| WIN11-CL02   | 192.168.56.12    | Windows workstation        |
| KALI01       | 192.168.56.15    | Kali attacker (Responder)  |

> Tip: After configuration, test connectivity with `ping` between all systems before proceeding.


## 5. Windows Server 2025 Setup (DC01)

### 5.1 Install Windows Server

Start by installing Windows Server 2025 on the virtual machine labeled `DC01`. This machine will serve as your domain controller.

### Installation Steps (Summary)

1. Create a new VM in VMware Workstation
2. Mount the Windows Server 2025 ISO (see [Resources](#2-resources))
3. Boot the VM and follow the on-screen setup
4. Choose:
   - Edition: **Standard Edition (Desktop Experience)** or equivalent
   - Language and region settings as preferred
5. Create a local Administrator password (secure but memorable)
6. Once installed, install VMware Tools and reboot

> Ensure you assign the machine a static IP before promoting it to a domain controller in later steps.

### Need Visual Help?

If you're unfamiliar with installing Windows Server, follow this video tutorial:

[Basic Windows Server 2025 Installation Guide (YouTube)](https://youtu.be/fbfSKgV2in8?si=7LoS7RCeenfELb87)

This video covers the base install process step-by-step.

### 5.2 Configure Hostname and Static IP
#### Set Hostname
##### Powershell
```powershell
Rename-Computer -NewName "DC01" -Force -Restart
```
##### GUI
`Start > System > Rename this PC` 
Enter the Name > hit "Next" > hit "Restart now" > Choose any Restart Reason > Continue  

<img width="801" height="455" alt="image" src="https://github.com/user-attachments/assets/3c99e323-9fde-47dd-ac38-de71ca4cdcf1" />  

---

#### Set Static IP Address
##### Powershell
```powershell
# Get Network Adapter Name
Get-NetAdapter

# Set static IP on Interface "Ethernet"
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.56.10 -PrefixLength 24 -DefaultGateway 192.168.56.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 127.0.0.1
```
###### Description
This assigns:
- IP: 192.168.56.10
- Subnet: 255.255.255.0 (/24)
- Gateway: 192.168.56.1 (can be a dummy if no internet is needed)
- DNS Server: localhost (127.0.0.1) — required before promoting to a DC

##### GUI:
`Search > View Network Connections > Select Ethernet0 with double-click > Select Properties > Select Internet Protocol Version 4 (TCP/IPv4) with double click > Change Settings as in Picture > Confirm with OK > Close opened Windows` 

<img width="399" height="454" alt="image" src="https://github.com/user-attachments/assets/b6594e2d-5801-4c3c-9602-ac635f2a55e9" />  

---

### 5.3 Install AD DS & DHCP Role
##### Powershell
```powershell
# Install Active Directory Domain Services and DHCP
Install-WindowsFeature -Name AD-Domain-Services, DHCP -IncludeManagementTools
```
##### GUI
`Server Manager > Manage > Add Roles and Features > Next > Next > Next > Check Active Directory Domain Services > Confirm Popup with Add Features > Check DHCP Server > Confirm Popup with Add Features > Click next until Install becomes selectable > Click Install > Wait for finish`  

<img width="433" height="514" alt="image" src="https://github.com/user-attachments/assets/ce8ccee6-9602-45e0-967c-22b66046b2b5" />  

---
### 5.4 Promote to Domain Controller (`discordia.local`)
##### Powershell
```powershell
Install-ADDSForest `
    -DomainName "discordia.lab" `
    -DomainNetbiosName "DISCORDIA" `
    -SafeModeAdministratorPassword (Read-Host -AsSecureString "Enter DSRM password") `
    -InstallDNS `
    -Force
```
> __Note:__ After completion the Server will restart.

##### GUI
Server Manager > Click the flag (should contain a yellow warning sign) > Select **Promote this server to a domain controller** > Select **Add a new forest** > Enter domain name `discordia.lab` > Next > Leave settings as-is and enter the **DSRM password** > Leave **DNS delegation** unchecked > Next > Leave **NetBIOS name** unchanged > Next > Leave the **AD DS database paths** unchanged > Next > Validate your review summary thoroughly > Next > Ignore the DNS delegation warning > Select **Install**

__The server will restart. Your former local admin is now your domain admin, so the password remains the same.__

> **Note:** We use the `.lab` suffix (e.g., `discordia.lab`) to clearly indicate that this is an internal, non-public domain for lab use. Adding a suffix prevents naming conflicts with real internet domains and ensures proper DNS separation between internal and external resources.

> **Note:** The DSRM (Directory Services Restore Mode) password is **not** the same as the domain administrator's login password. It is used to log into a domain controller in **safe mode** for recovery or maintenance tasks. This account operates locally on the DC and is only needed in special scenarios like restoring Active Directory from backup. However you can use same passwords in temporary labs for convenience.

<img width="928" height="585" alt="image" src="https://github.com/user-attachments/assets/0e1bdb1c-899b-4671-b1e0-97b1948d4e3d" />  

*DC Install Summary*

##### Description

- DomainName: The fully qualified domain name (FQDN)
- DomainNetbiosName: NetBIOS name shown in legacy tools (uppercase preferred)
- InstallDNS: Installs and configures DNS on this DC
- SafeModeAdministratorPassword: Required for Directory Services Restore Mode (DSRM)
- Force: Skips confirmation prompts

---
### 5.4.1 Configure DHCP Role
##### Powershell
```powershell
# Define basic variables
$ScopeName     = "Internal"
$ScopeID       = "192.168.56.0"
$StartRange    = "192.168.56.100"
$EndRange      = "192.168.56.200"
$SubnetMask    = "255.255.255.0"
$DNSServer     = "192.168.56.1"
$DomainName    = "discordia.lab"

# Add the scope
Add-DhcpServerv4Scope -Name $ScopeName -StartRange $StartRange -EndRange $EndRange -SubnetMask $SubnetMask

# Set DNS options
Set-DhcpServerv4OptionValue -ScopeId $ScopeID -DnsServer $DNSServer -DnsDomain $DomainName

# Activate the scope
Set-DhcpServerv4Scope -ScopeId $ScopeID -State Active
```
##### GUI
Because we're using a LAN segment (which has no built-in DHCP), we must install the DHCP role to automatically assign IP addresses and network settings to all VMs in the isolated subnet; DHCP (Dynamic Host Configuration Protocol) simplifies this by dynamically providing each machine with an IP address, subnet mask, gateway, and DNS configuration.

`Server Manager > Flag > Select "Complete DHCP Configuration" > Next > Leave Credentials as is > Commit`
`Server Manager > Tools > DHCP > Extend DC-Server Name > Right-Click IPv4 > Select New Scope > Next > Provide a Name for the IP-Range: Internal > Enter start address: 192.168.56.100 > Enter End Address: 192.168.56.200 > Next > Leave Exclusions unchanged > next > Leave Lease unchanged > next > Check I want to configure DHCP Options > Router (Leave Blank) > Next > Leave DNS Unchanged > Next > Leave WINS unchanged > Leave activate scope checked > finish.` 

<img width="557" height="166" alt="image" src="https://github.com/user-attachments/assets/6e93df3d-22c4-44fc-8589-fd04eb92d270" />

---

### 5.5 Create User Accounts for Testing
This step sets up the test user accounts you’ll need in your lab. One account will be a **non-privileged domain user**, and the other will be a **local admin account** for initial setup on the Windows 11 workstation.

#### Step 1: Create a Domain Admin Account (`r.deschain`)
On `DC01`, open PowerShell as Administrator:
```powershell
# Create User
New-ADUser -Name "Roland Deschain" `
    -SamAccountName "r.deschain" `
    -UserPrincipalName "r.deschain@discordia.local" `
    -AccountPassword (Read-Host -AsSecureString "Set password for r.deschain") `
    -Enabled $true

# Adding to "Domain Admins" Group
Add-ADGroupMember -Identity "Domain Admins" -Members "r.deschain"
```

#### Step 2: Create a Domain User (`jchambers`)

On `DC01`, open PowerShell as Administrator:
```powershell
New-ADUser -Name "jchambers" `
    -SamAccountName "jchambers" `
    -UserPrincipalName "jchambers@discordia.local" `
    -AccountPassword (Read-Host -AsSecureString "Set password for jchambers") `
    -Enabled $true
```

## 6. Windows 11 Client Setup (WIN11-CL01)
### 6.1 Install Windows 11

In this step, you'll install Windows 11 Pro on the virtual machine `WIN11-CL01` and `WIN11-CL02`. 
This machines will later be joined to the `discordia.local` domain and used to simulate user behavior or attack targets.

#### Installation Overview

1. Create a new virtual machine in **VMware Workstation**
2. Attach the **Windows 11 ISO** (see [Resources](#2-resources)) as the boot disk
3. Boot the VM and start the installer
4. Choose:
   - **Edition:** Windows 11 Pro
   - **Custom install** (not upgrade)
   - Create a new partition on the virtual disk
5. Follow the OOBE prompts:
   - Region, keyboard layout, etc.
   - Skip or disable Microsoft account login (choose "Offline Account")
   - Name the device: `WIN11-CL01`
   - Set a temporary local admin account (e.g., `localadmin`)

6. Once the setup is complete:
   - Log in with the local admin account
   - Install VMware Tools for proper drivers and integration

> Tip: Do not join the domain yet — this will be handled in the next steps.


#### Video Guide (Optional)

If you're new to Windows 11 installation or want to follow along visually, this guide walks through the full process:

[Windows 11 Pro Installation Guide (YouTube)](https://youtu.be/EMuw_IN-UOU?si=4v9BcCVfuMc7S7Uo)

### 6.2 Configure Network and Hostname
### 6.3 Join the Domain (`discordia.local`)
### 6.4 Log In with Test User

---

## 7. Kali Linux Setup (KALI01)

### 7.1 Install Kali Linux
### 7.2 Configure Hostname and Network
### 7.3 Update and Install Tools

## 8. Credits and External Resources

- [Windows Server 2025 Installation Video](https://youtu.be/fbfSKgV2in8?si=7LoS7RCeenfELb87) by Danny Moran
- [Windows 11 Installation Guide](https://youtu.be/EMuw_IN-UOU?si=4v9BcCVfuMc7S7Uo) by John Hammond

