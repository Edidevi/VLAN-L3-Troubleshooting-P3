---
layout: troubleshooting
title: VLAN L3 Troubleshooting — P3
description: "Enterprise inter-VLAN support scenarios. Real-world incident tickets simulating network faults across a Layer 3 switched environment."
diagram: https://github.com/user-attachments/assets/6eaf30bc-db18-47cd-b55a-79eddaa9899e

concepts:
  - Inter-VLAN Routing
  - SVI Misconfiguration
  - Layer 1 to Layer 3 Methodology
  - SVI Verification
  - IP Address Troubleshooting

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

    steps:
      - layer: "Layer 1"
        title: "Baseline Check & Isolating the Fault"
        findings:
          - "Instead of moving robotically, troubleshooting was based off of the work already done by the service desk team."
          - "Pinged the SVI for VLAN 20 (172.16.20.1) from Sales1 to try and isolate the issue."
          - "The ping was successful, which isolated the issue to the inter-VLAN routing area."
        images:
          - src: https://github.com/user-attachments/assets/5ceb5d57-daa4-4bee-aa4c-a0480a1ba2dc
            caption: "Successful ping to VLAN 20 SVI — fault isolated to inter-VLAN routing"

      - layer: "Layer 3"
        title: "Layer 3 Switch & SVI Verification"
        findings:
          - "Enabled IP routing on D1 and pinged HR2 to see if there was any change — no change observed."
          - "Typed <code>ip route</code> on D1 — saw connected routes for all 3 networks, no manual routes needed."
          - "Typed <code>show vlan</code> on both switches to confirm interfaces were assigned to VLANs correctly — no issues found."
          - "Entered <code>show ip interface brief</code> on D1 to check the SVI IP addresses — all SVI IP addresses were abnormal, none were the first IP address in their respective subnets."
        images:
          - src: https://github.com/user-attachments/assets/cbc25357-a674-4674-a605-61b3ce7035b0
            caption: "ip route on D1 — connected routes visible for all 3 networks"
          - src: https://github.com/user-attachments/assets/5c508fe2-333b-439a-8f62-50ab92fa7537
            caption: "show ip interface brief on D1 — SVI IP addresses showing incorrect values"

      - layer: "Resolve"
        title: "Correcting SVI IP Addresses"
        findings:
          - "Used <code>interface vlan (vlan id)</code> followed by <code>ip address (ip address) (subnet mask)</code> in global configuration mode."
          - "Amended all SVI IP addresses to the correct first IP address in each subnet."
        images:
          - src: https://github.com/user-attachments/assets/ac18bf0a-1e75-4e3d-9a1b-cac18d7158bc
            caption: "SVI IP addresses corrected to the first usable address in each subnet"

      - layer: "Verify"
        title: "Inter-VLAN Connectivity Test"
        findings:
          - "Pinged HR2 and IT1 from Sales1 — both pings were successful."
        images:
          - src: https://github.com/user-attachments/assets/6bb76124-a1c7-4a06-ad18-cfb8ec42fbb9
            caption: "Successful ping from Sales1 to HR2"
          - src: https://github.com/user-attachments/assets/7c6476e0-4650-40ce-97c5-d15c52576346
            caption: "Successful ping from Sales1 to IT1"

    resolution:
      - "Sales could not reach HR or IT because the end device default gateway IP addresses and the SVI IP addresses did not match."
      - "The initial ping to 172.16.20.1 succeeded only because that address was pinged directly — not the actual misconfigured SVI address."
      - Reconfiguring the SVIs solved the issue.

learnings:
  - "Use prior service desk findings to guide your starting point — avoid robotic layer-by-layer checks when the fault area can already be narrowed down."
  - "Always verify SVI IP addresses match the expected first usable address in the subnet — incorrect SVIs silently break inter-VLAN routing."
  - "A mismatch between end device default gateways and SVI addresses means traffic will never reach its destination, even if all other config is correct."
---
