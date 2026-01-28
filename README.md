# enterprise-soc-fortigate-wazuh-lab


> **Enterprise SOC & Network Security Lab (FortiGate + Wazuh)**

Repository name:

```
enterprise-soc-fortigate-wazuh-lab
```

---

# ✅ Final Combined GitHub README.md

You can paste everything below directly into your GitHub README.md:

---

# Enterprise SOC & Network Security Lab

**FortiGate • Wazuh • Active Directory (Planned) • Attack Simulation**

## 📌 Project Overview

This project documents the design, implementation, and troubleshooting of a simulated **enterprise cybersecurity lab environment**.
The lab is built for hands-on learning in:

* Network Security
* SOC Operations
* Firewall Configuration
* SIEM (Wazuh)
* Logging & Detection
* Blue Team Practices

The environment is hosted using **VMware Workstation** and simulates real-world enterprise segmentation.

---

## 🎯 Objectives

This lab helps practice:

* Building segmented enterprise networks
* Deploying and configuring FortiGate firewall
* Configuring routing, NAT, and firewall policies
* Deploying Wazuh SIEM and agents
* Troubleshooting connectivity and logging issues
* Understanding trust zones and attack surfaces
* Practicing SOC monitoring concepts

---

## 🧱 Lab Architecture

### Network Zones

| Zone    | Purpose                               | Trust Level |
| ------- | ------------------------------------- | ----------- |
| WAN     | Internet (VMware NAT)                 | Untrusted   |
| LAN     | User / Attacker network               | Medium      |
| Servers | Critical systems (Wazuh, AD, servers) | High        |
## Network Topology

![SOC Lab Network Topology](./images/network-topology.png)

---

## 💻 Environment

| Component       | OS / Device    | Role                    | IP Address          |
| --------------- | -------------- | ----------------------- | ------------------- |
| FortiGate VM    | FortiOS        | Firewall / Gateway      | WAN: 192.168.52.128 |
| FortiGate Port2 | Interface      | LAN / Attacker Network  | 10.10.10.1          |
| FortiGate Port3 | Interface      | Servers Network         | 10.10.20.1          |
| SOC Manager     | Ubuntu         | Wazuh Manager           | 10.10.20.128        |
| Ubuntu Server   | Ubuntu         | Monitored Server        | 10.10.20.130        |
| Windows Server  | Windows Server | Monitored Server        | 10.10.20.29         |
| Kali Linux      | Kali           | Attacker / Test Machine | 10.10.10.128        |

> Internal machines access the internet through FortiGate using NAT.

---

## 🔥 FortiGate Configuration

### Interfaces

* Port1 → WAN (VMware NAT)
* Port2 → LAN (Kali attacker network)
* Port3 → Servers network (Wazuh, Windows, Ubuntu)

### Firewall Policies

| Policy        | Source → Destination | NAT      | Logging      |
| ------------- | -------------------- | -------- | ------------ |
| LAN → WAN     | Port2 → Port1        | Enabled  | All Sessions |
| LAN → Servers | Port2 → Port3        | Disabled | All Sessions |
| Servers → WAN | Port3 → Port1        | Enabled  | All Sessions |

> Logging is critical. Without `logtraffic all`, Wazuh will not receive logs.

CLI example:

```
config firewall policy
    edit <policy_id>
        set logtraffic all
    next
end
```

---

## 📊 Wazuh Deployment

* Wazuh Manager installed on Ubuntu
* Wazuh Dashboard accessible via browser
* Agents installed on:

  * Windows Server
  * Ubuntu Server

Agents successfully appear as **Active** in dashboard.

---

## 🔄 Agent Troubleshooting Performed

After network changes, agents disconnected. Fix applied:

* Updated manager IP in agent config:

  * Linux: `/var/ossec/etc/ossec.conf`
  * Windows: `C:\Program Files (x86)\ossec-agent\ossec.conf`
* Removed old agents using `manage_agents`
* Deleted `client.keys`
* Restarted agent services

✅ Agents reconnected successfully

---

## 📡 FortiGate → Wazuh Syslog Integration

Correct configuration is critical:

* Use **FortiGate IP from same network as Wazuh**
* Do NOT use WAN IP

Correct config used:

```
<allowed-ips>10.10.20.1</allowed-ips>
```

FortiGate CLI:

```
config log syslogd setting
    set status enable
    set server 10.10.20.128
    set port 514
    set source-ip 10.10.20.1
end
```

---

## ✅ Validation Steps

Generate traffic:

```
ping 8.8.8.8
```

Check logs on Wazuh:

```
tail -f /var/ossec/logs/alerts/alerts.log
```

Verify:

* Source IP
* Destination IP
* Action (Accept/Deny)
* Policy name

---

## ⚠️ Common Issues Solved

| Issue                     | Cause              | Fix                     |
| ------------------------- | ------------------ | ----------------------- |
| No internet               | Missing gateway    | Set correct gateway     |
| Ping works, browser fails | DNS issue          | Fix DNS                 |
| Agents disappeared        | Old IP configs     | Re-register agents      |
| No logs in Wazuh          | Logging disabled   | Enable `logtraffic all` |
| Logs not received         | Wrong FortiGate IP | Use Port3 IP            |

---

## 📌 Limitations (FortiGate VM Free)

* Max 3 interfaces
* Max 3 firewall policies
* Limited features

Lab design was adapted to work within these limits.

---

## 📍 Current Status

✔ FortiGate routing functional
✔ NAT and internet working
✔ Wazuh Manager operational
✔ Agents connected
✔ Logging functional
❌ Active Directory not yet implemented
❌ Attack simulation in progress

---

## 🚧 Next Phase (Planned Improvements)

* Active Directory deployment
* Windows domain logging
* FortiGate advanced logging
* Attack simulations (Brute force, scanning, etc.)
* Custom Wazuh rules
* Detection engineering exercises

---

## 👤 Author

**Shakur**
Telecommunications Engineer | SOC & Network Security Enthusiast

---
