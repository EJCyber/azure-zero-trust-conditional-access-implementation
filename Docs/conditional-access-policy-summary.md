# Conditional Access Policy Summary

## Purpose

Conditional Access was used to enforce access decisions based on identity, device compliance, and risk signals.

Policies were first validated in report-only mode before enforcement to reduce the risk of accidental lockout or business disruption.

## Policy Inventory

| Policy Name | State | Purpose |
|---|---|---|
| CA-001-Require-MFA-Pilot-Users | Report-only | Validate MFA enforcement for pilot users |
| CA-002-Require-MFA-Privileged-Admins | Report-only | Protect privileged/admin accounts |
| CA-003-Require-Compliant-Device-O365 | Enabled | Require compliant device access for Microsoft 365 |
| CA-004-Risk-Based-MFA | Report-only | Evaluate risk-based authentication behavior |
| Microsoft-managed legacy authentication policy | Enabled | Block legacy authentication |
| Microsoft-managed MFA policies | Enabled | Provide baseline MFA protection |

## CA-001: Require MFA for Pilot Users

### Objective

Validate MFA enforcement against a limited pilot group before applying stronger authentication controls broadly.

### Target

- Included: Pilot user group
- Excluded: Break-glass account

### Control

- Require multifactor authentication

### Deployment Mode

- Report-only

### Reasoning

Using a pilot group allows policy behavior to be validated safely before expanding to all users.

## CA-002: Require MFA for Privileged Admins

### Objective

Apply stronger authentication expectations to privileged/admin users.

### Target

- Included: Privileged administrator group
- Excluded: Break-glass account

### Control

- Require multifactor authentication

### Deployment Mode

- Report-only

### Reasoning

Privileged users have elevated access and represent a higher-value target. Admin accounts should be protected separately from standard user accounts.

## CA-003: Require Compliant Device for Microsoft 365

### Objective

Restrict Microsoft 365 access to devices that meet Intune compliance requirements.

### Target

- Included: Microsoft 365 / Office 365 resources
- Excluded: Break-glass account

### Control

- Require compliant device

### Deployment Mode

- Enabled

### Reasoning

This policy enforces device trust. Even if a user has valid credentials and completes MFA, access should be blocked if the device does not meet security requirements.

## CA-004: Risk-Based MFA

### Objective

Require stronger authentication when user risk is detected.

### Target

- Included: All users
- Excluded: Break-glass account

### Conditions

- User risk: Medium and High

### Control

- Require multifactor authentication

### Deployment Mode

- Report-only

### Reasoning

Risk-based access allows authentication requirements to adjust dynamically based on sign-in behavior and identity risk.

## Break-Glass Account Strategy

A break-glass account was excluded from Conditional Access policies to prevent tenant lockout.

This account should be:

- Cloud-only
- Strongly protected
- Monitored
- Used only during emergencies
- Excluded from standard Conditional Access enforcement

## Important Configuration Lesson

Conditional Access grant controls must be configured carefully.

If a policy is configured to require only one selected control, access may succeed when MFA is satisfied even if device compliance fails.

For stricter Zero Trust enforcement, policies must require the correct control combination.
