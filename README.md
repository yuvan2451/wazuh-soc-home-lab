\# Wazuh SOC Home Lab



A self-directed cybersecurity home lab built to develop practical SOC analyst and detection engineering skills using Wazuh.



The lab focuses on SIEM deployment, endpoint monitoring, attack simulation, custom detection rules, MITRE ATT\&CK mapping, alert analysis, and incident-response workflows.



\## Architecture



!\[Wazuh SOC Home Lab Architecture](wazuh\_lab\_architecture.png)



\### Lab Components



| VM | Role | IP Address | Key Components |

|---|---|---|---|

| wazuh-manager | SIEM | `192.168.56.10` | Wazuh Manager, Indexer, Dashboard |

| windows-endpoint | Monitored Endpoint | `192.168.56.20` | Windows 11, Sysmon |

| ubuntu-endpoint | Monitored Endpoint | `192.168.56.30` | Ubuntu Desktop, OpenSSH, auditd |

| kali-attacker | Attack Simulation | `192.168.56.40` | Kali Linux, Hydra |



All VMs communicate through an isolated VirtualBox internal network:



`192.168.56.0/24`



The host can access the Wazuh environment through a separate host-only interface.



\---



\## Detections



\### 01 — SSH Brute-Force Detection



\*\*MITRE ATT\&CK:\*\* T1110 — Brute Force



Simulated repeated SSH authentication failures against the Ubuntu endpoint from the Kali attacker VM.



The default Wazuh ruleset generated individual alerts for each failed authentication attempt. A custom correlation rule was then created to identify repeated failures from the same source IP within a defined time window.



\*\*Detection rule:\*\*



```text

Rule ID: 100010

Severity: 10

Technique: T1110 — Brute Force

Threshold: 5 matching events

Timeframe: 120 seconds



Detection Engineering

The custom Wazuh rule correlates multiple authentication failures from the same source:

<rule id="100010" level="10" frequency="5" timeframe="120" ignore="60">

&#x20; <if\_matched\_sid>5760</if\_matched\_sid>

&#x20; <same\_source\_ip />

&#x20; <description>SSH brute force attempt detected - multiple failed logins from same source (T1110)</description>

&#x20; <mitre>

&#x20;   <id>T1110</id>

&#x20; </mitre>

&#x20; <group>authentication\_failures,pci\_dss\_10.2.4,pci\_dss\_10.2.5,</group>

</rule>

\[View detection rule](rules/100010-ssh-bruteforce.xml)

Detection Result



\- Default Wazuh rules: 96 individual low-severity alerts

\- Custom correlation rule: 1 high-severity detection

\- Correlation: 5+ matching failures within 120 seconds

\- MITRE ATT\&CK: T1110 — Brute Force



The goal is to improve signal quality by correlating repeated authentication failures rather than treating every failed login as an isolated event.

Skills Demonstrated

\- Wazuh SIEM deployment and administration

\- Linux server administration

\- Windows endpoint monitoring

\- Sysmon

\- Linux auditd

\- Authentication log analysis

\- Custom Wazuh rule development

\- Event correlation

\- MITRE ATT\&CK mapping

\- SSH security monitoring

\- Network segmentation

\- Attack simulation

\- SOC alert triage

\- Incident-response workflow

Attack Simulation

The lab uses Kali Linux to safely simulate attacks against intentionally configured lab systems.

Example SSH brute-force simulation:

hydra -l vboxuser -P /usr/share/wordlists/rockyou.txt -t 1 -W 1 192.168.56.30 ssh



All attack activity is performed inside the isolated home-lab environment.

SOC Workflow

The lab follows a simplified SOC workflow:

Attack Simulation

&#x20;      ↓

Endpoint Logs

&#x20;      ↓

Wazuh Agent

&#x20;      ↓

Wazuh Manager

&#x20;      ↓

Detection Rule

&#x20;      ↓

Alert Correlation

&#x20;      ↓

Analyst Triage

&#x20;      ↓

Investigation

&#x20;      ↓

Containment / Hardening



Evidence



Screenshots and raw alert evidence from the detection are available here:



\[View SSH brute-force detection evidence](screenshots/01-ssh-bruteforce/)



Repository Structure

wazuh-soc-home-lab/

│

├── README.md

├── wazuh\_lab\_architecture.png

│

├── writeups/

│   └── 01-ssh-bruteforce.md

│

├── rules/

│   └── 100010-ssh-bruteforce.xml

│

└── screenshots/

&#x20;   └── 01-ssh-bruteforce/



Disclaimer

This is a personal cybersecurity self-study project created in an isolated virtual lab environment.

All attack simulations are performed against systems owned and controlled by the author for educational and defensive security research purposes.

This project does not represent professional work experience.

