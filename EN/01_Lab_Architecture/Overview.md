## Lab Network Architecture

The following diagram provides an overview of the PurpleForge lab environment, including segmentation, firewalls, and role-specific zones.

![Lab Network Overview](./resources/netplan.drawio.png)  

### ⚠️ Caution: Recommended Hypervisor for Windows Hosts

> **Important:** When running this lab setup on a Windows host, it is strongly recommended to use **Microsoft Hyper-V** as the virtualization platform.  
>  
> This is due to **VLAN tagging limitations** in higher-level Type-2 hypervisors like **VMware Workstation** and **VirtualBox**, which do **not support native 802.1Q VLAN tagging** on virtual network adapters.  
>  
> Hyper-V provides **full VLAN support**, including **access and trunk modes**, making it essential for accurate zone-based network segmentation (e.g., DMZ, Quarantine, Blackhole) and **realistic enterprise-grade containment workflows**.
>
> While the usage of other hypervisors is technically possible, please note that this will **introduce significant limitations**, especially for **incident response or containment actions involving VLAN switching** on the internal firewall.  
> Such actions may not be possible or would require complex workarounds with isolated virtual switches.


## Machine Overview

### **C2-Server**
- Hosts command & control (C2) infrastructure for red team operations  
- Receives beacon traffic from compromised systems  
- Used to manage post-exploitation phases  

### **Delivery-Webserver**
- Serves malicious payloads for initial access vectors  
- Simulates phishing sites or exploit delivery  
- Publicly accessible within the adversary edge network  

### **Attackbox**
- Red team operator platform  
- Runs tools for reconnaissance, exploitation, and lateral movement  
- Central attack launch point for adversary activities  

---

### **Reverse Proxy + WAF**
- Filters and forwards incoming traffic to internal web services  
- Provides Web Application Firewall (WAF) protection  
- Enforces access control to services in the DMZ  

### **Webserver**
- Hosts public-facing applications  
- Serves web content or APIs to users  
- Receives filtered traffic from reverse proxy  

### **Database**
- Stores backend data for applications  
- Accessed only by internal services such as the webserver  
- Not directly exposed to external networks  

---

### **ELK_SIEM**
- Centralized log aggregation using Elasticsearch, Logstash, Kibana  
- Provides visibility into network and host activity  
- Used for detection, investigation, and dashboarding  

### **WAZUH_MAN**
- Manages endpoint agents for host-based intrusion detection  
- Monitors file integrity, log data, and configuration changes  
- Integrates with SIEM for alert correlation  

### **SHUFFLE_SOAR**
- Orchestrates automated responses to security incidents  
- Integrates with SIEM and CTI platforms  
- Supports playbook-driven incident handling  

### **MISP_CTI**
- Provides cyber threat intelligence feeds and IOC management  
- Enables threat correlation and enrichment  
- Shares structured threat information across the lab  

### **OPENVAS_VULNM**
- Performs vulnerability scanning on lab assets  
- Identifies misconfigurations or exploitable services  
- Assists in blue team hardening and red team targeting  

---

### **JMP_HOST**
- Secure administrative jump point  
- Used to access sensitive internal systems (e.g., DCs, SIEM)  
- Isolated from standard client networks  

---

### **DC_PRIMARY**
- Acts as the main Active Directory Domain Controller  
- Provides DNS and DHCP services for internal zones  
- Handles authentication and policy distribution  

### **DC_SECONDARY**
- Redundant Domain Controller for high availability  
- Syncs directory services with the primary DC  
- Ensures failover capability  

---

### **CLIENT_01 / CLIENT_02**
- Simulate end-user workstations in a corporate environment  
- Used for testing endpoint detection and response  
- Participate in domain authentication  

### **Insider_Attackbox**
- Represents a compromised internal client  
- Used for lateral movement, privilege escalation, or internal recon  
- Allows simulation of insider threat scenarios  
