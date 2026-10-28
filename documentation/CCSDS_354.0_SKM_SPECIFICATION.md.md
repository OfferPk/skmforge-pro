# CCSDS 354.0-M-1 Symmetric Key Management (SKM) Engineering Specification

## 1\. Executive Summary & Normative Baseline

- **Document Identifier**: CCSDS 354.0-M-1 (Magenta Book, Issue 1, Recommended Practice)  
- **Publication Date**: December 2023  
- **Title**: Symmetric Key Management  
- **Security Working Group**: Consultative Committee for Space Data Systems (CCSDS)  
- **Implementation**: SKMForge Pro (v1.0.0)

This document formalizes the architectural principles, state transitions, key hierarchy constraints, Over-The-Air-Rekeying (OTAR) mechanisms, and cryptographic zeroization mandates established by CCSDS 354.0-M-1 for space mission communication links.

---

## 2\. Key Hierarchy & Categorization (§3.1)

CCSDS 354.0-M-1 defines a strict two-category classification for symmetric cryptographic keys:

### 2.1 Master Keys (Tier 0\) (§3.1.2)

- **Classification**: Long-term root cryptographic keys stored in tamper-resistant, radiation-hardened cryptographic modules or secure non-volatile memory onboard the spacecraft.  
- **Single-Purpose Mandate (§3.1.2.2)**: A Master Key shall be assigned and restricted to exactly one authorized operational purpose throughout its lifecycle:  
  1. *OTAR Key Encryption Key (KEK)*: Exclusively wrapping and distributing Session Keys (§3.1.2.1.1).  
  2. *Onboard Crypto Unit Commanding*: Cryptographic authorization and authentication of sensitive cryptographic hardware commands (e.g., zeroize command, partition reconfiguration).  
  3. *Spacecraft Recovery Key*: Emergency fallback root key used to regain command authority during anomaly recovery.  
- **Strict Prohibition**: Under no circumstances shall a Master Key be utilized directly for protecting operational payload, telemetry, or telecommand mission traffic.

### 2.2 Session Keys (Tier 1..N) (§3.1.3)

- **Classification**: Ephemeral or mission-phase bounded keys generated dynamically or uplinked via OTAR.  
- **Tier Hierarchy**: Can be structured in multi-tier arrangements (Tier 1 Session Keys wrap Tier 2 Payload Sub-Keys, etc.).  
- **Single-Purpose Mandate (§3.1.3.3)**: A Session Key shall be assigned and restricted to exactly one authorized cryptographic purpose:  
  1. *Traffic Authentication*: Generation and verification of CMAC/GMAC message authentication codes on telecommands or telemetry frames.  
  2. *Traffic Encryption*: Confidentiality protection of telemetry transfer frames or payload streams.  
  3. *Authenticated Encryption with Associated Data (AEAD)*: Combined confidentiality and integrity protection using AES-GCM per CCSDS 352.0-B-2.  
  4. *Lower-Tier Key Encryption Key*: Wrapping lower-tier session keys for segmented multi-tenant payload instruments.

---

## 3\. Five-State Key Lifecycle Model (§3.2)

The lifecycle of every symmetric key entity conforms to the deterministic state machine defined in Section 3.2:

  \[ Generation \]

         │

         ▼

 ┌───────────────┐

 │ PRE-ACTIVATION│ ──(Activation Epoch reached)──► ┌──────────┐

 └───────────────┘                                 │  ACTIVE  │ ◄──┐

         │                                         └──────────┘    │

         │ (Abort/Purge)                               │  ▲        │ (Resume)

         │                                (Suspend)    │  │        │

         ▼                                             ▼  │        │

 ┌───────────────┐                                 ┌──────────┐    │

 │   DESTROYED   │ ◄──(Zeroization Command)─────── │SUSPENDED │ ───┘

 └───────────────┘                                 └──────────┘

         ▲                                             │

         │                                             │ (Decommission)

         │ (End-of-Life Zeroize)                       ▼

         └─────────────────────────────────────── ┌────────────┐

                                                   │DEACTIVATED │

                                                   └────────────┘

                                            (Read-Only Decryption Only)

### 3.1 PRE\_ACTIVATION (§3.2.2)

- Key entity has been generated or ingested into onboard volatile storage but the activation epoch has not been reached.  
- **Interlock**: Prohibited from encrypting, decrypting, or authenticating any mission traffic.

### 3.2 ACTIVE (§3.2.3)

- Key is operationally valid and within its defined cryptoperiod.  
- Permitted to perform its designated single purpose.  
- Usage metrics and frame counter states are continuously monitored against replay thresholds.

### 3.3 SUSPENDED (§3.2.4)

- Key is temporarily deactivated due to operational anomaly, orbital eclipse, safe-mode entry, or link handover.  
- Cryptographic operations are blocked while suspended.  
- Preserves key lifetime and counter states; can be resumed directly to `ACTIVE`.

### 3.4 DEACTIVATED (§3.2.5)

- Key has reached the expiration of its cryptoperiod or has been superseded by a newer key version.  
- **Normative Legacy Read-Only Mandate (§3.2.5.2.2)**:  
  - **STRICTLY PROHIBITED**: Encrypting new telemetry or generating authentication codes on outgoing telecommands.  
  - **PERMITTED**: Decrypting recorded telemetry playbacks or verifying historical audit logs recorded during the key's active window.

### 3.5 DESTROYED (§3.2.6)

- Terminal state of the key lifecycle.  
- **Cryptographic Zeroization (§3.2.6.2)**: Key material is overwritten in memory (all bits forced to zero or randomized per FIPS 140-3 Level 3/4 standards).  
- Keys in this state cannot transition to any other state under any circumstance.

### 3.6 Orthogonal Compromised Attribute (§3.2.7)

- Independent security flag that can be asserted in any operational state upon suspected physical compromise, side-channel leakage, or ground key breach.  
- Assertion immediately revokes all encryption capabilities.  
- Prevents active transmission using the compromised key while preserving auditability.

---

## 4\. Over-The-Air-Rekeying (OTAR) Protocol (§4.3)

OTAR enables secure, authenticated in-flight key deployment:

- **Wrapper Algorithm**: AES Key Wrap (RFC 3394 / NIST SP 800-38F) or AES-GCM (CCSDS 352.0-B-2).  
- **Integrity Protection**: 128-bit authentication tag ensuring tamper detection.  
- **Anti-Replay**: Monotonically increasing sequence counters and UTC timestamp validation.  
- **Atomic Activation**: Onboard key registry updates key atomically upon complete verification.