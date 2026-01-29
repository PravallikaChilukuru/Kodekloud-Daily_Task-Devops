🐧 Day 12: Linux Network Services
Topic: Troubleshooting Apache Port Connectivity & Firewalls

🎯 Problem Statement
Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 6100 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.
Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.
Once fixed, you can test the same using command curl http://stapp01:6100 command from jump host.
Note: Please do not try to alter the existing index.html code, as it will lead to task failure.

The Apache service on App Server 1 (stapp01) is unreachable on port 8088.

Goal: Identify the blockage (Service, Port Conflict, or Firewall) and fix it.

Constraint: Do not alter index.html.

Verification: Successful curl http://stapp01:8088 from the Jump Host.

💡 Learning: The Troubleshooting Hierarchy
To solve networking issues, we move through the layers from the application down to the network security.

1. Service Layer (systemctl)
Is the program running? If the process isn't active, the port will never open.

2. Transport Layer (netstat)
Is the correct program "listening" on the correct port? Only one service can own a port at a time.

3. Network Layer (iptables)
Is the system's "Security Guard" allowing traffic through the gate?

🛠️ The Solution
Step 1: Diagnose the Port Conflict
Upon attempting to start Apache, it failed. We checked for other services occupying port 8088:

Bash

sudo netstat -tunlp | grep :8088
Finding: sendmail was listening on 8088.

Step 2: Clear the Conflict
We stopped the conflicting service to free up the port for Apache:

Bash

sudo systemctl stop sendmail
Step 3: Configure & Start Apache
Verified the configuration file /etc/httpd/conf/httpd.conf contained the correct directive:

Apache

Listen 8088
Then, started the service:

Bash

sudo systemctl start httpd
Step 4: Bypass the Firewall "Trap"
The firewall had a REJECT all rule at the bottom of the list. We inserted an ACCEPT rule at the top (Position 1) so it would be processed first:

Bash

sudo iptables -I INPUT -p tcp --dport 8088 -j ACCEPT
🔍 Detailed Explanation
🛠 The Tool Breakdown
netstat -tunlp: Our "X-ray vision" to see which Process ID (PID) owns which port.

iptables -I: The "I" stands for Insert. It places the rule at the top of the chain. If we used -A (Append), the rule would go to the bottom and be ignored because of the REJECT rule above it.

curl -I: Fetches only the HTTP headers to verify status without cluttering the terminal with HTML code.

💡 Why this worked
🚪 The Bouncer Analogy
Your server is a club. Apache is the host at Table 8088.

The Conflict: Sendmail was already sitting at Table 8088. We had to ask Sendmail to leave.

The Bouncer: The Firewall bouncer had a rule: "If they aren't on the list, kick them out."

The Fix: We put "Table 8088" at the very top of the bouncer's list. Now, when a guest (Jump Host) arrives, they are allowed in immediately.

✅ Final Verification
From the Jump Host, the following command now returns the CentOS Test Page:

Bash

curl http://stapp01:8088
