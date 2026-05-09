# 🔐 Azure Zero Trust Conditional Access Implementation

### Designing, implementing, and validating a layered Zero Trust access control model using Microsoft Entra ID, Intune, and Conditional Access — built from a clean tenant foundation with production-minded rollout methodology.

> This project is the fourth entry in a connected Microsoft cloud portfolio series covering identity governance, endpoint management, security monitoring, and access control hardening.

---

## Overview

This project builds the access control and identity security layer on top of the same cloud-first organizational model established across Projects 1 through 3. Rather than configuring policies in isolation, the implementation starts from a clean Microsoft 365 tenant and constructs a realistic enterprise identity structure before any security controls are applied.

The environment is built around **Northstar Identity** — a fictional professional services company using Microsoft 365 Business Premium. The business question:

> *If something goes wrong — account compromise, unauthorized device access, suspicious login activity — does this organization have controls in place to prevent damage, detect it, and prove those controls were enforced?*

---

## Key Outcomes

- Built a complete Microsoft 365 and Azure tenant from scratch under a single unified organizational identity
- Designed an enterprise identity structure with security groups, admin separation, break-glass architecture, and department-level segmentation
- Transitioned tenant security posture from Microsoft Security Defaults to a custom Conditional Access framework using staged rollout methodology
- Implemented four Conditional Access policies covering MFA, privileged account protection, device compliance, and risk-based authentication
- Enrolled an Azure-hosted Windows 11 VM (`NSI-WKS-001`) into Microsoft Intune and validated compliance enforcement end-to-end
- Demonstrated real access blocking for non-compliant devices and confirmed enforcement through sign-in logs
- Identified and corrected a critical policy misconfiguration where OR grant control logic allowed compliant-device bypass when MFA succeeded
- Troubleshot real-world issues including cached token persistence, device identity correlation, TPM configuration, browser/session context, and Security Defaults migration timing

---

## Tenant Architecture

```text
Northstar Identity — northstarsystemidentity.onmicrosoft.com
Microsoft 365 Business Premium + Entra ID P2 + Azure Subscription

Identity Structure
├── emmanueljohnson        (Tenant owner)
├── ejohnson.admin         (Global Admin — daily operations)
└── emergency.admin        (Break-glass — excluded from all CA)

Department Users
├── olivia.carter          (HR)
├── james.wilson           (Finance)
├── david.lee              (Executive)
├── maya.thomas            (Operations)
└── taylor.reed            (Contractor — separate governance)

Security Groups
├── SG-All-Employees               SG-Privileged-Admins
├── SG-CA-Pilot-Users              SG-CA-Excluded-BreakGlass
├── SG-Executives                  SG-Contractors
├── SG-HR                          SG-Finance
```

![Zero Trust Access Flow](assets/zero-trust-access-flow.png)

---

## Conditional Access Policy Framework

All policies follow the naming convention: `CA-[Number]-[Action]-[Scope]`

All custom policies were validated in report-only mode before enforcement was enabled.

| Policy | State | Purpose |
|--------|-------|---------|
| CA-001-Require-MFA-Pilot-Users | Report-only | Validate MFA enforcement before broader rollout |
| CA-002-Require-MFA-Privileged-Admins | Report-only | Dedicated MFA validation for admin accounts |
| CA-003-Require-Compliant-Device-O365 | **Enabled** | Require Intune-compliant device for Microsoft 365 access |
| CA-004-Risk-Based-MFA | Report-only | Require MFA when sign-in risk is Medium or High |
| Microsoft-Managed Baseline Policies | **Enabled** | MFA for admins, MFA for Azure management, and legacy authentication blocking |

**Rollout Philosophy:** Pilot first → Report-only validation → Break-glass exclusion → Sign-in log review → Controlled enforcement.

**Critical Finding — CA-003:** Grant controls must require ALL selected controls, not just one. When configured as “one of,” MFA success alone can grant access even when device compliance fails — a common misconfiguration that undermines Zero Trust enforcement.

---

## Intune Compliance Baseline

A Windows compliance policy (`CP-Windows-Baseline`) was deployed to `NSI-WKS-001`.

| Control | Requirement |
|---------|------------|
| Firewall | Enabled |
| Antivirus | Enabled |
| TPM | Present and enabled |
| Secure Boot | Enabled |
| BitLocker | Evaluated |

**TPM Issue:** After initial enrollment, TPM and Secure Boot showed `NotApplicable`. Root cause: the Azure VM required Trusted Launch / vTPM to be enabled at the hardware level. This was resolved by deallocating the VM, enabling the required security configuration, and re-syncing Intune.

**Device Trust Issue:** Initial sign-in tests showed the device context inconsistently in Conditional Access evaluation. This reinforced that browser/session state, cached authentication tokens, and device registration timing can affect whether Entra ID properly correlates a sign-in to an enrolled compliant device. Testing was completed using Firefox after forcing fresh sessions and allowing policy/device state to update.

---

## Testing & Validation

### ✅ Scenario 1 — Compliant Device Access

