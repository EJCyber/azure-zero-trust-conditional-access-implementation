# Testing and Validation

## Purpose

Testing was performed to confirm that Conditional Access policies behaved as expected before and after enforcement.

The goal was not just to configure policies, but to prove that the policies produced the correct access decisions.

This validation process focused on confirming that Microsoft Entra ID, Conditional Access, Microsoft Intune, and device compliance worked together to enforce a Zero Trust access model.

---

## Validation Method

Testing relied on the following tools and evidence sources:

- Microsoft Entra sign-in logs
- Conditional Access policy results
- Microsoft Intune device compliance status
- Controlled sign-in attempts
- Intentional device non-compliance
- Report-only policy evaluation
- Enforced Conditional Access testing

---

## Scenario 1: Report-Only Validation

### Objective

Confirm that Conditional Access policies evaluated correctly before enforcement.

### Test Conditions

- Conditional Access policies configured in report-only mode
- Pilot users assigned to test groups
- Break-glass account excluded from policy enforcement
- Sign-in activity generated through Microsoft 365 and Azure portals

### Expected Result

Policies should appear in sign-in logs as report-only results without blocking access.

### Result

Report-only evaluation confirmed that the policies were being evaluated successfully before enforcement.

### Why This Matters

Report-only mode allows administrators to validate policy impact before enabling enforcement. This reduces the risk of accidental lockouts or unexpected business disruption.

---

## Scenario 2: Compliant Device Access

### Objective

Confirm that an Intune-managed and compliant device can access Microsoft 365 resources.

### Test Conditions

- Device enrolled into Microsoft Intune
- Device marked compliant
- MFA completed successfully
- User attempted access to Microsoft 365 resources

### Expected Result

Access should be allowed.

### Result

Access was allowed. Sign-in logs confirmed successful Conditional Access evaluation.

### Validation Evidence

The sign-in logs showed that the device was recognized as compliant and the required access controls were satisfied.

---

## Scenario 3: Non-Compliant Device Block

### Objective

Confirm that a non-compliant device is blocked when the compliant-device Conditional Access policy is enabled.

### Test Conditions

- TPM disabled on the test VM
- Device synced with Intune
- Device marked non-compliant
- User attempted access to Microsoft 365 resources
- CA-003-Require-Compliant-Device-O365 enabled

### Expected Result

Access should be blocked.

### Result

Access was blocked.

### Validation Evidence

The sign-in logs showed:

- Policy: CA-003-Require-Compliant-Device-O365
- Policy state: Enabled
- Result: Failure
- Grant Controls: Not satisfied
- Control: Require compliant device

This confirmed that access was denied because the device did not meet compliance requirements.

---

## Scenario 4: Conditional Access Logic Validation

### Objective

Validate how Conditional Access grant control logic affects access decisions.

### Finding

During testing, the compliant-device policy initially allowed access when MFA succeeded, even though the device compliance requirement was not satisfied.

This occurred because the policy was configured to require one of the selected controls instead of requiring all selected controls.

### Impact

This configuration weakens enforcement because MFA alone can satisfy the policy, even when the device is non-compliant.

In a true Zero Trust model, both identity verification and device trust should be evaluated when both controls are required.

### Resolution

The policy logic was reviewed and corrected so the intended access requirements were enforced.

This reinforced the importance of validating Conditional Access grant control behavior before enabling policies in production.

---

## Scenario 5: Device Identity Correlation

### Objective

Confirm that Microsoft Entra ID could properly associate the sign-in session with the Intune-managed device.

### Issue Observed

Some sign-in attempts showed the device as unknown.

### Likely Cause

The browser session did not initially provide enough device context for Entra ID to associate the login with the enrolled device.

This can happen because of browser profile state, cached sessions, token behavior, or device registration propagation delays.

### Resolution

Testing was performed using Microsoft Edge signed into the work profile. This allowed the browser session to present the expected device context during authentication.

### Result

Conditional Access was able to evaluate device compliance correctly once the device context was properly recognized.

---

## Scenario 6: Risk-Based Authentication

### Objective

Create a risk-based Conditional Access policy that requires MFA when medium or high user risk is detected.

### Test Conditions

- User risk set to Medium and High
- Grant control set to require MFA
- Policy configured in report-only mode
- Multiple sign-in attempts tested
- Identity Protection dashboard reviewed
- Sign-in telemetry analyzed

### Expected Result

If Microsoft Entra Identity Protection detects elevated risk, the policy should require MFA.

### Result

The policy was configured successfully and reviewed through Identity Protection and sign-in telemetry.

### Limitation

Real-time risk generation is difficult to simulate consistently in a lab tenant. Because of this, validation focused on report-only evaluation, policy configuration, and sign-in telemetry review.

---

## Final Validation Outcome

The project confirmed that Microsoft Entra Conditional Access and Microsoft Intune can enforce access decisions based on:

- User identity
- MFA completion
- Device compliance
- Risk-based signals
- Conditional Access policy logic

The strongest validation result was the successful blocking of Microsoft 365 access from a non-compliant device after CA-003 was enabled.

---

## Key Takeaway

Testing proved that access control should not rely on credentials alone.

A valid username, password, and completed MFA challenge do not automatically mean access should be granted. Device compliance and risk signals provide additional context that strengthens access decisions and aligns with Zero Trust principles.
