Hydra - RDP Brute Force
Password List
I used a short list on purpose. Enough to generate noise, not so long
that the lab took forever.
password
123456
admin
qwerty
letmein
welcome
Passw0rd123!
Command
hydra -l soclab -P passwords.txt rdp://192.168.106.129 -t 4 -V
Flags
Flag	Meaning
-l soclab	One username to attack
-P passwords.txt	List of passwords to try
rdp://	Target protocol
-t 4	4 parallel connections
-V	Show each attempt as it happens
Result
Hydra fired 19 failed logins in about 10 seconds.
Every one logged on Windows as Event ID 4625, Logon Type 3.
No successful login. NLA blocked the 
