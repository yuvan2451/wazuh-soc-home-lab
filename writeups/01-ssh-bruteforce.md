\# SSH Brute-Force Detection — T1110



\## Overview



Simulated an SSH brute-force attack from Kali Linux against an Ubuntu endpoint and created a custom Wazuh correlation rule to detect repeated authentication failures.



\## Lab



| Component | IP |

|---|---|

| Wazuh Manager | `192.168.56.10` |

| Ubuntu Endpoint | `192.168.56.30` |

| Kali Attacker | `192.168.56.40` |



\## Attack



Hydra was used to generate repeated SSH authentication failures:



```bash

sudo hydra -l vboxuser -P /usr/share/wordlists/rockyou.txt -t 1 -W 1 192.168.56.30 ssh



The failed attempts were recorded in Ubuntu's /var/log/auth.log and forwarded to Wazuh.

Detection

Wazuh's default rule 5760 generated individual alerts for each failed authentication attempt.

During the test:

\- 96 individual low-severity alerts

\- Custom rule: 100010

\- Severity: 10

\- Threshold: 5 failures

\- Timeframe: 120 seconds

\- Same source IP required

Custom Rule

<rule id="100010" level="10" frequency="5" timeframe="120" ignore="60">

&#x20; <if\_matched\_sid>5760</if\_matched\_sid>

&#x20; <same\_source\_ip />

&#x20; <description>SSH brute force attempt detected - multiple failed logins from same source (T1110)</description>

&#x20; <mitre>

&#x20;   <id>T1110</id>

&#x20; </mitre>

&#x20; <group>authentication\_failures,pci\_dss\_10.2.4,pci\_dss\_10.2.5,</group>

</rule>



Result

The custom rule correlated repeated authentication failures from 192.168.56.40 and generated a high-severity alert instead of leaving the activity as individual low-severity events.

MITRE ATT\&CK: T1110 — Brute Force

Tactic: Credential Access

Analyst Response

1\. Verify whether the source IP is authorized.

2\. Check for successful login attempts after the failures.

3\. Block the source if malicious.

4\. Review and harden SSH authentication controls.

Evidence

Screenshots and raw alert evidence:

screenshots/01-ssh-bruteforce/

