# Testing the Firewall Rules

After configuring pfSense firewall rules, I verified that the security controls were working correctly by performing reconnaissance from a Kali Linux attacker machine.

---

## Test Environment

| Machine | IP Address | Purpose |
|----------|------------|---------|
| Kali Linux | DHCP | Attacker |
| pfSense | 192.168.10.1 | Firewall |
| Windows Server | 192.168.10.10 | Domain Controller |
| Windows 10 | DHCP | Workstation |
| Wazuh Server | 192.168.10.30 | SIEM |

---

# Before Applying Firewall Rules

Before creating firewall rules, Kali Linux had unrestricted access to the Windows Server.

### ICMP Ping

The Windows Server responded successfully to ping requests.

**Screenshot**

<img width="562" height="298" alt="Screenshot 2026-07-02 164916" src="https://github.com/user-attachments/assets/02ea7d91-340c-4983-ac92-dbaaf8160107" />


---

### Network Discovery

Host discovery detected the Windows Server.

**Screenshot**

<img width="586" height="207" alt="Screenshot 2026-07-02 165102" src="https://github.com/user-attachments/assets/e5226715-e126-4b9e-a577-4adf498f59d4" />


---

### Port Scan

Nmap discovered multiple exposed services including:

- DNS (53)
- Kerberos (88)
- MSRPC (135)
- LDAP (389)
- SMB (445)
- Global Catalog (3268)

This indicates that the server was fully reachable from Kali.

**Screenshot**

<img width="622" height="342" alt="Screenshot 2026-07-02 165114" src="https://github.com/user-attachments/assets/625cc1fb-dfc5-4315-b90a-f3d70be8b9ed" />


---
### Aggressive scan 

An intrusive network reconnaissance or vulnerability assessment technique that sends a high volume of packets rapidly across target systems

**Screenshot**

<img width="1086" height="715" alt="Screenshot 2026-07-02 165128" src="https://github.com/user-attachments/assets/49b8dfe9-c4cf-40b5-8b3d-93e2f9833598" />

---

# Firewall Hardening

The following pfSense rules were implemented:

- Block Kali Linux from accessing Windows Server
- Block Kali Linux from accessing Windows Workstation
- Allow only required LAN communication
- Restrict pfSense Web GUI to the administrator workstation
- Follow the Principle of Least Privilege

---

# After Applying Firewall Rules

After enabling the firewall rules, Kali Linux could no longer communicate with the protected hosts.

---

## Ping Test

The Windows Server no longer responded to ICMP requests.

Result:

- 100% Packet Loss

**Screenshot**

<img width="570" height="107" alt="Screenshot 2026-07-02 165215" src="https://github.com/user-attachments/assets/4f3cc3a5-21e9-4022-9171-7b63a23712fb" />


---

## Port Scan

Running an Nmap SYN scan produced:

```
All 1000 scanned ports are filtered
```

This confirms that the firewall is silently dropping packets instead of responding.

**Screenshot**

<img width="575" height="162" alt="Screenshot 2026-07-02 165919" src="https://github.com/user-attachments/assets/5e07fda5-5eee-4d53-a79e-8e05dddb5cdb" />


---

## Service Detection

Version detection (-sV) also failed.

No services were identified because every packet was filtered.

**Screenshot**

<img width="780" height="200" alt="Screenshot 2026-07-02 165926" src="https://github.com/user-attachments/assets/17b3347d-239b-431b-9fff-2b9602347107" />


---

## pfSense Web Interface

Attempting to access the pfSense Web GUI from Kali resulted in:

```
curl: (28) Failed to connect
```

This confirms that only the administrator workstation can access the firewall management interface.

**Screenshot**

<img width="822" height="67" alt="Screenshot 2026-07-02 165202" src="https://github.com/user-attachments/assets/884d7c46-9da0-496b-9329-20e7b657692b" />


---

# Security Outcome

The firewall successfully prevented:

- Host discovery
- ICMP reconnaissance
- TCP port scanning
- Service fingerprinting
- Unauthorized access to pfSense Web UI

The environment now follows the **Principle of Least Privilege**, allowing only explicitly permitted communication.

---

# Skills Demonstrated

- pfSense Firewall Administration
- Network Segmentation
- Principle of Least Privilege
- Access Control
- ICMP Filtering
- TCP Filtering
- Nmap Security Testing
- Defensive Validation
- Blue Team Operations
- SOC Lab Hardening