Device compliant, MFA registered, and browser session refreshed.

**Result:** Access granted — CA-003 evaluated successfully and grant controls were satisfied.

---

### ❌ Scenario 2 — Non-Compliant Device Block

TPM disabled on `NSI-WKS-001`, Intune sync triggered, and device marked non-compliant.

**Result:** Access blocked — CA-003 triggered, the `Require compliant device` grant control failed, and the result was confirmed in sign-in logs.

---

### 🔍 Scenario 3 — Policy Logic Validation

**Finding:** OR grant control logic allowed access when MFA succeeded despite device non-compliance.

**Fix:** Updated CA-003 to require ALL controls. This distinction — AND vs OR — is one of the most impactful and frequently misconfigured settings in Conditional Access.

---

### 🧠 Scenario 4 — Risk-Based Authentication

CA-004 was configured for medium and high sign-in risk, MFA requirement, and report-only mode.

**Result:** Policy logic was validated through sign-in telemetry and Identity Protection review.

**Note:** Real-time risk simulation is limited in lab environments, so validation focused on report-only evaluation, configuration review, and telemetry analysis.

---

## Final Security Posture

After full implementation, Northstar Identity operates under a layered Zero Trust access model.

| Control Layer | Mechanism | State |
|--------------|-----------|-------|
| Identity | MFA through Microsoft-managed baseline policies and custom CA validation | Enforced / Validated |
| Privileged Accounts | Dedicated admin MFA Conditional Access policy | Report-only |
| Device Trust | Intune compliant device requirement for Microsoft 365 access | **Enforced** |
| Legacy Authentication | Microsoft-managed legacy authentication blocking | **Enforced** |
| Risk-Based Access | Medium and high risk sign-in evaluation | Report-only |
| Emergency Access | Break-glass exclusion | Maintained |

Access decisions are no longer based solely on credentials. They are evaluated dynamically using identity, device posture, and behavioral signals — aligning with modern Zero Trust principles.

---

## Key Lessons Learned

- **Security Defaults must be disabled before relying on custom Conditional Access policy enforcement.** Custom CA policies showed `Not Applied` until the tenant was migrated away from Security Defaults.
- **Cached tokens persist after policy changes.** Fresh authentication sessions were required to validate new policy behavior after disabling Security Defaults.
- **Azure VM TPM requires Trusted Launch / vTPM configuration.** Intune compliance policies requiring TPM may show `NotApplicable` if the VM security configuration does not expose the required virtual hardware.
- **Browser and session context affect device recognition.** Cached sessions, browser state, and device registration timing can affect whether Entra ID correlates a sign-in to a managed compliant device.
- **OR vs AND grant logic is a critical distinction.** Grant control configuration must be reviewed carefully before enabling enforcement.
- **Risk-based Conditional Access is powerful but difficult to simulate in a lab.** Report-only evaluation and sign-in telemetry were used to validate the intended behavior.

---

## Supporting Documentation

- [Implementation Summary](docs/implementation-summary.md)
- [Conditional Access Policy Summary](docs/conditional-access-policy-summary.md)
- [Intune Compliance Summary](docs/intune-compliance-summary.md)
- [Testing and Validation](docs/testing-and-validation.md)
- [Lessons Learned](docs/lessons-learned.md)

---

## Evidence

### Conditional Access Policy Inventory
![CA Policies](evidence/phase2-conditional-access-policies/01-conditional-access-policy-inventory.png)

### Intune Managed Device
![Intune Device](evidence/phase3-intune-compliance/03-intune-device-compliant.png)

### Compliance Policy Baseline
![Compliance Baseline](evidence/phase3-intune-compliance/05-compliance-policy-device-health.png)

### Non-Compliant Device
![Non-Compliant Device](evidence/phase4-testing-validation/01-device-noncompliant.png)

### CA-003 Grant Control Failure
![Grant Control Failure](evidence/phase4-testing-validation/05-ca003-grant-control-not-satisfied.png)

### Identity Protection Dashboard
![Identity Protection](evidence/phase4-testing-validation/07-identity-protection-dashboard.png)

---

## Portfolio Connection

| Project | Focus | Key Technologies |
|---------|-------|-----------------|
| 1 | Identity Governance | Entra ID, Graph PowerShell |
| 2 | Endpoint Management | Intune, Azure VMs, Compliance |
| 3 | Security Monitoring | Sentinel, KQL, Logic Apps |
| 4 | Zero Trust Access Control | Conditional Access, Identity Protection |

[Project 1](https://github.com/EJCyber/azure-identity-governance) · [Project 2](https://github.com/EJCyber/azure-cloud-endpoint-management) · [Project 3](https://github.com/EJCyber/Azure-Cloud-Security-Monitoring-and-Threat-Visibility)

---

## Author

**Emmanuel Johnson**  
Systems & Cloud Administrator | Microsoft 365 | Entra ID | Identity & Endpoint Security  
📎 [LinkedIn](https://linkedin.com/in/emmanuel-a-johnson) | 💻 [GitHub](https://github.com/EJCyber)
