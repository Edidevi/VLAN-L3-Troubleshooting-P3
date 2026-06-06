# Inter-VLAN-troubleshooting P3
<img width="567" height="407" alt="image" src="https://github.com/user-attachments/assets/6eaf30bc-db18-47cd-b55a-79eddaa9899e" />

Scenarios for enterprise inter-VLAN support

## 🎫 Incident Ticket INC-01078 
**Priority:** `P2 — High` **Reported by:** `Jodie Walters, Sales lead` **Status:** `open` **Time logged:** `09:14`
<img width="467" height="522" alt="image" src="https://github.com/user-attachments/assets/6645e3db-91c7-4894-94f0-0054e6829fe4" />

**<ins>Ticket Key Points:</ins>**
 - Sales cannot ping HR or IT


**<ins>Step 1: Layer 1</ins>**
- Instead of moving robotically, I decided to base my troubleshooting off of the work already done by the service desk team.
- I then pinged the SVI for vlan 20 (172.16.20.1) from sales 1 to try and isolate issue
THe ping was succesful, which isolated the issue to this area
<img width="200" height="200" alt="image" src="https://github.com/user-attachments/assets/5ceb5d57-daa4-4bee-aa4c-a0480a1ba2dc" />

**<ins>Step 2: The layer 3 switch or the layer  switches?</ins>**

- Now that i had isolated the possible fault, I enabled ip routing on D1.
- Pinged HR2 to see any chnage, saw no change.
- Typed `ip route` on D1, saw connected routes for all 3 networks.

<img width="550" height="132" alt="image" src="https://github.com/user-attachments/assets/cbc25357-a674-4674-a605-61b3ce7035b0" />

- From my ccna study, i know there is no need to configure routes manually.
- I then typed `show vlan` on both switches to see if interfaces were assigned to VLANs properly, I still saw no issues.
- My next step was to check the  SVIs, were they configured correctly? 
- I entered `show ip interface brief` on D1, I could check the SVI IP addresses, saw that all SVI ip addresses looked abnormal, they weren't the 1st ip address in the subnet.

 <img width="287" height="85" alt="image" src="https://github.com/user-attachments/assets/5c508fe2-333b-439a-8f62-50ab92fa7537" />

- I used the following commands in global config mode,  `interface vlan (vlan id)` then `ip-address (ip address) (subnet mask)`
- I amended all svi ip addresses to the first ip addresses in the subnet.
<img width="422" height="91" alt="image" src="https://github.com/user-attachments/assets/ac18bf0a-1e75-4e3d-9a1b-cac18d7158bc" />


- Then pinged HR2 and IT1 from SALES1, both pings were succesful


<img width="365" height="207" alt="image" src="https://github.com/user-attachments/assets/6bb76124-a1c7-4a06-ad18-cfb8ec42fbb9" />

<img width="493" height="238" alt="image" src="https://github.com/user-attachments/assets/7c6476e0-4650-40ce-97c5-d15c52576346" />


**<ins>Ticket Key Points Review:</ins>**
    
- ~~Sales cannot ping HR or IT~~ Sales could not reach HR or IT because the end device default gateway Ip addresses and the SVI ip addresses did not match,
- Even if everything was configred correctly, IP mismatch meant the information never got to the intended destination.
- The ping only worked at the start because I pinged whatshould  be VLan 20s Ip address, not what it actually was

