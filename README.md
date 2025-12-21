# SOAR EDR Project

### Objective
The objective of this project was to create a detection and response pipeline by integrating a **Security Orchestration, Automation, and Response (SOAR)** platform with an **Endpoint Detection and Response (EDR)** solution. The goal was to simulate a real-world SOC scenario where a specific threat (Credential Access via LaZagne) is detected on an endpoint, and the response process—including analyst notification and containment—is automated to reduce response time.

This project focused on:
* Deploying an EDR agent (**LimaCharlie**) to a cloud-based Windows endpoint.
* Generating telemetry by simulating a credential dumping attack using **LaZagne**.
* Writing a custom **Detection & Response (D&R)** rule to identify the malicious activity.
* Building an automated playbook in **Tines** to alert analysts via Slack and Email.
* Implementing a "User-in-the-Loop" mechanism to allow analysts to authorize machine isolation remotely.

---

### Skills Learned

**I. Detection Engineering & EDR Management:**
* **EDR Deployment:** Installed and configured the **LimaCharlie** EDR agent on a Windows Server instance hosted in the cloud (**Vultr**) to gain deep visibility into endpoint processes and network activity.
* **Custom Rule Creation:** Authored a granular Detection & Response (D&R) rule in LimaCharlie to detect the usage of **LaZagne** based on file paths, process names, and command-line arguments (e.g., `lasagna.exe all`).
* **Telemetry Analysis:** Analyzed process trees and event logs (Process Creation) to identify specific Indicators of Compromise (IOCs) needed for high-fidelity detection.

**II. Security Orchestration, Automation, and Response (SOAR):**
* **Playbook Design:** Designed a logical workflow in **Tines** to handle the incident lifecycle: Detection Ingestion -> Analyst Notification -> Decision Making -> Automated Response.
* **API Integration:** Configured **Webhooks** to send alerts from LimaCharlie to Tines and utilized the **LimaCharlie API** within Tines to execute containment actions (Sensor Isolation).
* **Human-in-the-Loop Decisions:** Created a dedicated **User Prompt Page** in Tines that allows a SOC analyst to approve or deny an isolation request via a simple web interface.

**III. Communication & Reporting:**
* **Automated Alerting:** Configured the SOAR playbook to parse JSON data from the alert and format a readable message sent to a dedicated **Slack** channel and **Email**.
* **Cross-Platform Integration:** Established secure connections between the EDR, SOAR platform, and communication tools (Slack) to ensure seamless data flow.

---

### Tools Used

**Security Stack:**
* **LimaCharlie:** EDR (Endpoint Detection and Response) & Control Plane.
* **Tines:** SOAR (Security Orchestration, Automation, and Response) Platform.
* **Slack:** Incident Alerting & Communication.

**Infrastructure & Environment:**
* **Vultr:** Cloud Service Provider (Windows Server Instance).
* **Windows Server 2022:** Target Endpoint.
* **LaZagne:** HackTool (Used for Password Recovery/Credential Dumping simulation).

**Utilities:**
* **Draw.io:** Workflow Diagramming.
* **SquareX:** Disposable Email for testing alerts.

---

### Network Diagram
*(I will create and add one later)*

---

### Key Results

**1. High-Fidelity Threat Detection**
Successfully created a custom D&R rule in LimaCharlie that instantly detected the execution of `lazagne.exe` with the argument `all`. This proved the ability to move beyond default signature sets and write logic for specific threats.

**2. Automated Analyst Notification**
Eliminated the need to constantly monitor the EDR console. Within seconds of the attack execution, Tines received the webhook event and sent a formatted alert to **Slack** and **Email** containing critical context:
* **Time of Event**
* **Computer Name & User**
* **Source IP**
* **Process Command Line**
* **Direct Link to EDR Console**

