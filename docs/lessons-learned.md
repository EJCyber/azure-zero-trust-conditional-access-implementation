# Lessons Learned

## Overview

This project reinforced that building a secure Microsoft cloud environment is not just about creating Conditional Access policies. The real value comes from understanding how identity, device compliance, policy logic, user sessions, and sign-in telemetry work together.

The most important lesson was that security controls must be validated through real testing. A policy can look correct on paper, but the only way to confirm enforcement is to test it, review the logs, and verify the result.

---

## 1. Report-Only Mode Is Critical

Conditional Access policies should be tested in report-only mode before they are enforced.

Report-only mode allowed policy behavior to be validated without immediately blocking users. This is important in a real business environment because a misconfigured Conditional Access policy can lock out users, administrators, or critical workflows.

Testing in report-only mode helped confirm:

- Which users were targeted
- Which resources were affected
- Which grant controls were evaluated
- Whether the expected result matched the actual result

This approach supports a controlled rollout instead of making risky tenant-wide changes.

---

## 2. Break-Glass Accounts Are Non-Negotiable

A break-glass account is essential when implementing Conditional Access.

If every administrator is subject to the same policies and one policy is misconfigured, the organization could lose administrative access to the tenant.

For this project, a dedicated break-glass exclusion group was created so emergency access could be preserved.

A proper break-glass account should be:

- Cloud-only
- Excluded from Conditional Access policies
- Protected with a strong password
- Monitored closely
- Used only during emergencies

This is a critical part of production-ready identity design.

---

## 3. Security Defaults and Conditional Access Serve Different Purposes

Security Defaults provides baseline protection, but it does not offer the same level of control as custom Conditional Access policies.

To build a more realistic Zero Trust access model, Security Defaults had to be disabled and replaced with targeted Conditional Access policies.

This made it possible to design policies around:

- Pilot users
- Privileged administrators
- Device compliance
- Microsoft 365 access
- Risk-based authentication
- Break-glass exclusions

The lesson is that Security Defaults can be useful for simple environments, but Conditional Access is better suited for organizations that need more granular policy control.

---

## 4. Device Compliance Is More Than Device Enrollment

A device being enrolled in Microsoft Intune does not automatically mean the device should be trusted.

The device must meet compliance requirements before it can be used as a trusted endpoint.

In this project, the Windows VM had to satisfy compliance checks such as:

- Firewall enabled
- Antivirus enabled
- TPM enabled
- Secure Boot enabled
- BitLocker evaluated
- Minimum device security posture validated

This reinforced that device trust depends on both enrollment and compliance.

---

## 5. Intune Policy Sync Takes Time

Intune compliance results do not always update immediately.

During testing, policy changes and device compliance status took time to appear correctly in the portal. Manual syncs helped, but there was still a delay before the final compliance state updated.

This is important in real environments because administrators should not assume a policy failed just because the result does not update instantly.

Validation requires patience and repeated checks across:

- The endpoint
- Intune device status
- Compliance policy results
- Entra sign-in logs

---

## 6. Virtual Machine Security Settings Matter

The Azure VM had to be configured properly before Intune could evaluate certain security requirements.

During testing, the VM needed TPM/security settings enabled to properly satisfy or fail the compliance baseline.

This was an important reminder that endpoint compliance depends on the underlying hardware or virtual hardware configuration.

If the VM does not expose the right security features, Intune may return unexpected compliance results.

---

## 7. Conditional Access Grant Logic Must Be Precise

One of the most important lessons from this project was understanding how Conditional Access grant controls behave.

If a policy is configured to require only one selected control, access may be granted when MFA succeeds even if device compliance fails.

That matters because MFA alone does not prove the device is safe.

For stronger Zero Trust enforcement, policy logic must align with the actual security goal. If both MFA and compliant device trust are required, the policy must be configured so both controls are satisfied.

This was one of the most valuable troubleshooting lessons in the project.

---

## 8. Browser and Session Context Affect Device Recognition

Some sign-in attempts did not immediately show the expected device context.

In certain cases, Entra ID did not fully recognize the sign-in as coming from the enrolled device. This can happen because of:

- Browser profile state
- Cached sessions
- Token behavior
- Device registration timing
- Work profile configuration
- Sign-in method

Using the correct Microsoft Edge work profile helped ensure the session provided the expected device context.

This showed that Conditional Access testing is not only about the policy. The user session and browser context also matter.

---

## 9. Sign-In Logs Are the Source of Truth

Microsoft Entra sign-in logs were the most important validation tool in this project.

The logs showed:

- Which user signed in
- Which application was accessed
- Which Conditional Access policies applied
- Whether policies succeeded or failed
- Which grant controls were satisfied or not satisfied
- Whether access was allowed or blocked

Without sign-in log analysis, it would be difficult to prove whether the policies were working as intended.

The biggest validation moment was confirming that CA-003 blocked access because the compliant device requirement was not satisfied.

---

## 10. Risk-Based Policies Are Valuable but Hard to Simulate

Risk-based Conditional Access adds an adaptive layer of protection by responding to user risk and sign-in risk.

However, real-time user risk is difficult to simulate consistently in a lab tenant.

Because of this, the risk-based policy was configured in report-only mode and validated through policy review, Identity Protection, and sign-in telemetry.

The lesson is that risk-based access is powerful, but testing it in a lab requires understanding the limitations of simulated risk signals.

---

## 11. Cost and Performance Tradeoffs Matter

The Azure VM was intentionally kept basic to control cost, but the lower resource allocation made the VM slow at times.

This affected testing because the VM occasionally lagged or disconnected.

In a real implementation, performance matters because unreliable test devices can slow down validation and troubleshooting.

The tradeoff was acceptable for this project, but a larger VM size would be better for longer-term testing or production-style labs.

---

## 12. Zero Trust Is About Access Decisions, Not Just Authentication

The biggest takeaway from this project is that authentication alone is not enough.

A valid username, password, and MFA approval do not automatically mean access should be granted.

A stronger access model evaluates:

- Who the user is
- What resource they are accessing
- Whether MFA was completed
- Whether the device is compliant
- Whether the sign-in appears risky
- Whether the session provides trusted device context

This is the core idea behind Zero Trust: never trust automatically, always verify.

---

## Final Takeaway

This project helped connect Microsoft Entra ID, Conditional Access, Microsoft Intune, and Identity Protection into one complete access control model.

The most valuable part of the project was not simply creating the policies. It was validating the behavior, identifying misconfigurations, troubleshooting unexpected results, and proving that access could be blocked when a device failed compliance requirements.

That made the project feel closer to a real business implementation than a basic lab.
