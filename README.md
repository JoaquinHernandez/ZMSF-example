# ZMSF-example
![Project Obsidian Banner](https://via.placeholder.com/1200x250/0b0f19/00ffcc?text=PROJECT+OBSIDIAN:+Zero+Meaning+Security+Framework)

# 🛡️ Project Obsidian: Zero Meaning Security Framework (ZMSF)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Stack: Ubuntu | Nginx | MongoDB | Wazuh](https://img.shields.io/badge/Stack-Ubuntu%20%7C%20Nginx%20%7C%20MongoDB%20%7C%20Wazuh-success)](#)
[![Security: Fail2Ban | iptables | LUKS](https://img.shields.io/badge/Security-Fail2Ban%20%7C%20iptables%20%7C%20LUKS-critical)](#)

**Project Obsidian (ZMSF)** is a hardened, production-ready Zero Trust architecture designed for bare-metal and containerized Linux environments. Built entirely on free, open-source tooling, this framework assumes *zero inherent trust* for any connection, internal endpoint, or data store.

This framework provides a comprehensive blueprint for strict microsegmentation, aggressive ingress/egress filtering, endpoint telemetry, and cryptographic data protection using a highly optimized stack: **Nginx, iptables, Fail2Ban, Wazuh, and encrypted MongoDB**.

---

## 🏗️ Architectural Core

Obsidian operates on a defense-in-depth model that enforces strict verification across the edge, the kernel, the endpoint, and the data layer.

1. **Kernel-Level Microsegmentation (iptables):** Default `DROP` policies on all chains. Implements explicit WireGuard mesh routing rules and reverse-shell kernel egress traps to prevent lateral movement and unauthorized outbound beaconing.
2. **Endpoint Security & Telemetry (Wazuh):** Continuous endpoint monitoring. Provides File Integrity Monitoring (FIM), rootkit detection, and real-time behavioral analysis to detect anomalies on the host device itself.
3. **Dynamic Intrusion Prevention (Fail2Ban):** Real-time log ingestion via `systemd` journal and file parsing. Automatically updates `iptables` drop chains based on aggressive scanning, Wazuh alerts, or failed authentication attempts.
4. **Edge Proxy & WAF (Nginx + ModSecurity):** Handles all TLS termination (Let's Encrypt), enforces strict server name indication (SNI) matching, and aggressively drops unauthenticated or malformed payloads using connection closures (Status 444) rather than active responses.
5. **Encrypted Data Persistence (MongoDB 7.0 + LUKS):** Heavily isolated database backend restricted to local loopback or mTLS networks. Enforces **Encryption at Rest** using LUKS block-device encryption and Client-Side Field Level Encryption (CSFLE) to ensure data is never exposed in plaintext, even if the disk or DB is compromised.

---

## 📂 Repository Structure

```text
OBSIDIAN-ZMSF/
├── configs/
│   ├── nginx/
│   │   ├── nginx.conf                 # Hardened global configurations
│   │   ├── conf.d/zmsf_proxy.conf     # Reverse proxy and 444 drop rules
│   │   └── modsec/                    # ModSecurity WAF rulesets
│   ├── fail2ban/
│   │   ├── jail.local                 # Custom aggressive jail definitions
│   │   └── filter.d/                  # Regex filters for Nginx and SSH
│   ├── iptables/
│   │   └── rules.v4                   # IPv4 default DROP and egress traps
│   ├── endpoint/
│   │   └── wazuh-agent.ossec.conf     # FIM and behavioral monitoring configs
│   └── mongodb/
│       └── mongod.conf                # Localhost binding and TLS settings
├── scripts/
│   ├── deploy_obsidian.sh             # Initial setup and hardening script
│   ├── setup_luks_encryption.sh       # Disk encryption provisioning for MongoDB
│   └── flush_stale_bans.sh            # Cron job for Fail2Ban database maintenance
├── docs/
│   └── THREAT_MODEL.md                # Attack vectors and mitigation strategies
└── README.md

🚀 Quickstart
Clone the repository to your target Ubuntu VPS.

Initialize the LUKS encrypted volume for your database:

Bash
sudo ./scripts/setup_luks_encryption.sh
Review and modify the environment variables in scripts/deploy_obsidian.sh.

Execute the deployment script as root to apply the baseline hardening:

Bash
sudo chmod +x scripts/deploy_obsidian.sh
sudo ./scripts/deploy_obsidian.sh
Tail your syslog and Fail2Ban logs to monitor active threat mitigation:

Bash
sudo tail -f /var/log/fail2ban.log
🛡️ License & Contributing
Project Obsidian is released under the MIT License. Pull requests focusing on enhanced failregex patterns, advanced kernel routing, EDR integration optimizations, or cryptographic enhancements are welcome. Do not submit public issues for security vulnerabilities; please use GitHub's private vulnerability reporting feature.
