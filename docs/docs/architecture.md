Lab Architecture
How the Pieces Fit Together
Kali Linux (192.168.106.128)
|
| RDP to port 3389
v
Windows 10 (192.168.106.129)
|
| Windows Security log written
v
Splunk (indexing and search)
Machines
Host	Role	IP
Kali	Attacker	192.168.106.128
Windows 10	Victim	192.168.106.129
Splunk (on Windows)	SIEM	local
Attack Path
Kali opens an RDP connection to the Windows host.
Hydra tries password after password from a short list.
Each wrong password writes Event ID 4625 to the Windows Security log.
Splunk ingests the Security log.
I search Splunk for the pattern and write it up.
Detection Path
Splunk receives the log events.
I filter for EventCode=4625.
I group by source IP, account, and logon type.
Anything above the threshold gets looked at.
Confirmed a real brute force, wrote the incident report.
Notes
NLA is on, so brute force hits the pre-auth stage (Type 3) first.
Full RDP sessions only show up as Type 10 after successful login.
Windows firewall drops ICMP by default, so I had to allow ping to
verify connectivity before starting. That's normal, not a bug.
