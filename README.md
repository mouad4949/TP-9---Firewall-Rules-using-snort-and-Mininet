# Network Security Lab: IDS Monitoring and Firewall Filtering

## 📖 Project Overview

This project demonstrates the complete lifecycle of a network security incident response within a virtualized environment. Using **Mininet** to simulate a corporate network, the lab focuses on detecting malicious traffic using **Snort IDS**, capturing forensic evidence with **Tcpdump**, and mitigating the threat using **Iptables** firewall rules.

## 🎯 Objectives

- **Simulate** a network attack involving malware transmission.
- **Detect** malicious signatures in real-time using an Intrusion Detection System (Snort).
- **Analyze** network traffic by capturing packets (PCAP).
- **Mitigate** the threat by implementing a Drop Rule in the Linux Firewall (Iptables).

## 🛠️ Technologies & Tools

- **Mininet**: Network topology simulation.
- **Snort**: Network Intrusion Detection System (NIDS).
- **Iptables**: Linux kernel firewall for packet filtering.
- **Tcpdump**: Command-line packet analyzer.
- **Linux (CyberOps Workstation)**: Host operating system.

## 🏗️ Network Topology

- **Router (R1)**: Acts as the gateway, IDS sensor, and Firewall.
- **Host (H10)**: Simulates an external malicious server (IP: `209.165.202.133`).
- **Host (H5)**: Simulates an internal victim client (IP: `209.165.200.235`).

## 🚀 Lab Walkthrough

### Phase 1: Environment Setup

The network was initialized using a Python script to deploy the topology.
```bash
sudo ./lab.support.files/scripts/cyberops_extended_topo_no_fw.py
```

### Phase 2: Intrusion Detection (Snort)

Snort was launched on Router R1 in console mode to monitor traffic in real-time.

**Command:**
```bash
./lab.support.files/scripts/start_snort.sh
```

**Result:** When H5 downloaded the W32.Nimda.Amm.exe malware from H10, Snort generated the following alert:
```
[**] [1:1000003:0] Malicious Server Hit! [**] [Priority: 0] {TCP}
```

### Phase 3: Forensic Capture (Tcpdump)

To preserve evidence, a packet capture was executed on the victim machine (H5) during the attack.
```bash
tcpdump -i H5-eth0 -w nimda.download.pcap &
```

**Output:** A .pcap file was generated, confirming the transmission of the malware payload via TCP port 6666.

### Phase 4: Mitigation (Iptables)

To block the attack, a firewall rule was added to the FORWARD chain on Router R1.

**Rule Logic:** Drop all TCP packets destined for the malicious server on port 6666.
```bash
iptables -I FORWARD -p tcp -d 209.165.202.133 --dport 6666 -j DROP
```

**Verification:** After applying the rule, subsequent wget attempts from H5 resulted in a Connection Timed Out, confirming successful mitigation.

## 📊 Key Findings

| Metric | Value |
|--------|-------|
| Malware Port | TCP 6666 |
| Attacker IP | 209.165.202.133 |
| Snort Mode | IDS (Detection only) |
| Mitigation Status | Success (Traffic Dropped) |
