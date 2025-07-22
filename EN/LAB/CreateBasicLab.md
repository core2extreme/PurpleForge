# Create a Basic Active Directory Lab

This guide walks you through creating a minimal lab environment for Windows domain attack and defense simulations. It includes one domain controller, one Windows 11 workstation, and one Kali Linux machine — all within the same subnet.


## 1. Lab Overview

### 1.1 Lab Objectives
- Simulate real-world domain environments
- Demonstrate credential theft and detection
- Serve as foundation for blue and red team exercises

### 1.2 Lab Topology
Coming Soon

## 2. Resources

### Operating System ISOs
- Windows Server 2025: https://www.microsoft.com/en-us/evalcenter/download-windows-server
- Windows 11 Pro: https://www.microsoft.com/software-download/windows11
- Kali Linux: https://www.kali.org/get-kali/

### Microsoft Tools
- Sysinternals Suite: https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite
- RSAT Tools: https://learn.microsoft.com/en-us/windows-server/remote/remote-server-administration-tools

### Security Tools
- Responder: https://github.com/lgandx/Responder
- Wireshark: https://www.wireshark.org/
- Hashcat: https://hashcat.net/hashcat/
- Mimikatz: https://github.com/gentilkiwi/mimikatz

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

- All machines should be placed in the same internal, NAT, or host-only network.
- Use static IPs or reserved DHCP leases to ensure consistency.


## 5. Windows Server 2025 Setup (DC01)

### 5.1 Install Windows Server
### 5.2 Configure Hostname and Static IP
### 5.3 Install AD DS Role
### 5.4 Promote to Domain Controller (`discordia.local`)
### 5.5 Create User Accounts for Testing

---

## 6. Windows 11 Client Setup (WIN11-CL01)

### 6.1 Install Windows 11
### 6.2 Configure Network and Hostname
### 6.3 Join the Domain (`discordia.local`)
### 6.4 Log In with Test User

---

## 7. Kali Linux Setup (KALI01)

### 7.1 Install Kali Linux
### 7.2 Configure Hostname and Network
### 7.3 Update and Install Tools
```

