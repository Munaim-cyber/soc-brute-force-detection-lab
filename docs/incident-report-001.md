Incident Report 001 - RDP Brute Force Against DESKTOP-PT51AEU
Analyst: Munaim
Date: 2026-10-13
Status: Closed (lab exercise)
Severity: High
Summary
At around 02:54 on 2026-10-13, a burst of failed RDP logins hit the
Windows 10 lab host DESKTOP-PT51AEU. All of them came from a single
source, 192.168.106.128 (the Kali VM), and they all targeted the
account soclab. The attempts were fast and sequential, which told me
it was a script, not a person. Nothing succeeded, because NLA stopped
the session before it ever got past pre-authentication.
Timeline
Time (local)	What Happened
02:54:27	First failed login
02:54:27-37	19 failures total
02:54:37	Last failed login
--	No successful login logged
Indicators
Source IP:   192.168.106.128
Target user: soclab
Target port: 3389
Event ID:    4625
Logon Type:  3
Tool (probable): Hydra
What I Looked At
Windows Security log on the victim (4625, 4624).
Splunk search filtered by EventCode and Logon_Type.
Source IP and account fields to see if it was one attacker or many.
Timing between events to confirm it was automated.
Analysis
19 failures in 10 seconds. A person can't type that fast — it's a
tool.
Single source IP, single target account. Focused, not spray-and-pray.
Logon Type 3 = NLA pre-auth. Not Type 10, which means the full RDP
session was never established.
No 4624 Type 10 events anywhere in the window. NLA did its job.
Verdict
True positive. Automated RDP brute force attempt against a valid
account. Attack failed because NLA blocked it before authentication
completed. In a production environment this would still be treated
as high severity and investigated, because a successful login would
mean full desktop access to the target.
Recommended Actions
Block 192.168.106.128 at the perimeter.
Reset the soclab password and verify it wasn't reused elsewhere.
Confirm account lockout policy is enabled and tuned.
Require MFA for RDP.
Put RDP behind a VPN and stop exposing 3389 to the network.
Evidence
Splunk stats table (evidence/screenshots/01-stats-table.png)
Event list showing 4625 burst (evidence/screenshots/02-4625-events.png)
Hydra output from Kali (evidence/screenshots/03-hydra-output.png)
