# Implementation Summary

## Project Overview

This project implements a Zero Trust access control model using Microsoft Entra ID, Microsoft Intune, Conditional Access, and Identity Protection.

The environment represents a fictional organization, Northstar Identity, that needs to improve cloud access security by validating user identity, device posture, and risk signals before allowing access to Microsoft 365 resources.

## Business Objective

The objective was to move beyond password-based access and enforce access decisions using layered security controls.

The implementation answers the following question:

> If a user signs in with valid credentials, should they still be trusted?

In a Zero Trust model, the answer depends on additional signals such as MFA status, device compliance, user risk, and authentication behavior.

## Implementation Scope

The project included:

- Microsoft 365 tenant setup
- Azure subscription setup
- Test user and security group creation
- Break-glass account planning
- Security Defaults migration
- Conditional Access policy design
- Intune device enrollment
- Windows compliance policy creation
- Azure VM deployment for endpoint testing
- Conditional Access enforcement validation
- Risk-based access policy configuration
- Sign-in log analysis

## Major Phases

### Phase 0: Tenant and Identity Foundation

Created the Microsoft cloud environment, users, groups, and administrative accounts required to simulate a production-like identity structure.

### Phase 1: Security Defaults Migration

Disabled Security Defaults to allow custom Conditional Access policies to be designed and tested.

### Phase 2: Conditional Access Framework

Created a layered Conditional Access policy framework covering MFA, privileged account protection, compliant device requirements, and risk-based authentication.

### Phase 3: Intune Device Compliance

Enrolled a Windows VM into Intune and created a compliance baseline requiring endpoint security controls such as TPM, Secure Boot, Firewall, and Antivirus.

### Phase 4: Enforcement Testing and Validation

Tested policy behavior using real sign-in attempts, intentional non-compliance, and Entra sign-in logs.

## Final Security Outcome

After implementation, the environment enforced access using a layered model:

- Users are required to satisfy MFA requirements
- Privileged accounts are protected with dedicated controls
- Microsoft 365 access can be restricted to compliant devices
- Non-compliant devices can be blocked even when credentials are valid
- Legacy authentication is blocked
- Risk-based policies evaluate sign-in behavior
- Break-glass accounts remain available for emergency access

## Key Result

The project successfully demonstrated that access decisions can be based on identity, device trust, and risk rather than credentials alone.
