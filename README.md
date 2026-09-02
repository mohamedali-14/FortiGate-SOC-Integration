# SOC Lab: FortiGate Firewall Configuration & Policy Integration

## Project Overview
This repository documents my role as **Network Security Engineer 2** in a graduation project focused on building a miniature SOC environment. My primary responsibilities were designing the network segmentation, configuring the FortiGate firewall policies, implementing NAT, and setting up the initial logging infrastructure to forward data to the ELK Stack (SIEM).

## My Role & Responsibilities
- Network Segmentation: Implemented separate LAN and DMZ zones to isolate untrusted traffic.
- Address Objects: Created network objects for servers, clients, and the Kali attacker machine to streamline policy management.
- Firewall Policies: Designed and implemented strict firewall policies governing traffic flow between LAN, DMZ, and WAN.
- Security Rules: Applied "Implicit Deny" at the end of the policy table.
- Logging: Configured FortiGate to log all traffic events (allowed and blocked) for SOC analysis.

## Network Topology
*(Insert the diagram image here)*

## Firewall Configuration

### Interface & Zone Assignments
| Interface | Role | IP/Netmask | Admin Access |
| :--- | :--- | :--- | :--- |
| port1 | WAN | 192.168.1.40/24 | PING, HTTPS |
| port2 | LAN | 20.0.0.1/8 | PING, HTTPS, SSH |
| port3 | DMZ | 30.0.0.1/8 | PING |

### Address Objects
| Name | Type | Subnet/IP | Interface |
| :--- | :--- | :--- | :--- |
| LAN_Network | Subnet | 20.0.0.0/8 | port2 |
| DMZ_Network | Subnet | 30.0.0.0/8 | port3 |
| Kali_IP | IP/Netmask | 30.0.0.10/32 | port3 |
| Windows_Server | IP/Netmask | 20.0.0.10/32 | port2 |
| Windows_Client | IP/Netmask | 20.0.0.20/32 | port2 |
| Ubuntu_ELK | IP/Netmask | 20.0.0.30/32 | port2 |

### Firewall Policies
*(Paste your exact table here)*
| # | Name | From -> To | Source -> Dest | Action | NAT | Log |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | LAN_to_WAN | LAN -> WAN | LAN_Network -> all | ACCEPT | ✓ | ✓ |
| 2 | LAN_to_DMZ | LAN -> DMZ | LAN_Network -> Kali_IP | ACCEPT | x | ✓ |
| 3 | DMZ_to_LAN | DMZ -> LAN | Kali_IP -> LAN_Network | ACCEPT | x | ✓ |
| 4 | DMZ_to_Internet | DMZ -> WAN | Kali_IP -> all | ACCEPT | ✓ | ✓ |
| - | Implicit Deny | any -> any | all -> all | DENY | - | - |

## SOC Integration (Log Flow)
My role involved setting up the initial log forwarding architecture to enable the SOC team to analyze data. The planned flow is:
- **FortiGate** ---> (Syslog UDP 514) ---> **Logstash**
- **Windows** ---> (Sysmon/Winlogbeat) ---> **Logstash**
- **Logstash** ---> (Parse/Process) ---> **Elasticsearch**
- **Elasticsearch** ---> **Kibana** (Dashboards & Alerts)

## Security Scenarios Addressed
Based on the policies I configured, the lab is designed to detect and log the following scenarios:
1. Client attempting to access the Internet (Allowed and Logged).
2. Kali Linux performing a Port Scan on the Server (Attempts Logged, some blocked).
3. Kali Linux attempting unauthorized entry (Failed logins Logged).
4. Admin logging into FortiGate (System logs recorded).

## Technologies Used
- FortiGate (FortiOS)
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Syslog (UDP 514)
- Winlogbeat, Sysmon
- Windows Server / Client, Kali Linux, Ubuntu
