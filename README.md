# SOAR - Lazagne Detection

## Overview
This repository provides Security Orchestration, Automation, and Response (SOAR) rules to detect the usage of **LaZagne**, a credential-dumping tool.

## Features
- Detects LaZagne execution based on process creation events.
- Matches process names, command-line arguments, and hashes.
- Generates alerts when detection criteria are met.

## Detection Rules
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

## Diagram
Below is the logical flow of SOAR detection.  
![SOAR Logical Diagram](SOAR_LOGICAL_DIAGRAM.png)

## Installation & Usage
1. Clone this repository:
   ```sh
   git clone https://github.com/your-username/SOAR-Lazagne-Detection.git
   ```
2. Deploy the detection rules in your SOAR platform.
3. Monitor alerts for LaZagne activity.

## References
- [LaZagne GitHub](https://github.com/AlessandroZ/LaZagne)