**3. One-Click Containment (Response)**
Implemented a seamless response workflow. Instead of manually logging into the EDR to isolate the host, the analyst receives a prompt.
* **Scenario:** Analyst receives Slack alert -> Clicks "Investigate" link -> Reviews details on Tines Prompt Page -> Selects "Yes, Isolate".
* **Outcome:** Tines automatically triggers the LimaCharlie Isolation API, severing the device's network connection to prevent lateral movement.

---

### Screenshots

#### 1. Detection Engineering (The Rule)
*Here, I configured a custom Detection & Response (D&R) rule within LimaCharlie. This rule monitors for the specific process `lazagne.exe` in the File Path or Command Line. This granular rule ensures that even if the attacker renames the file but keeps the original internal name or argument structure, the activity is still detected.*
![LimaCharlie DR Rule](1-Detection-Engineering-The-Rule.png)

#### 2. Multi-Channel Alerting (Slack & Email)
*Upon detection, the Tines storyboard broadcasts the alert to multiple channels to ensure immediate visibility. The screenshots below show the formatted Slack message and the corresponding email notification containing the context (Time, User, IP) needed for immediate triage.*
![Slack Alert](2.1-Multi-Channel-Alerting-Slack-&-Email.png)
![Email Alert](2.2-Multi-Channel-Alerting-Slack-&-Email.png)

#### 3. The Playbook (Tines Workflow)
*This is the backend logic built in Tines. It accepts the JSON alert from LimaCharlie, formats the data, sends the notifications to Slack and Email, and then creates a "User Prompt" decision block to wait for analyst authorization before taking action.*
![Tines Storyboard](3-The-Playbook-Tines-Workflow.png)

#### 4. Human-in-the-Loop Response (User Prompt)
*Instead of blindly isolating the machine (which could disrupt business operations), I implemented a decision step. The analyst receives a link to this page to review the details and simply select "Yes" to authorize the isolation.*
![Tines User Prompt](4.1-Human-in-the-Loop-Response-User-Prompt.png)

#### 5. Response Confirmation (Isolation & Slack Update)
*Once authorized, Tines triggers the LimaCharlie API to isolate the host. 
**Image A:** Confirms the sensor status has changed to "Isolated" (highlighted in red).
**Image B:** Shows the final confirmation sent back to Slack. Note the timestamps: The alert arrived at **8:55 PM** and was contained by **8:56 PM**, demonstrating a **1-minute Mean Time to Respond (MTTR)**.*
![LimaCharlie Isolation Status](5.1-Response-Confirmation-Isolation-&-Slack-Update.png)
![Slack Isolation Confirmation](5.2-Response-Confirmation-Isolation-&-Slack-Update.png)

---

### Future Improvements
To expand the capabilities of this project and mirror a mature SOC environment, the following improvements are planned:

* **False Positive Handling:** Incorporate logic in the Tines playbook to check if the executable is signed or is located in a known "admin tools" directory before alerting, reducing noise.
* **Threat Intelligence Integration:** Automatically query the file hash against **VirusTotal** or **Hybrid Analysis** within the Tines story and include the reputation score in the initial Slack alert.
* **Case Management:** Integrate a ticketing system (like **Jira** or **TheHive**) to automatically create an incident ticket when a user chooses to isolate the machine, ensuring an audit trail.
* **Broader Response Actions:** Add options to the user prompt for other actions, such as "Kill Process" or "Get System Info," rather than just "Isolate."

---

### Conclusion
This SOAR EDR project demonstrated the power of integrating detection capabilities with automated response workflows. By bridging **LimaCharlie** and **Tines**, I moved from a passive monitoring stance to an active, automated defense posture.

This project validated my ability to:
1.  **Engineer Detections:** Write custom logic to catch specific attacker tools.
2.  **Automate Operations:** Reduce the "Time to Acknowledge" and "Time to Respond" metrics significantly using SOAR.
3.  **Manage Security Infrastructure:** Configure cloud-based EDR sensors and manage their connectivity.

These skills are directly transferable to a **SOC Analyst** role, where speed, accuracy, and process efficiency are paramount.

---

### Resources and Documentation
to add..
