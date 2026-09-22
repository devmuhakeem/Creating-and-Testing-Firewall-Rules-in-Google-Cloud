# Creating-and-Testing-Firewall-Rules-in-Google-Cloud

# Creating and Testing Firewall Rules in Google Cloud

A hands-on Google Cloud lab where I locked down a web server's network access using firewall rules, then used Logs Explorer to prove the rules actually worked.

## Scenario
A fictional bank had a demo web server running on a VPC network. The security team wanted its inbound traffic analyzed and unnecessary ports blocked. My job was to build the firewall rules, generate real traffic against the server, and verify everything in the logs.

## What I did

### 1. Created a firewall rule to allow HTTP and SSH
Built a rule called `allow-http-ssh`, scoped with a target tag (`http-server`) so it only applied to the web server VM, not the whole network. Opened TCP ports 80 and 22, with logging turned on.

### 2. Generated real traffic and analyzed the logs
Hit the web server's external IP directly to generate HTTP traffic, then went into Logs Explorer and filtered VPC Flow Logs by my own source IP. Inspected the connection details — destination IP, port, protocol — and confirmed the traffic was being allowed exactly because of the rule I'd created.

### 3. Created a rule to deny HTTP traffic
Added a second rule, `deny-http`, targeting the same tag but denying TCP port 80 instead of allowing it.

### 4. Tested the deny rule and verified it in the logs
Tried loading the web server again — got a connection error, as expected. Then pulled up the firewall logs, filtered for my IP with a DENIED disposition, and confirmed the log entry showed the deny-http rule firing: direction INGRESS, action DENY, port 80, matched against the http-server target tag.

## Key takeaways
- Target tags are what make firewall rules manageable at scale — they let you scope a rule to exactly the VMs it should apply to instead of the whole VPC
- "It works" isn't enough — the log entry showing disposition: DENIED with the matching rule name is the actual proof a rule is doing what you think it's doing
- Testing a deny rule by trying to break your own access is a simple but effective way to validate a firewall change before trusting it in production

## Tools
Google Cloud VPC Firewall, Compute Engine, Logs Explorer, VPC Flow Logs

---
*Completed as a Google Cloud Skills Boost lab.*
