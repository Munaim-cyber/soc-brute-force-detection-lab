Lessons Learned
NLA broke my first RDP client
rdesktop wouldn't connect because Windows 10 wants Network Level
Authentication. Switched to xfreerdp and it worked. Lesson: pick the
right tool for the target, and read the error instead of fighting it.
Windows firewall blocks ICMP by default
Ping failed with 100% packet loss and I thought the network was
broken. It wasn't — Windows just drops ICMP unless you allow it. Fine
for a lab, and worth remembering: a silent ping doesn't always mean
the host is down.
Logon Type 3 vs Type 10
This tripped me up. I assumed RDP brute force would show as Logon
Type 10. It doesn't — it shows as Type 3 (Network) during the NLA
stage. Type 10 only appears after a full session is established. If
I'd filtered only for Type 10, I would have missed the whole attack.
Real takeaway: know how your logs represent what you're hunting for.
Speed is the giveaway
19 failures in 10 seconds. No human types that fast. When you see a
burst like that from one IP hitting one account, it's automated. That
single observation is what turns a pile of 4625s into an incident.
Clean data matters
I messed up the password file early on and ended up with duplicate
entries and stray text. It didn't break the attack, but it made the
output messier than it needed to be. In a real SOC, bad input leads
to bad logs, which leads to bad analysis. Slow down and get the input
right the first time.
NLA actually works
No Type 10 events at all. The attack never got past pre-auth. That's
a nice concrete example of a control doing exactly what it's supposed
to do. Defense in depth isn't just a buzzword — this lab proved it on
a small scale.
