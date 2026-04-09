## Troubleshooting & Challenges

This section documents the real issues encountered during the lab build and how they were resolved.

### 1. RDP Brute Force Returning No Results (Crowbar)
**Issue:** Crowbar was stopping within 1-2 seconds with "No results found" despite the target machine being online.

**Root Cause:** Multiple layered issues:
- The Windows 10 target was on DHCP with an unknown IP, causing connection attempts to fail silently
- Windows Firewall was blocking all inbound traffic including ICMP and port 3389
- The RDP service (TermService) was not enabled by default
- Network Level Authentication (NLA) was interfering with Crowbar's connection attempts

**Resolution:**
- Assigned a static IP (192.168.10.100) to the host-only adapter on the Windows 10 VM
- Disabled Windows Firewall for lab purposes (`netsh advfirewall set allprofiles state off`)
- Enabled RDP via registry (`fDenyTSConnections = 0`) and restarted TermService
- Disabled NLA via registry (`UserAuthentication = 0`)
- Switched from Crowbar to Hydra, which handled the RDP brute force more reliably

---

### 2. Target Machine Not Reachable From Other VMs
**Issue:** All VMs could ping each other except the Windows 10 target — no VM could ping 192.168.10.100, but the target itself could reach other VMs outbound.

**Root Cause:** Windows Firewall was blocking all inbound traffic by default, including ICMP echo requests.

**Resolution:** Enabled inbound ICMP and RDP rules through the firewall, then disabled the firewall entirely for lab use.

---

### 3. Port 3389 Showing as Filtered Then Closed
**Issue:** Even after opening the firewall rule for RDP, nmap continued showing port 3389 as `filtered`, then `closed` after disabling the firewall entirely.

**Root Cause:** The RDP service was not actually running. The firewall rule was correct, but nothing was listening on the port.

**Resolution:** Confirmed via `netstat -an | findstr 3389` (note: initially mistyped as 3398). Restarted TermService after applying the registry changes, which brought 3389 to a LISTENING state.

---

### 4. tsmith User Not Found Locally
**Issue:** Crowbar and initial user lookups returned "user not found" for tsmith on the Windows 10 machine.

**Root Cause:** tsmith was a domain user in Active Directory (tamt.local), not a local Windows account. Crowbar without domain context could not authenticate against a domain user.

**Resolution:** Confirmed domain membership via `systeminfo`, added the `-d tamt.local` flag to Crowbar, and verified tsmith's credentials using `runas /user:tamt\tsmith cmd`. Also confirmed tsmith was already a member of the Remote Desktop Users group.

---

### 5. Crowbar Failing Despite Correct Setup
**Issue:** Even with port 3389 open, firewall disabled, RDP enabled, and correct credentials in the password list, Crowbar continued to fail instantly.

**Root Cause:** Crowbar v0.4.2 has known compatibility issues with modern RDP/NLA configurations and does not iterate through password lists reliably in some environments.

**Resolution:** Switched to Hydra with the correct syntax:
```bash
hydra -l tsmith -P passwords.txt rdp://192.168.10.100
```
Hydra successfully iterated through the password list and identified `Tam30122003` as the valid credential.

---

### 6. Limited Splunk Telemetry for Brute Force Events
**Issue:** Expected to see many 4625 (failed logon) events in Splunk but only found 3 total events.

**Root Cause:** Domain account authentication is processed at the Domain Controller level, not the local endpoint. Logs were generated on ADDC10, not the Windows 10 target. Additionally, Hydra batches connection attempts rather than sending one per password, reducing the event count.

**Resolution:** Searched against the DC source in Splunk (`host=ADDC10`) instead of the endpoint index. Found EventCode 4776 (credential validation) and EventCode 4624 (successful logon) events confirming the attack was captured by the DC's security logs.
