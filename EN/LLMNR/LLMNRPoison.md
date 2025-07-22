# LLMNR Poisoning in Active Directory Environments

## 1.  Introduction
### 1.1 What is LLMNR?
[LLMNR (Link-Local Multicast Name Resolution)](https://www.ip-insider.de/was-ist-llmnr-link-local-multicast-name-resolution-a-66773309f16efc869538fc522f319c4f/) is a protocol used by Windows operating systems to resolve hostnames to IP addresses when DNS fails. It operates over UDP port 5355 and functions similarly to DNS, but is limited to the local network segment.

LLMNR is primarily designed for small networks or ad hoc environments without a fully functional DNS infrastructure. When a system issues a DNS query that receives no response, it will send a multicast LLMNR query to the local network asking if any host knows the address of the requested name.

Key characteristics of LLMNR:

- Operates at the link-local level (multicast to 224.0.0.252)
- Enabled by default on Windows systems prior to Windows 11 and Windows Server 2022
- Uses no authentication or validation of responses

While LLMNR can provide fallback name resolution in poorly configured environments, its lack of security controls makes it vulnerable to spoofing and poisoning attacks. Because of this, it is considered a legacy feature and is recommended to be disabled in enterprise environments.

### 1.2 What is LLMNR Poisoning?
### 1.3 Why Is It a Threat?
### 1.4 Attack Context (Insider Threat, Lateral Movement, etc.)

## 2.  Lab Setup
### 2.1 Lab Topology
### 2.2 Virtual Machines
### 2.3 Network Configuration
### 2.4 Tools Used

## 3.  Performing the Attack
### 3.1 Starting Responder on Kali
### 3.2 Triggering LLMNR Requests from Windows Client
### 3.3 Capturing NTLMv2 Hashes
### 3.4 (Optional) Cracking Hashes or Relay Scenarios

## 4.  Detection
### 4.1 Using Microsoft Defender or Sentinel
### 4.2 Example KQL Queries
### 4.3 Event IDs to Look For
### 4.4 Behavioral Indicators

## 5.  Hardening Measures
### 5.1 Disable LLMNR via GPO
### 5.2 Disable NetBIOS
### 5.3 Enable SMB Signing
### 5.4 Firewall Rules
### 5.5 Intune / Registry / Scripts

## 6.  Pros and Cons of Mitigations
### 6.1 Compatibility Considerations
### 6.2 Pros and Cons Table

## 7.  References and Further Reading

## 8.  Conclusion
### 8.1 Summary of Risk
### 8.2 Actionable Recommendations

