
![Microsoft Security Copilot](https://img.shields.io/badge/Microsoft-Security%20Copilot-blue)
![Latest Release](https://img.shields.io/github/v/release/marcopassanisi/breakglass-guardian-agent)

# BreakGlass Guardian Agent for Microsoft Security Copilot

**BreakGlass Guardian Agent** is a custom Microsoft Security Copilot agent designed to
audit Microsoft Entra ID Conditional Access policies and ensure that emergency
access (break-glass) accounts are correctly excluded and operationally usable,
preventing tenant lockout scenarios.

---

## 📋 Table of Contents

- [Purpose](#-purpose)
- [Key Features](#-key-features)
- [How it works](#-how-it-works)
- [Evaluation logic](#-evaluation-logic)
- [Requirements](#-requirements)
- [Safety & Guardrails](#-safety--guardrails)
- [Repository Structure](#-repository-structure)
- [Disclaimer](#-disclaimer)
- [Community & Contributions](#-community--contributions)

---

## 🎯 Purpose

Conditional Access is a critical security control in Microsoft Entra ID.
Misconfigurations or missing exclusions for emergency access accounts can result
in complete tenant lockout.

This agent continuously audits Conditional Access policies to ensure that:

✅ Each active policy excludes a minimum number of emergency (break-glass) accounts  
✅ Policies with no exclusions or insufficient exclusions are clearly identified  
✅ Policies with exclusions not recognizable as break-glass accounts are flagged  
✅ Break-glass accounts are not only configured, but also **operationally validated**  
✅ Lockout risks are surfaced early and consistently  

> **Note:** The agent follows Microsoft best practices for emergency access account
> management and operational readiness.

---

## ✨ Key Features

- 🔍 **Conditional Access exclusion audit**  
  Validates effective exclusions across users, groups, and directory roles.

- 🧑‍🚒 **Break-glass account detection**  
  Supports explicitly provided accounts or automatic discovery via naming
  conventions.

- 📊 **Break-glass sign-in activity validation**  
  Reviews authentication activity for emergency accounts over a configurable
  lookback period (default: **30 days**) to ensure accounts are usable and tested.

- 🧪 **Always-on audit mode**  
  Produces a complete audit even when no break-glass accounts are defined or
  discovered.

- 🧾 **Executive-ready output**  
  Generates clear summaries and tables suitable for security leadership,
  governance, and audit reviews.

- 🛠️ **Dry-run remediation guidance**  
  Suggests corrective actions without applying any automatic changes.

---

## 🧠 How it works

The agent performs a **read-only audit** using Microsoft Graph API:

1. **Retrieves** all Conditional Access policies
2. **Filters** optionally disabled policies
3. **Builds** the effective exclusion list by expanding:
   - Excluded users
   - Excluded groups (members)
   - Excluded directory roles (members)
4. **Resolves** all excluded identities to user principal names (UPNs)
5. **Determines** the break-glass reference list using:
   - Explicit input, or
   - Automatic discovery based on naming conventions
6. **Evaluates** policies using one of the following modes:
   - **Provided**: break-glass accounts explicitly supplied
   - **Discovered**: break-glass accounts inferred
   - **None**: no break-glass accounts identified
7. **Analyzes** sign-in activity for break-glass accounts over the last
   *N days* (default: 30)
8. **Produces** a full audit and **executive-style summary**
9. **Generates** dry-run remediation suggestions (never applied automatically)

---

## 🧪 Evaluation logic

A policy is classified as:

### ✅ Compliant
- At least the required number of break-glass accounts are excluded

### ⚠️ Non-Compliant
- Break-glass accounts are known but fewer than the required number are excluded

### 🔴 At-Risk
- No user exclusions are present, **OR**
- Exclusions exist but cannot be validated as break-glass accounts, **OR**
- No break-glass accounts are defined or discovered

Break-glass accounts are additionally evaluated for **operational readiness**
based on recent sign-in activity.

---

## 🛠 Requirements

### Platform Requirements
- **Microsoft Security Copilot** enabled
- **Microsoft Entra ID** tenant with Conditional Access policies

### Permissions
This agent runs in **read-only mode** and does not apply any configuration changes.

The user executing the agent must have sufficient Entra ID privileges to read:
- Conditional Access policies
- Users, groups, and directory roles

Recommended roles:
- **Security Administrator**, or
- **Global Administrator**

### Notes on Remediation
The agent may generate **suggested remediation payloads** (JSON examples)
to illustrate how missing break-glass exclusions could be added to Conditional
Access policies.

> ⚠️ These payloads are **never applied automatically** and are provided
> for review purposes only.


> ⚠️ **Important:** No write operations are executed automatically.

---

## 🔐 Safety & Guardrails

| Feature | Status |
|--------|--------|
| Read-only by default | ✅ |
| No automatic remediation | ✅ |
| No credentials or secrets stored | ✅ |
| Dry-run remediation only | ✅ |
| Production-safe design | ✅ |
| Explicit confirmation required | ✅ |

---

## 📁 Repository Structure

```
security-copilot-breakglass-guardian-agent/
│
├── agent/
│   ├── BreakGlassGuardianAgent.yaml    # Security Copilot agent manifest
│   └── icon.png                        # Agent icon (100x100)
│
├── prompts/
│   ├── builder-prompt.md               # Prompt used in the agent builder
│   └── test-prompts.md                 # Example prompts for validation
│
├── examples/
│   └── sample-output.json              # Example audit output
│
└── README.md                           # This file
```

---

## 📄 Disclaimer

> This project is provided **as-is** for educational and community purposes.
>
> ⚠️ **Always test in a non-production tenant** before relying on any security control or audit result.

---

## ⭐ Community & Contributions

Feedback, issues, and improvements are welcome.

This project supports the **Microsoft Security and Entra ID community**, with a focus on:
- 🔒 Security architecture
- 📋 Governance
- 🛡️ Operational resilience

