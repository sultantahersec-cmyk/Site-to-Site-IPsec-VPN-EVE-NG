# Site-to-Site IPsec VPN with Cisco Routers & Ubuntu DMZ Server

## 📌 Project Overview
A fully functional **Site-to-Site IPsec VPN** network environment built using the **EVE-NG** simulator. The setup establishes a secure encrypted tunnel between Headquarter Firewall (`HQ-FW`) and a remote branch (`Branch2-Router`), allowing secure communication across an untrusted ISP infrastructure. A dedicated **DMZ Zone** hosting an **Ubuntu Linux Server** is implemented and fully integrated into the VPN routing path.

---

## 📐 Network Architecture & IP Schema
### Network Topology Diagram
<img width="924" height="391" alt="image" src="https://github.com/user-attachments/assets/6759056b-9ed9-41b9-975c-a5f9e8f1a53e" />

| Device Name | Role | Interface | IP Address / Subnet |
| :--- | :--- | :--- | :--- |
| **HQ-FW** | Core Firewall / Router | `Gi0/0` (WAN) <br> `Gi0/1` (LAN) <br> `Gi0/2` (DMZ) | `100.64.1.2/30` <br> `192.168.1.1/24` <br> `172.16.10.1/24` |
| **ISP-Router** | Service Provider | `Gi0/0` <br> `Gi0/1` | `100.64.1.1/30` <br> `100.64.2.1/30` |
| **Branch2-Router** | Remote Branch Router | `Gi0/0` (WAN) <br> `Gi0/1` (LAN) | `100.64.2.2/30` <br> `10.20.1.1/24` |
| **DMZ-Server** | Linux Server | `ens3` | `172.16.10.10/24` |
| **BR2-PC-STAFF01** | Branch Host | `eth0` | `10.20.1.10/24` |

---

## 🔐 Cryptographic Specifications
* **IKE Phase 1 (ISAKMP Policy):**
  * Encryption: `AES-256`
  * Hash: `SHA-256`
  * Diffie-Hellman: `Group 14 (2048-bit)`
  * Authentication: `Pre-Shared Key (PSK)`
* **IKE Phase 2 (IPsec Transform-Set):**
  * Encryption: `ESP-AES 256`
  * Integrity: `ESP-SHA256-HMAC`

---

## ⚙️ Access Control List (ACL) Logic
To allow bidirectional traffic through the IPsec tunnel, symmetric extended ACLs were configured:

* **`HQ-FW` VPN Access-List:**
```text
permit ip 192.168.1.0 0.0.0.255 10.20.0.0 0.255.255.255
permit ip 172.16.10.0 0.0.0.255 10.20.0.0 0.255.255.255
```
  * **`Branch2-Router` VPN Access-List:**
  ```text
  permit ip 10.20.0.0 0.255.255.255 192.168.1.0 0.0.0.255
  permit ip 10.20.0.0 0.255.255.255 172.16.10.0 0.0.0.255
```

## ✅Verification & Results
 Phase 1 Status: Confirmed active ISAKMP Security Association (⁠QM_IDLE⁠).
 
   In `HQ-FW & Branch2-Router`  
   <img width="715" height="296" alt="image" src="https://github.com/user-attachments/assets/4cc4f6a0-12a4-4432-a306-cffa4b84f670" />
   <img width="627" height="262" alt="image" src="https://github.com/user-attachments/assets/615e6d6b-ffd8-4093-b9b5-cd0fcea3955e" />

Phase 2 Status: Verified encrypted/decrypted IPsec packet counters.

HQ-FW
<img width="822" height="232" alt="image" src="https://github.com/user-attachments/assets/ad408b22-ebae-40c2-b490-068c41dd6978" />
<img width="812" height="246" alt="image" src="https://github.com/user-attachments/assets/e92d63d0-ebe7-49d7-8657-8ea73f44b16a" />

Branch2-Router
<img width="825" height="477" alt="image" src="https://github.com/user-attachments/assets/993b6188-99ef-46df-80d3-0bc31607c951" />
<img width="805" height="240" alt="image" src="https://github.com/user-attachments/assets/7edcd697-1325-4ccb-b4da-ccee1fd542bd" />


3-Connectivity Verification:
   
 Ping test from Branch Host (⁠10.20.1.10⁠) to HQ Internal PC (⁠192.168.1.10⁠) -> 100% Success.
<img width="716" height="482" alt="image" src="https://github.com/user-attachments/assets/2f601856-daae-4e4a-900e-66157fa97785" />

 Ping test from Branch Host (⁠10.20.1.10⁠) to DMZ Server (⁠172.16.10.10⁠) -> 100% Succes
 <img width="727" height="305" alt="image" src="https://github.com/user-attachments/assets/66924480-0b8f-4a22-a1e9-c8630aec0bf4" />
