# Intune Compliance Summary

## Purpose

Microsoft Intune was used to evaluate endpoint security posture and determine whether a device should be trusted for access to Microsoft 365 resources.

The goal was to enforce access based on device compliance, not just user identity. This supports a Zero Trust model where access decisions are based on both who the user is and whether the device meets security requirements.

---

## Test Device

A Windows VM named `NSI-WKS-001` was deployed and enrolled into Microsoft Intune.

The device was used to validate both compliant and non-compliant access scenarios.

---

## Compliance Policy

Policy name: `CP-Windows-Baseline`

---

## Compliance Requirements

The Windows compliance baseline evaluated the following controls:

- Firewall enabled
- Antivirus enabled
- TPM enabled
- Secure Boot enabled
- BitLocker evaluated
- Minimum Windows security posture validated

---

## Expected Behavior

If the device meets compliance requirements:

- Device Status: Compliant
- Access Result: Allowed

If the device does not meet compliance requirements:

- Device Status: Non-Compliant
- Access Result: Blocked

---

## Validation Steps

1. Enrolled the Windows VM into Intune
2. Confirmed the device appeared under Windows devices
3. Created the Windows compliance policy
4. Assigned the policy to the device/user scope
5. Synced the device
6. Verified the compliance state
7. Disabled TPM to simulate non-compliance
8. Synced the device again
9. Verified that the device became non-compliant
10. Tested access through Conditional Access

---

## Troubleshooting Notes

Intune compliance state may take time to update because of policy sync delays.

During testing, the VM had to be reconfigured to properly expose TPM and Secure Boot-related security posture to Intune.

This reinforced the importance of validating both the endpoint configuration and the policy evaluation results before relying on compliance status for access control decisions.

---

## Key Takeaway

Device compliance adds a critical security layer because valid credentials and successful MFA do not automatically mean the endpoint should be trusted.

By combining Intune compliance with Conditional Access, Microsoft 365 access can be restricted to devices that meet defined security requirements.
