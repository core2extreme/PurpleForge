# PurpleForge (English)

**PurpleForge** is a long-term, hands-on repository for Purple Teaming — blending offensive techniques (Red Team), defensive detection (Blue Team), and practical integration (Purple Team) into a single, reusable reference platform.

This repository documents techniques like credential theft, lateral movement, misconfigurations, and detection bypasses in a way that's accessible to both red and blue teams — with context from both sides.

### Index File
[Index File | Table of Contents](https://github.com/Spectralsec/PurpleForge/blob/main/EN/Index.md)

## Structure

Each attack or technique is stored in its own folder and includes:
- A single Markdown file (`*.md`) with:
  - Description of the technique
  - Red team execution steps
  - Blue team detection methods
  - Purple team insights
  - Hardening and mitigation guidance
- A `/screenshots/` subfolder for visual aids (if applicable)

Central indexes:
- [EN/Index.md](./EN/Index.md) — English Table of Contents  
- [JP/目次.md](./JP/目次.md) — Japanese Table of Contents

## Language Support

This repository supports both English and Japanese:
- English documentation is located under `/EN/`
- Japanese documentation is located under `/JP/`

## Topics Covered

- LLMNR Poisoning  
- NTLM Relay  
- Pass-the-Hash  
- Detection Engineering (KQL, Sysmon, Sigma)  
- Hardening Techniques (GPOs, firewall, audit policy)  
- Active Directory Lab Environments

## Goal

This project aims to:
- Provide reusable, realistic purple team examples  
- Help defenders understand attacker behavior  
- Demonstrate how to detect and stop attacks using built-in tools  
- Support SOC training, threat hunting, and red/blue collaboration

## Disclaimer & Legal

These guides are for **educational and authorized use only**.  
Use of offensive tools should only occur in isolated labs or approved assessments.  
I take **no responsibility** for any damage, misuse, or legal violations resulting from use of this content.
