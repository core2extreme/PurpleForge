# PurpleForge (English)

**PurpleForge** is a long-term, hands-on repository for Purple Teaming — blending offensive techniques (Red Team), defensive detection (Blue Team), and practical integration (Purple Team) into a single, reusable reference platform.

This repository documents techniques like credential theft, lateral movement, misconfigurations, and detection bypasses in a way that's accessible to both red and blue teams — with context from both sides.

### Index File
[Index File | Table of Contents](EN/README.md)

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
- [EN/Index.md](EN/README.md) — English Table of Contents  
- [JP/Index.md](JP/README.md) — Japanese Table of Contents

## Language Support

This repository supports both English and Japanese:
- English documentation is located under `/EN/`
- Japanese documentation is located under `/JP/`

## Topics Covered

- End-to-end attack simulations (e.g. phishing to ransomware)
- Credential access and lateral movement techniques
- Blue team detection strategies (Sysmon, Sigma, KQL)
- Threat intelligence enrichment and correlation
- Security automation using SOAR and playbooks
- Incident response and containment workflows
- Integration of LLMs for decision support and tuning
- Active Directory lab infrastructure and misconfigurations

## Goal

This project aims to:
- Demonstrate practical purple teaming workflows
- Help analysts understand attacker behavior through hands-on replication
- Provide detection and hardening guidance tied to real scenarios
- Serve as a reference lab for SOC operations, IR training, and tooling validation
- Explore automation and AI-assisted decision-making in incident response

## Disclaimer & Legal

These guides are for **educational and authorized use only**.  
Use of offensive tools should only occur in isolated labs or approved assessments.  
I take **no responsibility** for any damage, misuse, or legal violations resulting from use of this content.
