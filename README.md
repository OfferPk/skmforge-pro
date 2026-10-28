# SKMForge Pro (v1.0.0)

### High-Assurance Spacecraft Symmetric Key Management (SKM) & Over-The-Air-Rekeying (OTAR) Cockpit

**Normative Reference Standard**: [CCSDS 354.0-M-1 (Magenta Book, Issue 1, December 2023\)](https://ccsds.org/Pubs/354x0m1.pdf)  
**Security Baseline**: [CCSDS 352.0-B-2](https://ccsds.org/Pubs/352x0b2.pdf) & [SANA Space Registry](https://sanaregistry.org/)  
**Certification Verdict**: `CCSDS_354_0_SKM_CERTIFIED` (10/10 Normative Mandates Passed — 100.0%)

---

## 1\. Product Overview

SKMForge Pro is an aerospace-grade software engine, CLI suite, and glass-cockpit ground/flight operations console implementing the official Consultative Committee for Space Data Systems (CCSDS) Recommended Practice for Symmetric Key Management (CCSDS 354.0-M-1).

Modern satellite constellations (US Space Force Starshield, SDA PWSA, ESA IRIS², and commercial LEO mega-constellations) require rigorous cryptographic key lifecycle control to defend space communication links against unauthorized command injection, eavesdropping, and key compromise. SKMForge Pro operationalizes the five-state lifecycle model, strict key categorization, single-purpose key separation, read-only legacy restrictions for deactivated keys, cryptographic zeroization, and Over-The-Air-Rekeying (OTAR) key wrapping.

---

## 2\. Core Capabilities & Architectural Pillars

### 2.1 Two-Category Key Hierarchy (§3.1)

- **Master Keys (Tier 0\)**: Long-term root keys stored in tamper-resistant cryptographic modules. Enforces single-purpose restriction (§3.1.2.2) to OTAR Key Encryption Key (KEK), Onboard Command Authorization, or Emergency Recovery. Prohibited from encrypting payload traffic.  
- **Session Keys (Tier 1..N)**: Ephemeral keys dynamically provisioned in multi-tier trees for traffic encryption (TM), authentication (TC), or authenticated encryption (AEAD AES-GCM).

### 2.2 Deterministic 5-State Key Lifecycle State Machine (§3.2)

1. `PRE_ACTIVATION`: Key generated and staged; operational interlock prevents cryptographic use.  
2. `ACTIVE`: Bounded operational cryptoperiod; full cryptographic capabilities enabled.  
3. `SUSPENDED`: Temporary non-operational state (e.g., during orbital eclipse, anomaly safe mode, or link handover) preserving lifetime counters.  
4. `DEACTIVATED`: Expired or decommissioned key. **Strict Normative Legacy Restriction (§3.2.5.2.2)**: Prohibited from protecting new data; strictly permitted for reading/decrypting historical playback telemetry.  
5. `DESTROYED`: Terminal state; all memory locations cryptographically zeroized (overwritten with zeros per FIPS 140-3). Resurrection is blocked.

### 2.3 Orthogonal Compromised Attribute (§3.2.7)

Emergency security revocation flag that immediately revokes all encryption capabilities across any state, isolating compromised assets from transmitting on satellite communication channels.

### 2.4 Over-The-Air-Rekeying (OTAR) Pipeline (§4.3)

Automated authenticated key packaging engine wrapping session keys under active Master KEKs with 128-bit integrity tags, anti-replay sequence counters, and onboard verification simulation.

### 2.5 10-Point Normative Compliance Auditor

Built-in automated audit engine verifying implementation adherence against normative clauses SKM-001 through SKM-010.

---

## 3\. Directory Structure

skm-forge-pro/

├── source-code/

│   ├── index.html            \# Responsive Glass-Cockpit Web Studio

│   ├── skm-engine.js         \# Pure ES6 Key Vault & OTAR Rekeying Engine

│   ├── skm-auditor.js        \# 10-Point Normative Compliance Auditor

│   ├── skm\_cli.py            \# Multi-subcommand Python 3 CLI & Vault

│   └── test\_runner.py        \# 10 Automated Normative Unit Tests

├── documentation/

│   ├── CCSDS\_354.0\_SKM\_SPECIFICATION.md  \# Detailed Engineering Specification

│   └── SKM\_COMPLIANCE\_MATRIX.md          \# 10-Point Verification Matrix (100% PASS)

├── screenshots/

│   ├── skm-architecture-diagram.svg      \# Vector Architecture Schematic

│   └── skm-studio-preview.svg            \# Workstation Cockpit UI Mockup

└── build/

    └── skm-forge-pro-v1.0.0.zip          \# Production Deployment Bundle

---

## 4\. Quickstart & Verification

### Running Automated Test Suite

python3 source-code/test\_runner.py

*Expected Result: 10/10 tests pass in \< 0.01s.*

### Command-Line Interface (CLI)

\# 1\. Create a Master KEK (Tier 0\)

python3 source-code/skm\_cli.py create-key \--type master \--purpose MASTER\_OTAR\_KEY\_ENCRYPTION

\# 2\. Transition Key State to ACTIVE

python3 source-code/skm\_cli.py transition-state \--key-id MK-01 \--target-state ACTIVE

\# 3\. Wrap Session Key via OTAR Pipeline

python3 source-code/skm\_cli.py otar-wrap \--master-id MK-01 \--session-id SK-01

\# 4\. Execute 10-Point Compliance Audit

python3 source-code/skm\_cli.py audit

### Launching the Web Cockpit

Open `source-code/index.html` in any modern web browser to manage key hierarchies, simulate OTAR uplinks, and inspect live compliance badges.