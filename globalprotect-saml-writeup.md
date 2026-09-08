# GlobalProtect Authentication Outage: SAML Assertion Mismatch

**Category:** Palo Alto Networks | GlobalProtect | SAML / Identity
**Role:** Network Security Engineer (L2/L3 Support)

## Problem

An enterprise customer reported that **GlobalProtect (GP) was down network-wide** — no users could establish a VPN connection through the portal/gateway. Since this affected all remote users at once (not an isolated case), it pointed to an authentication or configuration-level failure rather than a client-side issue.

## Environment

- Palo Alto NGFW with GlobalProtect Portal & Gateway
- SAML-based authentication (SSO) integrated with the customer's IDP
- Remote workforce relying on GP for VPN access

## Investigation

1. **Initial triage** — Gathered context from the customer: when the issue started, whether it affected all users or a subset, and any recent changes to the environment.
2. **GlobalProtect logs** — Reviewed GP portal and gateway logs to identify where in the connection flow the failure was occurring (portal reachability vs. authentication vs. tunnel establishment).
3. **SSL/Auth logs** — Checked SSL decryption/manager logs to rule out certificate-related handshake failures.
4. **SAML authentication logs** — Traced the SAML flow specifically, since the failure pattern (all users, all at once) suggested an assertion/IDP-level break rather than a network path issue.
5. Walked through each step of the GlobalProtect authentication process (portal request → IDP redirect → SAML assertion → gateway validation) to isolate exactly where it was breaking.

## Root Cause

The **SAML assertion was failing between the IDP and the firewall**. Comparing the SAML configuration on both ends (the firewall's SAML settings and the customer's IDP-side configuration) revealed a **mismatch in configuration values** between the two — the settings on the firewall side did not exactly match what was configured on the customer's IDP.

## Resolution

- Identified the specific discrepancy between the firewall's SAML config and the IDP config.
- Worked with the customer to **decode and align the SAML configuration on both ends** (firewall and IDP).
- Once both sides were synced, SAML assertions began validating correctly and GlobalProtect authentication was restored for all users.

## Outcome

- Full GlobalProtect connectivity restored for the entire customer network.
- Root cause documented as a config-sync issue between IDP and firewall SAML settings, rather than a product/platform bug — helping the customer prevent recurrence during future IDP changes.

## Key Takeaway

When an authentication failure hits **all users simultaneously**, it's rarely a client or network-path problem — it's almost always a config or identity-provider-level issue. Isolating GP failures by tracing the exact stage (portal → IDP redirect → SAML assertion → gateway validation) rather than jumping straight to logs saves significant troubleshooting time.
