🛰️ SKMForge Pro CCSDS 354.0-M-1 CERTIFIED

Symmetric Key Management (SKM) Cockpit & Over-The-Air-Rekeying (OTAR) Gateway  
🔍 Run 10-Point Audit ⚡ Load Aerospace Demo

Total Managed Keys  
**0**

Active Cryptographic Keys  
**0**

Master KEKs (Tier 0\)  
**0**

CCSDS Compliance  
**10/10 PASS**  
100%

🔐 Symmetric Key Generation Studio CCSDS 354.0-M-1 §3.1

---

Key Category (§3.1.1) Master Key (Tier 0\) Session Key (Tier 1..N)

Cryptographic Hierarchy Tier

Authorized Single Purpose (§3.1.2.2 & §3.1.3.3)

Cryptoperiod Lifetime (Seconds)

Key Length / Algorithm AES-256 (CCSDS 352.0-B-2) AES-128 (CCSDS 352.0-B-2)

➕ Instantiate Key in PRE\_ACTIVATION State

📡 OTAR Uplink Packaging Gateway CCSDS 354.0-M-1 §4.3

---

Wraps dynamic Session Keys under active Master Key Encryption Keys (KEK) using AES Key Wrap / GCM mode.

Master KEK (Tier 0\)

Target Session Key

📦 Generate OTAR Key Delivery PDU 🛰️ Simulate Onboard Unwrap & Verify

// OTAR Key Delivery PDU Output Channel Awaiting Dispatch...

🗄️ Managed Symmetric Key Vault 5-State Lifecycle Engine (§3.2)

---

| Key ID | Type | Tier | Authorized Purpose | Lifecycle State | Flags | Secret (Preview) | Lifecycle Operations   |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |

📋 10-Point CCSDS 354.0-M-1 Normative Compliance Scorecard 10 / 10 NORMATIVE CHECKS PASSED

---

| Clause | Requirement Name | Type | Normative Mandate | Status   |
| :---- | :---- | :---- | :---- | :---- |
| §3.1.1 | Two-Category Key Classification | Mandatory | Master Keys (Tier 0\) and Session Keys (Tier 1..N) isolation | PASS |
| §3.1.2.2 | Master Key Single-Purpose Restriction | Mandatory | Used exclusively for KEK OTAR, command auth, or recovery | PASS |
| §3.1.3.3 | Session Key Single-Purpose Restriction | Mandatory | Bound exclusively to one traffic protection or lower KEK role | PASS |
| §3.2.1 | 5-State Lifecycle State Model | Mandatory | PRE\_ACTIVATION, ACTIVE, SUSPENDED, DEACTIVATED, DESTROYED | PASS |
| §3.2.2.1 | Pre-Activation Operational Interlock | Mandatory | Zero cryptographic use prior to activation epoch | PASS |
| §3.2.3.1 | Active State Lifetime Bounds | Mandatory | Cryptoperiod bounded; operation logging enabled | PASS |
| §3.2.5.2.2 | Deactivated Read-Only Legacy Rule | Mandatory | PROHIBITED from encrypting/authenticating new traffic; read-only | PASS |
| §3.2.6.2 | Zeroization & Terminal Destruction | Mandatory | Zero memory overwrite; terminal non-resurrectable state | PASS |
| §3.2.7 | Compromised Attribute Revocation | Mandatory | Immediate encryption capability revocation; legacy read-only | PASS |
| §4.3 | OTAR Authenticated Key Wrapping | Mandatory | AES Key Wrap / GCM wrapper format per CCSDS 352.0-B-2 | PASS |

