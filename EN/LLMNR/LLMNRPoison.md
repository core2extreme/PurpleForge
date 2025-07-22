# LLMNR Poisoning in Active Directory Environments

## 1.  Introduction
### 1.1 What is LLMNR?
[LLMNR (Link-Local Multicast Name Resolution)](https://www.rfc-editor.org/rfc/rfc4795) is a protocol used by Windows operating systems to resolve hostnames to IP addresses when DNS fails. It operates over UDP port 5355 and functions similarly to DNS, but is limited to the local network segment.

LLMNR is primarily designed for small networks or ad hoc environments without a fully functional DNS infrastructure. When a system issues a DNS query that receives no response, it will send a multicast LLMNR query to the local network asking if any host knows the address of the requested name.

Key characteristics of LLMNR:

- Operates at the link-local level (multicast to 224.0.0.252)
- Enabled by default on Windows systems prior to Windows 11 and Windows Server 2022
- Uses no authentication or validation of responses

While LLMNR can provide fallback name resolution in poorly configured environments, its lack of security controls makes it vulnerable to spoofing and poisoning attacks. Because of this, it is considered a legacy feature and is recommended to be disabled in enterprise environments.

### 1.2 What is LLMNR Poisoning?

LLMNR poisoning, also referred to as **LLMNR spoofing** or a **Responder attack**, is a technique used by attackers to intercept and manipulate local name resolution traffic on a network. It exploits the design of the LLMNR (Link-Local Multicast Name Resolution) protocol, which lacks authentication and allows any host on the local subnet to respond to name resolution requests.

When a Windows system attempts to resolve a hostname that fails via DNS, it falls back to LLMNR. An attacker on the same broadcast domain can listen for these LLMNR queries and respond with a spoofed reply, claiming to be the requested host. This leads the victim machine to attempt authentication with the attacker, typically over SMB, and in doing so, leak **NTLMv2 authentication hashes**.

These captured hashes can then be:
- Cracked offline using password recovery tools such as `hashcat`
- Relayed to other services in real time if SMB signing is not enforced
- Stored and analyzed for lateral movement or privilege escalation

The most commonly used tool for this type of attack is **Responder**, which automates spoofing of LLMNR, NBNS, and other legacy protocols to capture credentials.

LLMNR poisoning is highly effective in flat networks and environments where legacy protocols are still enabled by default. It is widely seen in internal penetration tests, red team engagements, and real-world post-compromise scenarios.

This technique is classified under [MITRE ATT&CK T1557.001](https://attack.mitre.org/techniques/T1557/001/): *Adversary-in-the-Middle: LLMNR/NBT-NS Poisoning and SMB Relay*.

### 1.3 Why Is It a Threat?
LLMNR poisoning poses a significant risk because it allows attackers to capture or relay authentication attempts with minimal effort and no special privileges. It exploits a built-in, unauthenticated fallback protocol that is enabled by default on many Windows systems, especially in legacy or unsegmented environments.

The attack itself is low-noise and typically does not generate obvious security alerts unless specific monitoring is in place. This makes it a reliable method for stealthy credential access in real-world scenarios.

### Key Risks

- **Credential Exposure**  
  NTLMv2 challenge-response hashes can be captured and cracked offline or relayed to other services if signing is not enforced.

- **Bypasses Privilege Barriers**  
  The attacker does not need administrative rights to perform LLMNR spoofing. Any user on the same subnet can impersonate a valid responder.

- **Enables Unauthenticated Attacks**  
  Since LLMNR/NBNS do not validate responders, attackers can impersonate any hostname requested by the victim system.

- **Affects Trust Boundaries**  
  The technique undermines internal trust models by exploiting name resolution fallback mechanisms, which are rarely hardened or audited.

LLMNR poisoning remains an effective entry point or lateral movement enabler in modern environments where legacy protocols have not been fully disabled. Without proper network segmentation, endpoint hardening, and protocol hygiene, it can silently lead to compromise of sensitive accounts or systems.

### 1.4 Attack Context (Insider Threat, Lateral Movement, etc.)
LLMNR poisoning is most relevant in **post-compromise** or **insider threat scenarios**, where the attacker already has a presence on the internal network. It's not a remote exploitation technique — it requires local network access, but once that is achieved, it can act as a gateway to more serious compromise.

### Common Threat Contexts

#### Insider Threats
Disgruntled employees or contractors with basic access can use tools like Responder to harvest credentials from colleagues or IT systems. Since no elevated permissions are required to initiate the attack, it is highly accessible even to non-technical insiders.

#### Compromised Endpoints (Initial Foothold)
After a phishing attack, malware infection, or USB drop, a compromised user machine may be used to perform LLMNR poisoning. It provides attackers a stealthy way to escalate their access without triggering traditional perimeter defenses.

#### Lateral Movement
LLMNR poisoning is frequently used to collect hashes for lateral movement. Once credentials are captured, the attacker can move laterally using SMB, RDP, or WMI — especially if admin or service accounts are exposed.

#### Red Team and Penetration Testing
Professional red teams and internal security testers often use LLMNR poisoning early in their engagements to demonstrate weaknesses in name resolution, credential hygiene, and monitoring gaps.

#### Unsegmented or Legacy Networks
Environments with flat network topologies or legacy systems often leave LLMNR and NBNS enabled by default. These are ideal conditions for poisoning attacks, especially in small-to-medium enterprise networks without strict configuration baselines.

LLMNR poisoning is not just a standalone technique — it is a **launchpad** for further credential abuse, lateral movement, and in many cases, domain-wide compromise.
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

