
---

# Wazuh SIEM - Incident Response Home Lab

A hands on cybersecurity home lab demonstrating real world threat detection and incident response using Wazuh SIEM. Built in VMware Workstation with multiple VMs including kali linux to simulate an attacker and victim environment.

---

## Lab Setup

| VM          | Role                             | IP             | OS          |
| ----------- | -------------------------------- | -------------- | ----------- |
| SIEM Server | Wazuh v4.7.5 Manager + Dashboard | 192.168.48.140 | Ubuntu      |
| Agent 001   | Monitored victim endpoint        | 192.168.48.148 | Kali Linux  |
| Attacker    | Attack source                    | Same subnet    | Debian 13.x |

All VMs are isolated on a VMware NAT network with no internet exposure.

---

## What Wazuh Detected

| Finding                                                              | Severity | MITRE        | Rule  |
| -------------------------------------------------------------------- | -------- | ------------ | ----- |
| Trojaned system binaries (`/usr/bin/diff`, `/bin/diff`, `/bin/chsh`) | HIGH     | T1014        | 510   |
| `/etc/shadow` modified in real time (hash changed)                   | CRITICAL | T1565.001    | 550   |
| `/etc/sudoers` modified in real time                                 | CRITICAL | T1565.001    | 550   |
| Rogue root accounts created (UID 0)                                  | CRITICAL | T1136, T1078 | N/A   |
| Defense evasion activity where agent was stopped 3 times             | MEDIUM   | T1562.001    | 506   |
| SSH brute force attempt (14M+ passwords)                             | MEDIUM   | T1110        | N/A   |
| SSH hardening issues identified (9 findings)                         | MEDIUM   | N/A          | 19007 |
| AppArmor policy violations                                           | LOW      | N/A          | 52002 |

---

## Attacks Simulated

```bash
# SSH brute force
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1 -t 4

# Rogue root account
echo "root2:x:0:0:root:/root:/bin/bash" >> /etc/passwd
sudo useradd -o -u 0 -g 0 -M hacker

# Critical file tampering
echo "test" >> /etc/shadow
echo "test" >> /etc/sudoers
```

---

## Key Wazuh Features Used

* File Integrity Monitoring (FIM)
  Real time detection of `/etc/shadow` and `/etc/sudoers` changes with SHA256 hash comparison

* Rootcheck
  Detection of trojaned binaries matching known rootkit signatures

* SCA
  Automated SSH hardening audit

* MITRE ATT and CK mapping
  Automatic tagging of alerts to tactics and techniques

* PAM log analysis
  Detection of privilege escalation activity

* AppArmor integration
  Policy violation alerting

---

## Compliance Frameworks Triggered

PCI DSS
HIPAA
GDPR
NIST 800-53
GPG13
TSC

---

## Files

```
wazuh-ir-lab/
├── README.md
├── ir-report.pdf
└── screenshots/
```

---

## How to Replicate

**Install Wazuh (Ubuntu):**

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.7/config.yml
# Edit config.yml and replace IPs with your SIEM VM IP
sudo bash wazuh-install.sh -a -i
```

**Install agent (on victim VM):**

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.7.3-1_amd64.deb
sudo WAZUH_MANAGER='YOUR_SIEM_IP' dpkg -i ./wazuh-agent_4.7.3-1_amd64.deb
sudo systemctl enable wazuh-agent && sudo systemctl start wazuh-agent
```

**Enable realtime FIM on /etc (in `/var/ossec/etc/ossec.conf`):**

```xml
<directories realtime="yes">/etc,/usr/bin,/usr/sbin</directories>
```

---

`Wazuh` `SIEM` `Incident Response` `FIM` `MITRE ATT&CK` `Linux` `VMware` `SOC` `Threat Detection`
