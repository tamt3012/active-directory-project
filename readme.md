# Lab Overview
This project provides hands-on experience in building an Active Directory lab environment, configuring centralized log collection, and practicing detection engineering with Splunk.

## Project Goal
The goal of this lab was to build a small enterprise-style environment with Active Directory, onboard Windows systems into Splunk, deploy Sysmon for detailed telemetry, and simulate attack activity using Atomic Red Team.

# Lab Components

## Active Directory Domain Controller
A Windows Server system used to manage the `tamt.local` domain environment, including user and account administration.

## Splunk Server
An Ubuntu-based Splunk Enterprise server used for log ingestion, searching, and telemetry analysis.

## Target Machine
A Windows 10 endpoint used to simulate normal user activity and attack telemetry generation.

## Attacker Machine
A Kali Linux instance used for security testing and attack simulation within the lab.

# Network Configuration
The lab used a dual-adapter configuration:
- NAT adapter for internet connectivity
- Host-only adapter for internal lab communication

This allowed the virtual machines to stay connected within the private lab while still maintaining internet access for downloads and setup.

# Tooling & Telemetry

## Sysmon
Deployed on the Active Directory server and Windows 10 target machine to provide granular process, network, and system telemetry.

## Splunk Universal Forwarder
Installed on Windows endpoints to securely forward telemetry and event logs to the Splunk server.

## Atomic Red Team
Used on the target machine to simulate attacker behavior and generate real-world telemetry for detection testing.

# Results
This lab successfully demonstrated:
- Active Directory domain setup
- Splunk deployment and connectivity
- Sysmon telemetry collection
- Universal Forwarder log shipping
- Attack simulation with Atomic Red Team
- Detection visibility through Splunk searches

# Lessons Learned
This lab challanged me in so many ways that were unexpected. I thought it would mainly be in configuring Active Directory and running attacks. But the most valuable experience came from troubleshooting the issues in between.

**Networking** I underestimated how much time I would spend on connectivity before any security work could begin. Diagnosing why one VM could not be reached while others communicated fine forced me to look for connectivities such as adapters, firewall rules, and serivces, and each of these can mess with one another if not configured correctly.

**Knowing your tools** Crowbar kept on failing throughout this lab due to compatibility issues with the environment. Rather than continuing to retry the same approach, I searched the whole internet and finally diagnosed the failure. I switched to Hydra, and it finally worked. I felt like these processes of elimination were more educational than a perfect successful first attemp.

**Windows Security Defaults** Getting RDP to work required enabling it at the registry level, starting the service, opening the firewall, and disabling NLA. This honestly got me in awe of how defense-in-depth can be so meticuluous-all these small, little configurations just to make the end device safer.

