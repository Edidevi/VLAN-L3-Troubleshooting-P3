---
layout: troubleshooting
title: VLAN L3 Troubleshooting — P3
description: "Enterprise inter-VLAN support scenarios. Real-world incident tickets simulating network faults across a Layer 3 switched environment."
diagram: https://github.com/user-attachments/assets/6eaf30bc-db18-47cd-b55a-79eddaa9899e

concepts:
  - Inter-VLAN Routing
  - SVI Misconfiguration
  - Default Gateway Mismatch
  - IP Routing
  - Layer 1 to Layer 3 Methodology

environment:
  - "1x Cisco Layer 3 Switch (D1)"
  - "End Devices — Sales, HR & IT PCs"
  - "Cisco Packet Tracer"

incidents:
  - id: "Incident Ticket #INC-01078"
    priority: "P2 — High"
    reported_by: "Jodie Walters, Sales Lead"
    assigned_to: "Network Support"
    time: "09:14"

    symptoms:
      - "Sales cannot ping HR or IT"
      - "All devices appear to have IP addresses — DHCP functioning normally"

    steps:
      - layer: "Layer 1"
        title: "Targeted Diagnosis Based on Prior Service Desk Work"
        findings:
          - "Rather than starting robotically at physical layer checks, I leveraged the work already completed by the service desk team to narrow down the fault domain."
          - "Pinged the SVI for VLAN 20 (172.16.20.1) from Sales 1 to isolate whether the issue was local or cross-VLAN."
          - "Received a successful ping to the VLAN 20 SVI — confirming the Sales PC could reach its default gateway. The fault was above Layer 1."
        images:
          - src: https://github.com/user-attachments/assets/6645e3db-91c7-4894-94f0-0054e6829fe4
            caption: "Ping from Sales 1 to VLAN 20 SVI — successful, ruling out Layer 1 issues"

      - layer: "Layer 2 & Layer 3"
        title: "IP Routing, SVI & Default Gateway Verification"
        findings:
          - "Enabled IP routing on D1, then pinged HR2 from Sales 1 to test for any change — no improvement observed."
          - "Ran <code>show ip route</code> on D1 — confirmed connected routes were present for all three networks (Sales, HR, IT)."
          - "Ran <code>show vlan</code> on all switches to check for any VLANs not being allowed across trunks — no issues found."
          - "Reviewed the default gateway configuration on all end devices and compared against the SVI IP addresses configured on D1."
          - "Identified the root cause — all SVI IP addresses on D1 were misconfigured. The SVIs were not set to the first usable IP address in each subnet, causing a mismatch with the default gateways configured on the end devices."
          - "Corrected all SVI IP addresses on D1 to match the first IP address in each respective subnet."
        images:
          - src: https://github.com/user-attachments/assets/c84f4900-db3b-4833-a6cd-f596e467d604
            caption: "show ip route on D1 — connected routes present for all three VLANs"
          - src: https://github.com/user-attachments/assets/7c6476e0-4650-40ce-97c5-d15c52576346
            caption: "SVI IP address misconfiguration identified — addresses not matching end device default gateways"
          - src: https://github.com/user-attachments/assets/3283a90e-4da3-43ae-b1a4-59778ba1339f
            caption: "Corrected SVI IP addresses configured on D1"

      - layer: "Verify"
        title: "Inter-VLAN Connectivity Test"
        findings:
          - "After correcting all SVI IP addresses, pinged HR2 from Sales 1 — successful."
          - "Pinged IT1 from Sales 1 — successful."
          - "Full inter-VLAN connectivity restored across Sales, HR, and IT VLANs."
        images:
          - src: https://github.com/user-attachments/assets/f53e6367-7064-4bde-bf40-30900ccd8244
            caption: "Successful ping from Sales 1 to HR2 after SVI correction"
          - src: https://github.com/user-attachments/assets/9a6ee5af-5a48-4edb-bdd5-f49d14bbcb09
            caption: "Successful ping from Sales 1 to IT1 — full inter-VLAN routing confirmed"

    resolution:
      - "All SVI IP addresses on D1 were corrected to the first usable IP in each subnet, resolving the mismatch with end device default gateways."
      - "IP routing was enabled on D1 to allow inter-VLAN forwarding."
      - "Sales can now successfully reach both HR and IT across all VLANs."

learnings:
  - "Don't troubleshoot robotically — use available information from the service desk to narrow down the fault domain before starting."
  - "A successful ping to the local SVI is a quick way to confirm Layer 1 and Layer 2 are functioning and isolate the fault to Layer 3."
  - "SVI IP addresses must match the default gateway configured on end devices exactly — even a valid IP in the wrong position within a subnet will break connectivity silently."
  - "Always verify IP routing is enabled on Layer 3 switches — it is disabled by default on some Cisco platforms."
---
