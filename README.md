# SOC Brute Force Detection Lab

## What This Is
I built a small SOC lab to practice detecting an RDP brute force attack.
Kali tries to break into a Windows 10 box over RDP, Windows logs the
attempts, Splunk picks them up, and I wrote the detection query and
incident report like a Tier 1 analyst would.

## Lab Setup
| Component | Details |
|-----------|---------|
| Attacker  | Kali Linux 192.168.106.128 |
| Victim    | Windows 10 192.168.106.129 |
| SIEM      | Splunk |
| Tool Used | Hydra |
| Target    | RDP on port 3389 |

## What I Did
1. Got the two VMs talking (had to turn off Windows firewall for ICMP,
   and fix an NLA issue when xfreerdp wouldn't connect — rdesktop was
   too old for it).
2. Created a test user called soclab on Windows with a weak password on
   purpose, so there would be something to brute force.
3. Ran Hydra from Kali against RDP using a short password list.
4. Watched the Windows Security log fill up with Event ID 4625.
5. Pulled the events into Splunk and wrote an SPL query to spot the
   pattern.

## What I Found
- 19 failed logins from 192.168.106.128 in about 10 seconds.
- All of them were Logon Type 3 (Network), which surprised me at
  first — I expected Type 10 because I assumed RDP would show up that
  way. Turns out Type 3 is the NLA pre-auth stage, and Type 10 only
  shows up once you actually get a session.
- No Type 10 events appeared, which means NLA did its job and blocked
  the login attempts before a session was ever created.

## Detection Query
index=* EventCode=4625
| stats count, values(Account_Name) as Targeted_Users by Source_Network_Address, Logon_Type
| where count > 5
| sort -count

Basically: group failed logins by source IP and logon type, then only
show the ones with more than 5 attempts. In a real environment you'd
tune the threshold and add a time window, but for a lab this catches
the obvious bursts.

## MITRE ATT&CK
- T1110     Brute Force
- T1021.001 Remote Services: RDP
- T1078     Valid Accounts (attempted, not achieved)

## What I'd Do in a Real Incident
- Block the source IP at the firewall.
- Reset the password on the targeted account and check it wasn't reused.
- Enable account lockout if it's not already on.
- Turn on MFA for RDP.
- Push RDP behind a VPN so it's not exposed.

## What I Learned
- NLA blocks old RDP clients. rdesktop failed, xfreerdp worked.
- Windows firewall drops ICMP by default — normal, not a problem.
- RDP brute force with NLA shows up as Logon Type 3, not Type 10.
  If you only filter for Type 10 you'll miss the attack.
- 19 attempts in 10 seconds is not a human — that's automation.
- Clean log data matters. I fumbled the password file early on and it
  made the analysis messier than it needed to be.

## Tools
Kali, Hydra, xfreerdp, Splunk, Windows Event Viewer.

## Author
Munaim, October 2026
