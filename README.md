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
- My next step was to check the IP addresses at the PCs, do they have the right IP addresses? THe right default gateways?
- I then saw the default gateways wre configured incorreclty,they werent the 1st Ip in the subnet
- Entered `show ip interface brief` on D1, I could check the SVI IP addresses, saw that all SVI ip addresses looked abnormal, they weren't the 1st ip address in the subnet.

 <img width="287" height="85" alt="image" src="https://github.com/user-attachments/assets/5c508fe2-333b-439a-8f62-50ab92fa7537" />

- I used the following commands in global config mode,  `interface vlan (vlan id)` then `ip-address (ip address) (subnet mask)`
- I amended all svi ip addresses to the first ip addresses in the subnet







Then pinged HR2 from sales 1 and it was succesful
THen pinged IT1 from sales 1 and it was succesful

<img width="327" height="187" alt="image" src="https://github.com/user-attachments/assets/c84f4900-db3b-4833-a6cd-f596e467d604" />



<img width="287" height="85" alt="image" src="https://github.com/user-attachments/assets/f53e6367-7064-4bde-bf40-30900ccd8244" />



<img width="365" height="207" alt="image" src="https://github.com/user-attachments/assets/6bb76124-a1c7-4a06-ad18-cfb8ec42fbb9" />

<img width="493" height="238" alt="image" src="https://github.com/user-attachments/assets/7c6476e0-4650-40ce-97c5-d15c52576346" />

<img width="550" height="132" alt="image" src="https://github.com/user-attachments/assets/3283a90e-4da3-43ae-b1a4-59778ba1339f" />
<img width="422" height="91" alt="image" src="https://github.com/user-attachments/assets/9a6ee5af-5a48-4edb-bdd5-f49d14bbcb09" />


since packet is arriving at D1, enabled ip routing at D1
**<ins>Ticket Key Points Review:</ins>**
    
- ~~Sales cannot ping HR or IT~~ Sales could not reach HR or IT because the end device default gateway Ip addresses and the SVI ip addresses did not match,

