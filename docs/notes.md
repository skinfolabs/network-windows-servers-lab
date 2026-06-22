# Technical and Production Notes

## Repository Scope

This repository documents the Windows Server lab workflow, configuration decisions, commands, and validation results. It does not include virtual-machine exports or live server configuration backups.

## Lab Data

The screenshots retain the lab domain, internal IP addresses, test usernames, and lab passwords used during configuration. These values belong to the isolated training environment and must not be reused as production credentials.

## Image Organization

The repository contains 143 screenshots organized in technical order across these topics:

- Topology
- Lab environment
- Active Directory
- NAT/RRAS routing
- DHCP
- Remote management
- DNS
- Roaming profiles
- File server
- Group Policy hardening
- Password policy

Screenshots are preserved by default, organized by chapter, and placed near the relevant explanation whenever they support the guided walkthrough. Each screenshot keeps a stable sequence number and a caption explaining what is visible and what action or result it documents. A screenshot is removed only after explicit review and approval from the project owner.

## Administrative Automation

### PowerShell bulk user creation

The documented loop creates exactly ten accounts, from `user50` through `user59`:

```powershell
for ($i = 50; $i -lt 60; $i++)
```

PowerShell is the preferred automation method for repeatable Active Directory administration. Initial passwords should be protected, changed at first logon, and never embedded in production scripts.

### DSADD

`dsadd` is retained as a command-line administration exercise. It demonstrates distinguished names and scripted object creation, but modern administration should normally use the Active Directory PowerShell module.

## Networking and Remote Access

### NAT and PAT

PAT is a NAT translation method that allows multiple internal sessions to share an external address by tracking transport-layer ports. It is not a standalone network protocol.

### DHCP failover

The two DHCP servers use a DHCP failover relationship. This provides lease-state synchronization and service continuity without describing the configuration as a general-purpose failover cluster.

### RDP forwarding

The RDP connection test is initiated from the physical host and reaches the lab through SAMNAT's WAN interface. Direct RDP port forwarding is suitable only for this isolated demonstration. Production remote access should use VPN or RD Gateway, MFA, source-IP restrictions, firewall rules, and privileged-access monitoring.

### Windows Firewall

Windows Defender Firewall should remain enabled in production. Required services should be permitted with explicit inbound rules instead of disabling host protection.

## Identity and User Profiles

An Active Directory user account provides identity and authentication. A Windows user profile contains the user's desktop environment, registry settings, and profile data loaded during sign-in.

Mandatory profiles use `NTUSER.MAN` so profile changes are discarded at sign-out. They are useful for shared or controlled workstations but should be combined with appropriate file storage and user-data policies.

## File Services

Active Directory provides identities and group membership, while NTFS ACLs and share permissions enforce file access. FSRM provides the storage quota used in this lab.

Broad `Everyone` permissions are used only in isolated lab demonstrations. Production shares should assign access through least-privilege Active Directory security groups.

## Group Policy

Computer Configuration settings apply according to the location and scope of computer accounts. A Restricted Groups policy intended for workstations should therefore be linked to an OU containing those workstations, or scoped through an appropriate domain link and security filtering. Linking it only to an OU containing administrator users does not apply the computer-side setting to client computers.

The Notepad++ package is assigned through GPO from the network-accessible path `\\samdc2\Software\Notepad++.msi`. The Group Policy Management Editor screenshot records the assigned MSI and its UNC source, while the Windows 10 client screenshot shows Notepad++ available on `SAMWINPC1` after deployment. Together they document the server-side configuration and the resulting client installation.

## DNS

### Mail record

The `mail.samueldomain.com` entry is a Host (`A`) record mapped directly to `192.168.116.200`. A CNAME would instead map the `mail` alias to a canonical hostname such as `samdc1.samueldomain.com`.

### Conditional forwarder

The `google.com` conditional forwarder demonstrates domain-specific forwarding. Production conditional forwarders are commonly used for private partner namespaces, trusted networks, or separate Active Directory forests.

### Stub zone

The `yahoo.com` stub zone stores SOA, NS, and required glue records so the DNS server can locate the zone's authoritative name servers without holding a complete copy of the zone.

### Round robin

DNS round robin rotates the order of multiple addresses registered under one hostname. It can provide basic distribution but does not perform health checks and should not be treated as a full load-balancing solution.

## Password and Privileged Account Controls

The domain password baseline is defined in the `PassPolicy` GPO and linked at the `SamuelDomain.com` domain level. Password history, minimum length, complexity, and age requirements reduce common credential risks, but production policy should also follow current organizational and platform guidance. Long password phrases, compromised-password screening, MFA, account lockout protection, and monitoring remain important complementary controls.

The lab uses a 30-day minimum password age to demonstrate history-bypass prevention. This is restrictive for normal operations, so a production value should be selected carefully to avoid preventing legitimate password changes after user concern or help-desk intervention.

The built-in Administrator account has the well-known RID 500 and is commonly targeted during account discovery and password attacks. Disabling or renaming it after creating controlled named administrator accounts reduces predictable exposure. Production protection should also include least privilege, MFA, privileged-access management, and logon auditing.
