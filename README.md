# SOAR Workflow for LaZagne Detection

## **1. Introduction**
### **Overview**
This project integrates **LimaCharlie** for real-time threat detection and **Tines** for automated alert processing and response. The goal is to detect the usage of **LaZagne**, a credential-dumping tool, and automate security responses while maintaining human oversight for critical decisions.

### **Objectives**
- Detect LaZagne execution on endpoints.
- Automate alert forwarding to security teams.
- Enable SOC analysts to decide on machine isolation.
- Maintain detailed logs for auditing and security analysis.

## **2. Architecture**
The SOAR system is built around three key components:
1. **LimaCharlie** – Endpoint Detection and Response (EDR) for monitoring malicious activities.
2. **Tines** – Security automation platform handling alert processing and notifications.
3. **SOC Team** – Receives alerts, reviews incidents, and approves response actions.

### **Architecture Diagram**
![SOAR Architecture](SOAR_ARCHITECTURE.png)

## **3. Detection Workflow**
### **Threat Detection Flow**
1. **LimaCharlie Agent** continuously monitors endpoint activity.
2. A suspicious process execution is detected (e.g., `LaZagne.exe`).
3. LimaCharlie generates an **alert** and forwards it to **Tines**.

### **Automated Alert Processing**
1. **Tines receives the alert** via API integration.
2. It extracts relevant threat details (e.g., process name, command line, hash value).
3. Tines automatically forwards the alert to Slack and email notifications.

### **SOC Analyst Decision-Making**
1. The **SOC Analyst receives a notification** (via Slack and Email).
2. The analyst reviews the alert details.
3. A decision prompt is sent: **"Do you want to isolate the affected machine?"**

## **4. Implementation Details**
### **LimaCharlie Configuration**
- Deploy **LimaCharlie agents** on all endpoints.
- Set up **detection rules** to identify LaZagne.
- Integrate LimaCharlie with **Tines** via API for automated response handling.

### **Tines Automation Setup**
- Configure **Tines workflows** to process LimaCharlie alerts.
- Define playbooks for **SOC analyst decision prompts**.
- Enable **automated machine isolation** if the analyst approves.

## **5. Response Automation**
### **If Analyst Approves Isolation:**
1. Tines triggers **LimaCharlie** to isolate the compromised machine.
2. A status update is sent to all stakeholders.
3. Logs are generated for auditing.

### **If Analyst Rejects Isolation:**
1. The machine remains online.
2. The analyst marks it as a false positive or a monitored threat.
3. Actions are logged for further investigation.

## **6. Code & Configuration**
### **Detection Rules (LimaCharlie YAML)**
```yaml
events:
  - NEW_PROCESS
  - EXISTING_PROCESS
op: and
rules:
  - op: is windows
  - op: or
    rules:
    - case sensitive: false
      op: ends with
      path: event/FILE_PATH
      value: LaZagne.exe
    - case sensitive: false
      op: contains
      path: event/COMMAND_LINE
      value: LaZagne
    - case sensitive: false
      op: is
      path: event/HASH
      value: '3cc5ee93a9ba1fc57389705283b760c8bd61f35e9398bbfa3210e2becf6d4b05'

- action: report
  metadata:
    author: MyDFIR
    description: TEST - Detects Lazagne Usage
    falsepositives:
    - ToTheMoon
    level: high
    tags:
    - attack.credential_access
  name: MyDFIR - HackTool - Lazagne
```

### **Tines Workflow JSON Example**
```json
{
  "workflow": "Lazagne Detection",
  "trigger": "LimaCharlie Alert",
  "actions": [
    {
      "type": "notification",
      "method": "Slack",
      "message": "Possible LaZagne Execution Detected! Analyst Approval Needed."
    },
    {
      "type": "decision",
      "prompt": "Isolate the affected machine?",
      "options": ["Yes", "No"]
    },
    {
      "type": "action",
      "condition": "Yes",
      "method": "LimaCharlie API",
      "command": "Isolate Endpoint"
    }
  ]
}
```

## **7. Testing & Results**
### **Test Cases**
1. **Run LaZagne.exe on a test machine**.
2. Verify **LimaCharlie detects the process**.
3. Confirm **Tines receives the alert and triggers SOC notification**.
4. Test **SOC analyst response (Approve or Reject Isolation)**.
5. Validate whether **machine isolation occurs as expected**.

### **Expected Results**
| Test Case | Expected Outcome |
|-----------|-----------------|
| LaZagne Execution | Alert detected in LimaCharlie |
| Tines Alert Forwarding | Notification sent to Slack/Email |
| SOC Analyst Approves Isolation | Machine is isolated via LimaCharlie |
| SOC Analyst Rejects | Machine remains online with logs updated |

## **8. Future Enhancements**
- **Machine Learning Integration** – Improve detection accuracy for evolving threats.
- **Automated Response Playbooks** – Expand automation for different attack scenarios.
- **Multi-Platform Support** – Extend support to cloud-based and hybrid environments.
- **Enhanced SOC Dashboard** – Develop a UI for better visualization of alerts and actions.

---
### **Workflow Diagram:**
![SOAR Workflow](SOAR_WORKFLOW.png)

This workflow ensures **automated threat detection, real-time analyst interaction, and efficient incident response.** 🚀
