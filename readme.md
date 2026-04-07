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
This project improved my understanding of Active Directory administration, endpoint telemetry, Splunk ingestion, and the networking required to support a multi-VM security lab.
