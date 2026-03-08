# SOC Automation — Shuffle SOAR + TheHive

## Project Overview
Automated SOC alert triage pipeline built with Shuffle SOAR and TheHive.
When Splunk detects a critical firewall threat it automatically fires a 
webhook to Shuffle which processes the alert and creates a case in TheHive 
for analyst investigation. Zero manual triage required.

## Automation Pipeline
```
Splunk detects critical firewall event
            ↓
Splunk fires real-time webhook alert
            ↓
Shuffle SOAR receives and processes alert
            ↓
TheHive case created automatically
            ↓
SOC analyst investigates
```

## Tools Used
| Tool | Purpose |
|---|---|
| Splunk Enterprise | SIEM — detects and triggers alerts |
| Shuffle SOAR | Automation and orchestration platform |
| TheHive | Security case management |
| Docker | Container deployment for Shuffle |
| PowerShell | Webhook testing and simulation |

## Key Features
- Real-time alert processing — not scheduled, fires instantly
- Per-result triggering — every critical event creates a case
- Webhook integration between Splunk and Shuffle SOAR
- Automated TheHive case creation with full threat details
- Tested with simulated C2 beacon and brute force attack data

## Alert Data Passed Through Pipeline
| Field | Example Value | Description |
|---|---|---|
| src_ip | 192.168.1.200 | Source IP of attacker |
| dst_ip | 203.0.113.100 | Destination IP |
| dst_port | 4444 | Destination port (C2 beacon) |
| severity | critical | Alert severity level |
| rule_name | Block_Suspicious_Port | Firewall rule triggered |
| country | CN | Country of origin |
| action | blocked | Firewall action taken |

## Splunk Alert Configuration
- Alert Name: Critical Firewall Alert
- Alert Type: Realtime
- Trigger Condition: Number of results greater than 0
- Trigger: Per-Result
- Action: Webhook → Shuffle SOAR

## Splunk Detection Query
```
source="firewall_logs.csv" host="DESKTOP-DDM3ULB" 
index="main" sourcetype="csv" severity=critical
| table _time, src_ip, dst_ip, dst_port, rule_name, country
```

## Webhook Test Command
```powershell
$body = '{"src_ip":"192.168.1.200","dst_ip":"203.0.113.100",
"dst_port":"4444","severity":"critical",
"rule_name":"Block_Suspicious_Port","country":"CN","action":"blocked"}'

$url = "http://YOUR_IP:3001/api/v1/hooks/YOUR_WEBHOOK_ID"

Invoke-RestMethod -Uri $url -Method POST 
-ContentType "application/json" -Body $body
```

## MITRE ATT&CK Coverage
| Detection | MITRE Technique |
|---|---|
| C2 beacon on port 4444 | T1071 — Application Layer Protocol |
| Brute force RDP/SSH | T1110 — Brute Force |
| Database port scanning | T1046 — Network Service Scanning |
| IRC botnet port 6667 | T1071 — Application Layer Protocol |

## How to Reproduce This Project
1. Install Splunk Enterprise (free) from splunk.com
2. Install Docker Desktop from docker.com
3. Clone and start Shuffle:
```
git clone https://github.com/Shuffle/Shuffle
cd Shuffle
docker-compose up -d
```
4. Open Shuffle at http://localhost:3001
5. Create a new workflow with a Webhook trigger
6. Add TheHive node and connect it to the webhook
7. Copy the webhook URL from the trigger node
8. In Splunk, create a Real-time Alert with a webhook action
9. Paste the Shuffle webhook URL into the Splunk alert
10. Test using the PowerShell command above

## Project Screenshots
| Screenshot | Description |
|---|---|
| shuffle_workflow.png | Webhook 1 connected to TheHive 1 in Shuffle canvas |
| webhook_trigger.png | Active webhook showing Stop webhook button |
| splunk_alert.png | Splunk Critical Firewall Alert with webhook action |
| webhook_test.png | PowerShell sending simulated attack data to Shuffle |

## Related Projects
- [Splunk Firewall Log Analysis]
  — The SIEM project that feeds alerts into this automation pipeline

## Skills Demonstrated
- SOAR platform configuration (Shuffle)
- Webhook integration between security tools
- Splunk real-time alerting
- Docker container deployment
- Security automation and orchestration
- Incident response workflow design
