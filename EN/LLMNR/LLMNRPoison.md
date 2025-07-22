# 🔥 LLMNR Poisoning in Active Directory Environments

## 1. 🧠 Introduction
### 1.1 What is LLMNR?
### 1.2 What is LLMNR Poisoning?
### 1.3 Why Is It a Threat?
### 1.4 Attack Context (Insider Threat, Lateral Movement, etc.)

## 2. 🧪 Lab Setup
### 2.1 Lab Topology
### 2.2 Virtual Machines
### 2.3 Network Configuration
### 2.4 Tools Used

## 3. 💣 Performing the Attack
### 3.1 Starting Responder on Kali
### 3.2 Triggering LLMNR Requests from Windows Client
### 3.3 Capturing NTLMv2 Hashes
### 3.4 (Optional) Cracking Hashes or Relay Scenarios

## 4. 🕵️‍♂️ Detection
### 4.1 Using Microsoft Defender or Sentinel
### 4.2 Example KQL Queries
### 4.3 Event IDs to Look For
### 4.4 Behavioral Indicators

## 5. 🛡️ Hardening Measures
### 5.1 Disable LLMNR via GPO
### 5.2 Disable NetBIOS
### 5.3 Enable SMB Signing
### 5.4 Firewall Rules
### 5.5 Intune / Registry / Scripts

## 6. ⚖️ Pros and Cons of Mitigations
### 6.1 Compatibility Considerations
### 6.2 Pros and Cons Table

## 7. 📚 References and Further Reading

## 8. ✅ Conclusion
### 8.1 Summary of Risk
### 8.2 Actionable Recommendations

