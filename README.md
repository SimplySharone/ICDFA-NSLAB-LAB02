# ICDFA-NSLAB-LAB02
# Laboratory 2: OPNsense Firewall Policy Testing, Logging, and Packet Analysis
**Course:** WADF105 - Network Security Fundamentals

## Part A: Baseline Connectivity Verification
### Evidence E1: Successful Baseline Tests
*Description: Terminal outputs capturing initial un-restricted status across Layer 3 paths and high-level application protocols (ICMP, DNS, HTTP, and HTTPS).*
![Evidence E1: Baseline Connectivity](images/e1_baseline.png)
*Caption: Ubuntu workstation capturing clean end-to-end routing, successful system host registration, and normal layer execution prior to policy restrictions.*

---

## Part B & C: Top-Down Rule Processing & ICMP Restrictions
### Evidence E2: LAN Interface Rule Layout Order
*Description: OPNsense interface management console highlighting rule sequencing order with specific block restrictions explicitly placed above the broad implicit matching rules.*
![Evidence E2: LAN Rules Hierarchy](images/e2_rules_hierarchy.png)
*Caption: Rule chain overview showcasing explicit block directives matching 'LAB2 BLOCK' policies evaluated before parsing hits the broad allow rule fallback entries.*

### Evidence E3: Protocol-Specific Filtering (ICMP Blocked)
*Description: Terminal view tracing execution states where ICMP requests fail or timeout, while parallel application layers complete successfully.*
![Evidence E3: Protocol-Specific Testing](images/e3_icmp_block.png)
*Caption: Terminal testing showing complete packet truncation targeting destination address 1.1.1.1 while parallel layers successfully compile lookup queries.*

---

## Part D: Outbound Application Restrictions
### Evidence E4: Port-Level Protocol Filtering (HTTP Drop vs. HTTPS Clear)
*Description: Execution view comparing raw unencrypted web connections to secure transaction endpoints.*
![Evidence E4: Port Level Isolation](images/e4_http_block.png)
*Caption: Active tracking tracing failure drops targeting unencrypted web server endpoints on port 80 while cryptographically secure ports pass clear responses.*

---

## Part E: OPNsense Engine Live Diagnostic Auditing
### Evidence E5: Live Packet Filter Logs
*Description: Administrative dashboard view showing security policy actions matching localized source rules.*
![Evidence E5: Live Diagnostic Log Entries](images/e5_live_logs.png)
*Caption: Diagnostic window logs indexing specific dropping interventions with matching structural policy labels mapped to local system engines.*

```text
======================================================================================
                     OPNSENSE FIREWALL LOG ENTRY BREAKDOWN
======================================================================================
• LOG ENTRY 1 (ICMP Block):
  - Timestamp: [Insert Timestamp]          - Interface: LAN (em0)
  - Source IP: 10.10.10.x                  - Destination IP: 1.1.1.1
  - Protocol: ICMP (Echo Request)          - Action: Block / Drop
  - Assigned Rule Label: LAB2 BLOCK ICMP TO 1.1.1.1

• LOG ENTRY 2 (HTTP Port 80 Block):
  - Timestamp: [Insert Timestamp]          - Interface: LAN (em0)
  - Source IP: 10.10.10.x                  - Destination IP: [Target IP Address]
  - Protocol: TCP                          - Destination Port: 80 (HTTP)
  - Action: Block / Drop                   - Assigned Rule Label: LAB2 BLOCK OUTBOUND HTTP
======================================================================================
```

---

## Part F: Wireshark Packet Capture Deep-Dive Validation
### Evidence E6: Packet Stream Comparison Analysis
*Description: Structural trace details showing transport differences under active firewall policies.*
![Evidence E6: Packet Stream Overviews](images/e6_wireshark.png)
*Caption: Visual packet sequence tracking illustrating repetitive retransmission loops on filtered profiles versus clean handshakes on allowed links.*

---

## Part G: Kernel Core Translation State Table Inspections
### Evidence E7: Active State Mapping Projections
*Description: State diagnostic engine displaying dynamic link translation properties.*
![Evidence E7: Internal State Tracking](images/e7_state_table.png)
*Caption: Core connection table displaying local private interface mapping bindings linked directly through automated external WAN NAT operations.*

---

## Part H: Environment Decommissioning & Restoration
### Evidence E8: Restored Workspace Verification Checks
*Description: System rule workspace showing test policies completely disabled and normal interface functionality restored.*
![Evidence E8: Workspace Restoration Checkpoints](images/e8_restoration.png)
*Caption: Terminal summary verifying baseline status has returned to a normal clear state across all structural checking pipelines.*

---

## Part I: Laboratory Analytical Review Questions

### 1. Why must the specific block rules be placed above the broad allow rule?
Firewall evaluation engines parse active interface policy blocks down the rule matrix sequentially from top to bottom, applying an immediate matching trigger logic. If a broad, un-restricted allow rule is positioned above a targeted block rule, any packet traversing that boundary will immediately fulfill the parameters of the generic allow directive and pass through. A narrow block rule must sit higher in the sequence to trap the targeted traffic profile before it can reach the fallback allow rule.

### 2. Which five packet attributes are most useful when explaining a firewall decision?
The five fundamental headers leveraged to determine and explain security rule choices are:
* **Protocol Type:** Defines the structural syntax and behavior layout of the frame (e.g., TCP, UDP, ICMP).
* **Source IP Address:** Validates the network identity layer of the machine initiating the transmission.
* **Source Port:** Tracks the temporary system connection descriptor port managed by the client kernel.
* **Destination IP Address:** Identifies the final endpoint target host machine configuration routing path.
* **Destination Port:** Specifies the application layer task or utility daemon running on the receiver.

### 3. Why did blocking ICMP not block HTTPS?
Firewalls read and filter at the transport and network layer headers independently. The initial block policy specifically checked for the Internet Control Message Protocol (ICMP), which manages structural diagnostics like echo requests. Hypertext Transfer Protocol Secure (HTTPS) runs entirely across a separate transport wrapper using the Transmission Control Protocol (TCP) targeting destination port 443. Because the rule was restricted to ICMP, TCP traffic bypassed the rule unhindered.

### 4. What difference did you observe between the blocked TCP port 80 traffic and permitted TCP port 443 traffic?
The blocked port 80 traffic showed an incomplete connection pattern where the client sent `SYN` packets but received no replies from the firewall, resulting in repeated retransmission timeouts. The permitted port 443 traffic successfully completed a standard three-way handshake (`SYN` -> `SYN-ACK` -> `ACK`), followed by a fluid stream of application layer data packets (TLS cryptographic handshakes and encrypted record exchanges).

### 5. What role does outbound NAT play when icdfa-nslab-client-v1 uses a private IPv4 address?
Because the workstation resides inside an isolated local space using non-routable private addresses (`10.10.10.0/24`), external public internet infrastructure cannot route responses back directly. Outbound Network Address Translation (NAT) maps these local source IPs to the firewall's public-facing WAN adapter IP as packets pass outbound. It automatically keeps track of these translations in its state table so that external responses can be returned back to the correct internal client workstation.

### 6. Why is restoring the original state an important part of a controlled security laboratory?
Cleaning up and restoring the baseline environment ensures that subsequent infrastructure labs or separate configuration deployments build upon a reliable, predictable network state. Leaving temporary rules active can create hard-to-troubleshoot permission conflicts later on, masking whether future failures are caused by new lab tasks or leftover test policies.
