# 🐧 Day 12: Linux Network Services  
## Troubleshooting Apache Port Connectivity & Firewalls

---

## 🎯 Problem Statement

Our monitoring tool has reported an issue in **Stratos Datacenter**. One of our app servers has an issue, as its Apache service is not reachable on port **6100** (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.

Use tools like `telnet`, `netstat`, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using the command:

```bash
curl http://stapp01:3000
```
Note: Please do not try to alter the existing index.html code, as it will lead to task failure.

## 🛠️ The Solution
Step 1: Diagnose the Port Conflict
```bash
sudo netstat -tunlp | grep :8088
```

Step 2: Clear the Conflict
```bash
sudo systemctl stop sendmail
```

Step 3: Configure & Start Apache (check for the line listen correct port number)
```bash
sudo vi /etc/httpd/conf/httpd.conf 
```
- Check for the line Listen <port_number>

Step 4: Check the Port Status
```bash
sudo netstat -tunlp | grep :5002 
```

Step 5: Bypass the Firewall "Trap"
```bash
sudo iptables -L -n
sudo iptables -I INPUT -p tcp --dport 8088 -j ACCEPT
```
Step 6: Start Apache Service
```bash
sudo systemctl start httpd 
```

Step 7: Configuration verification
```bash
curl -I http://stapp01:3000
```

### 💡 Why This Worked

> #### 🚪 The Bouncer Analogy
> Your server is a **Club**. **Apache** is the host waiting at **Table 8088**.
>
> * **The Conflict:** **Sendmail** was already sitting at Table 8088. We had to ask Sendmail to leave (stop the service) so Apache could sit down.
> * **The Bouncer:** The **Firewall** bouncer had a rule: *"If they aren't on the list, kick them out."* Because the "Reject" rule was at the top, the bouncer was kicking guests out before checking for Apache.
> * **The Fix:** We put **Table 8088** at the very **top** of the bouncer's list (`iptables -I`). Now, when a guest (**Jump Host**) arrives, they are recognized and allowed in immediately.


