# Project Notes and Correction Log

## Source Material

- Source PDF: `new.pdf`
- Source language: Hebrew
- Repository language: English
- Excel attachment: intentionally excluded from this repository

The original PDF was reorganized into a GitHub-ready portfolio repository. The README keeps the technical intent of the source project while improving terminology, structure, and professional wording.

## Translation Notes

The Hebrew source material was translated into professional English. Informal report-style wording was rewritten as technical documentation. The final README avoids school-assignment phrasing and presents the work as a structured Windows infrastructure lab.

## Image Handling Notes

The PDF contained 189 embedded image objects. The repository includes 147 selected evidence images.

Duplicate or low-value screenshots were excluded when they showed the same state repeatedly. Images were kept in the same technical order as the lab workflow so each paragraph has matching visual evidence.

Images were organized by topic:

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

## Technical Corrections Applied

### 1. Sensitive Lab Data

The screenshots intentionally retain lab credentials, domain names, usernames, and internal IP addresses because they are part of the project evidence and were approved for inclusion.

### 2. Excel File Excluded

`BatchUsersScript.xls` was not included because its visible internal content references a different lab context, including `msclass.net`, `NewUsers`, `NUG`, `MSuser01`, and `1234abcD`. Including it would create confusion with the main `samueldomain.com` project.

### 3. PowerShell User-Creation Loop

The source loop:

```powershell
for ($i = 50; $i -le 60; $i++)
```

creates 11 users: `user50` through `user60`.

The README documents the corrected 10-user version:

```powershell
for ($i = 50; $i -lt 60; $i++)
```

This creates `user50` through `user59`.

### 4. NAT/PAT Terminology

The source describes PAT as a protocol. The README corrects this to describe PAT as a NAT translation method that maps internal sessions through port translations.

### 5. DHCP Failover Terminology

The source refers to a DHCP failover cluster. The README uses the more accurate term `DHCP failover relationship`.

### 6. RDP Port Forwarding

The source demonstrates RDP access through NAT port forwarding. The README keeps the lab evidence but clearly marks this as lab-only. In production, direct RDP exposure should be replaced with VPN, RD Gateway, MFA, source-IP restrictions, and explicit firewall rules.

The source also contains a port mismatch between `5588` and `5589`. The README avoids relying on the conflicting value and documents the concept as a lab NAT forwarding rule.

### 7. Windows Firewall

The source screenshots show Windows Defender Firewall disabled in parts of the lab. The README keeps the evidence but notes that a production deployment should keep the firewall enabled and use explicit inbound rules.

### 8. User Account vs User Profile

The source explanation blends the concepts of an AD user account and a Windows profile. The README separates the two:

- AD user account: identity and authentication object.
- Windows user profile: user settings and data loaded during logon.

### 9. Everyone Permissions

The source uses `Everyone` permissions for selected lab shares. The README keeps this as lab evidence but notes that production file shares should use least-privilege AD groups.

### 10. Quota and File Screening

The source describes quota as an Active Directory control. The README corrects this to File Server Resource Manager and NTFS-based file-server management.

AD provides identity and group membership. NTFS ACLs, share permissions, FSRM quotas, and file screens enforce file access and storage controls.

### 11. Sales Password Policy

The source demonstrates a different password policy for Sales through GPO. The README explains that production Active Directory environments should normally use Fine-Grained Password Policies and Password Settings Objects for group-specific password policy behavior.

### 12. DNS CNAME vs A Record

The source task describes creating a CNAME record, but the screenshot evidence shows a `New Host` A record named `mail` pointing to `192.168.116.200`.

The README documents the demonstrated evidence as an A-record validation and notes that a true CNAME would point an alias to an existing canonical host name.

### 13. Terminology Cleanup

The following terminology was normalized:

- `Kerberus` -> `Kerberos`
- `Disk-on-key` -> `USB removable storage`
- `law` / generic rule wording -> policy, rule, ACL, or firewall rule depending on context
- `Failover cluster` in DHCP context -> DHCP failover relationship
- `Quota in Active Directory` -> FSRM quota / file-server quota

### 14. Built-in Administrator Explanation

The source states that the administrator account should be disabled because its SID is known. The README uses the more precise explanation: the built-in Administrator account has the well-known RID 500 and is commonly renamed or disabled after creating controlled administrative accounts.

This is documented as a cybersecurity hardening decision. RID 500 is predictable during enumeration, and default privileged accounts are commonly targeted during password attacks, account discovery, and post-compromise reconnaissance. In a production domain, this should be paired with named administrator accounts, least privilege, privileged access management, MFA, and auditing of privileged logons.

## Repository Scope

This repository is documentation-focused. It does not recreate the virtual machines or export live Windows configuration files. Its purpose is to present the lab workflow, configuration decisions, validation evidence, and security-relevant corrections in a clean GitHub portfolio format.
