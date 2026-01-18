# SOAR + EDR Automated Containment (LimaCharlie + Tines)

**One-liner:** Built a LimaCharlie + Tines workflow to detect **LaZagne** and automate **Slack/Email alerts** plus **approved host isolation via API**, achieving **~1 minute containment**.

**Stack:** LimaCharlie (EDR) • Tines (SOAR) • Slack/Email • Vultr • Windows Server 2022 • LaZagne  
**Use case:** Credential access / hacktool execution (LaZagne) → alert + triage context → human approval → isolate host

## What I Built (Flow)
1. **EDR Telemetry:** Deployed LimaCharlie sensor on a cloud Windows endpoint (Vultr).
2. **Detection:** Wrote a custom **LimaCharlie D&R rule** (process/path/CLI indicators) for LaZagne.
3. **SOAR Ingestion:** Sent detections to **Tines via webhook**.
4. **Alerting:** Delivered high-context alerts to **Slack + Email** (host/user/command line/link).
5. **Containment:** Added **human-in-the-loop approval** → **one-click host isolation** via LimaCharlie API.

## Outcomes (Measurable)
- **Containment:** **~1 minute** from detection → isolation  
- **Alerting:** Slack/Email notification sent automatically (no console monitoring)  
- **Signal Quality:** Detection based on **behavioral indicators** (process/path/CLI)

## Screenshots / Evidence

### Workflow Diagram
![Logical Workflow Diagram](Key-Screenshots/SOAR-EDR-Project.png)

### Detection (LimaCharlie D&R Rule)
![LimaCharlie DR Rule](Key-Screenshots/1-Detection-Engineering-The-Rule.png)

### Alerting (Slack + Email)
![Slack Alert](Key-Screenshots/2.1-Multi-Channel-Alerting-Slack-&-Email-V2.png)
![Email Alert](Key-Screenshots/2.2-Multi-Channel-Alerting-Slack-&-Email-V2.png)

### SOAR Playbook (Tines)
![Tines Storyboard](Key-Screenshots/3-The-Playbook-Tines-Workflow.png)

### Human Approval → Containment
![Tines User Prompt](Key-Screenshots/4.1-Human-in-the-Loop-Response-User-Prompt-V3.png)
![Prompt Result](Key-Screenshots/4.2-Human-in-the-Loop-Response-User-Prompt-V3.png)
![Isolation Status](Key-Screenshots/5.1-Response-Confirmation-Isolation-&-Slack-Update.png)
![Slack Confirmation](Key-Screenshots/5.2-Response-Confirmation-Isolation-&-Slack-Update.png)

## Skills Demonstrated
- **Detection Engineering** (custom D&R logic: process/path/CLI)
- **SOAR Automation** (webhook ingest, branching workflow, prompt gating)
- **EDR Response** (host isolation via API)
- **Incident Response Workflow** (alert → triage context → approve → contain)
- **API Integration** (LimaCharlie ↔ Tines ↔ Slack/Email)

## Next Improvements
- Add **VirusTotal/Hybrid Analysis enrichment** for file hash reputation in the initial alert
- Add a **false-positive guardrail** (signed binary / allowlisted admin paths)
- Auto-create a **case/ticket** on approval (TheHive/Jira) for an audit trail
- Expand response actions (kill process / collect triage artifacts / system info)

## Resources
- **Complete Process Screenshots:** [Full Screenshot Process](./Full-Screenshot-Process)
