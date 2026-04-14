# Practice 01: Zero Trust Network Architecture & VLAN Segmentation

## Objective
To design and implement a secure, segmented network topology for a Small and Medium Business (SMB) environment. The primary goal is to isolate sensitive administrative workstations from exposed Internet of Things (IoT) devices (CCTV cameras, printers) and general user traffic, effectively preventing lateral movement in the event of a breach.

## SMB Scenario & Threat Model
A local organization requires a network upgrade to accommodate 8 new IP cameras alongside their administrative and general staff networks.
- **The Risk:** IoT devices often have weak default security or unpatched vulnerabilities. If deployed on a flat network, a compromised camera acts as a beachhead, allowing attackers to pivot and target the Director's PC or financial records.

## Logical Segmentation Design (802.1Q)
I designed a strict three-VLAN structure implementing the Principle of Least Privilege:
* **VLAN 10 (ADMIN):** Network `192.168.10.0/24`. (Confidential data, high security).
* **VLAN 20 (USERS):** Network `192.168.20.0/24`. (Standard workstations, Secretary PC).
* **VLAN 30 (IoT):** Network `192.168.30.0/24`. (Exposed infrastructure: CCTV, NVR).

## Physical Topology
Implemented a "Router-on-a-Stick" topology via Cisco Packet Tracer, a highly cost-effective and scalable routing approach for SMBs.

![Network Topology](topology.png)

## Technical Implementation & Evidence

### 1. VLAN Configuration (Layer 2)
Ports were assigned strictly to their respective operational VLANs. Trunking was configured on the uplink to the router to handle tagged frames.

![Switch VLAN Verification](switch_config.png)

### 2. Zero Trust Access Control Lists (Layer 3)
To enforce true isolation, I configured Extended Access Control Lists (ACLs) on the gateway (Router). 
The firewall rules dictate that:
1. IoT devices (VLAN 30) are strictly forbidden from initiating connections to USERS or ADMIN networks.
2. USERS (VLAN 20) are forbidden from accessing the ADMIN network.
3. ADMIN (VLAN 10) retains outbound access to manage the NVR and IoT devices.

### 3. Proof of Isolation (Security Audit)
To validate the security posture, cross-VLAN ICMP traffic was tested. 

As demonstrated below, while the Admin PC can successfully reach the network, any unauthorized traffic originating from the IoT Camera targeting the Secretary PC is actively blocked by the router (`Destination host unreachable`), confirming the containment strategy.

![Isolation Proof - Ping Blocked vs Ping Allowed](isolation_proof.png)

## Cybersecurity Impact (Conclusion)
By deploying this Zero Trust segmentation, the infrastructure is cryptographically and logically shielded. If a threat actor exploits a vulnerable CCTV camera, they remain trapped within the VLAN 30 sandbox. They are denied network visibility and access to critical organizational assets, neutralizing the ransomware propagation threat at the perimeter.
