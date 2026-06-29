# Windows Server Infrastructure Lab

![Category](https://img.shields.io/badge/Category-Windows%20Infrastructure-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202019-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Active%20Directory%20%7C%20DNS%20%7C%20DHCP%20%7C%20GPO-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Overview

This project documents a complete Windows Server domain environment built around identity, network services, remote administration, file services, and endpoint policy enforcement. The lab follows the same order an administrator would use in a real deployment: plan the topology, build the domain, add redundancy, configure network services, validate client access, and then apply security controls.

The main goal is to demonstrate how core Microsoft infrastructure components work together. Active Directory provides centralized identity, DNS allows domain and external name resolution, DHCP automates client addressing, RRAS provides lab routing/NAT, and Group Policy enforces workstation security settings. File services, roaming profiles, quotas, and software deployment are included to show how a domain environment supports daily user operations.

From a cybersecurity perspective, the lab focuses on the controls that matter most in a small Windows domain: reducing default-account exposure, using named administrative access, separating users into groups, validating least privilege on file shares, controlling endpoint behavior through GPO, and checking that the environment behaves as expected after each change.

## Objectives

- Build a Windows domain with two domain controllers.
- Promote DC1 into a new Active Directory forest and add DC2 as an additional domain controller.
- Configure AD users, groups, OUs, FSMO role management, and scripted account creation.
- Implement RRAS NAT/PAT routing for outbound lab connectivity.
- Deploy DHCP scopes, exclusions, lease duration, gateway/DNS options, and DHCP failover.
- Configure DNS forwarding, primary/secondary zones, stub zones, host records, and round robin validation.
- Enable remote administration through RDP for approved administrator groups.
- Configure roaming and mandatory profiles.
- Implement file-server shares, NTFS permissions, mapped drives, and quotas.
- Harden Windows clients with Group Policy controls.
- Configure and apply a domain-wide password policy baseline.

## Project Roadmap

| Section | What I configured |
|---------|-------------------|
| Lab planning | Topology, server roles, addressing, and VMware inventory |
| Identity services | AD forest creation, two domain controllers, users, groups, FSMO role handling, and replication checks |
| Network services | RRAS NAT, DHCP scope options, DHCP failover, DNS forwarding, zones, and round robin records |
| Remote access | RDP access for approved administrators and lab-only NAT forwarding validation |
| User environment | Roaming profiles, mandatory profiles, home folders, and mapped drives |
| File access | Share permissions, NTFS access, mapped drives, and FSRM quotas |
| Endpoint policy | GPO hardening, removable storage controls, local administrator assignment, and software deployment |
| Account security | Password policy controls and production-correct notes for group-specific password policy |

## Lab Environment

The environment is intentionally small but covers the main moving parts of a Windows domain. DC1 hosts the initial domain services, DC2 provides additional domain-controller and file-server functionality, SAMNAT acts as the lab router, and the Windows 10 client is used to validate user-facing behavior.

| Component | Description |
|----------|-------------|
| Domain | `samueldomain.com` |
| DC1 | Windows Server 2019, AD DS, DNS, DHCP |
| DC2 | Windows Server 2019, additional domain controller, DNS, file server |
| SAMNAT | Windows Server 2019, RRAS NAT router |
| Client | Windows 10 domain-joined workstation |
| Internal network | `192.168.116.0/24` |
| DC1 IP | `192.168.116.200` |
| DC2 IP | `192.168.116.201` |
| NAT/LAN gateway | `192.168.116.254` |
| WAN network | `192.168.68.0/24` lab bridged network |

> The screenshots retain the usernames, domain names, internal addresses, and passwords used inside this isolated training environment.

## Tools and Technologies

- Microsoft Windows Server 2019
- Microsoft Windows 10
- VMware virtual machines
- Active Directory Domain Services
- DNS Server
- DHCP Server
- Routing and Remote Access Service
- Group Policy Management
- Active Directory Users and Computers
- File Server Resource Manager
- PowerShell Active Directory module
- `dsadd`
- `net use`
- RDP
- NTFS ACLs

## Implementation Walkthrough

The walkthrough is ordered by dependency. Domain services are configured first, then routing and address assignment, then DNS, user profile behavior, file services, and finally endpoint/security policy. Each step explains the purpose of the configuration before showing the supporting evidence.

---------

## Network Topology and Lab Planning

The lab starts with a defined topology and addressing plan. This stage matters because Windows domain services depend heavily on predictable DNS, stable domain-controller addresses, and clear server roles. A weak plan at this point can cause problems later with domain joins, DHCP options, replication, and name resolution.

The design uses two domain controllers for identity services, SAMNAT for network routing/NAT, and one Windows 10 endpoint to test the user experience from a client perspective.

**Implemented controls:**

- Defined the logical topology and traffic path.
- Documented server roles before installation.
- Planned static IP addressing for infrastructure systems.
- Prepared the VMware virtual machines required for the lab.

### Network topology

This diagram shows the relationship between DC1, DC2, SAMNAT, the internal LAN, the Windows 10 client, and the external internet path. It is the baseline architecture used throughout the project.

> A topology diagram makes the lab easier to understand before any configuration starts. It shows which machines provide identity services, which server routes traffic, where clients sit, and how traffic is expected to move between the internal domain and the outside network.

![Network Topology](images/01-topology/01-network-topology.png)

<p><sub><strong>Screenshot 001 - Network Topology:</strong> Network topology with DC1, DC2, NAT server, Win10 client, LAN and internet path.</sub></p>

### Lab addressing and role plan

Before installation, the lab defines hostnames, IP addresses, gateway settings, DNS settings, and server roles. This prevents later conflicts and makes the implementation easier to validate. The table below converts the original planning screenshot into a cleaner technical reference.

> Domain controllers, DNS servers, DHCP scopes, and routing depend on stable addressing. If a domain controller IP changes unexpectedly, clients may fail to locate the domain, join the domain, apply Group Policy, or resolve internal names correctly.

**General lab parameters:**

| Item | Value |
|------|-------|
| Domain | `samueldomain.com` |
| FQDN | `samueldomain.com` |
| Internal subnet | `192.168.116.0/24` |
| DHCP address pool | `192.168.116.50` - `192.168.116.100` |
| Admin username | `Avocado` |
| Admin password | `1234qweR` |

**Server addressing and roles:**

| Server Role | Hostname | Operating System | IP Address | Default Gateway | Primary DNS | Secondary DNS | Roles |
|-------------|----------|------------------|------------|-----------------|-------------|---------------|-------|
| First domain controller | `samdc1` | Windows Server 2019 | `192.168.116.200/24` | `192.168.116.254` | `192.168.116.200` | `192.168.116.201` | AD DS, DNS, DHCP |
| Second domain controller | `samdc2` | Windows Server 2019 | `192.168.116.201/24` | `192.168.116.254` | `192.168.116.200` | `192.168.116.201` | AD DS, DNS, File Server |
| NAT server | `samnat` | Windows Server 2019 | `192.168.116.254/24` | Not configured on LAN side | `192.168.116.200` | `192.168.116.201` | RRAS, NAT |

### Virtual machine inventory

The VMware inventory confirms that the required machines were prepared before domain deployment: DC1, DC2, SAMNAT, and the Windows 10 client.

> The inventory proves that the lab has the correct systems before services are installed. Separating roles across different virtual machines also makes the design closer to a real environment than installing every service on one server.

![VM Inventory](images/02-lab-environment/02-vm-inventory.png)

<p><sub><strong>Screenshot 003 - VM Inventory:</strong> VMware inventory showing the lab virtual machines.</sub></p>

### Prepare the Windows Server 2019 installation

Windows Server 2019 is installed on all server virtual machines in the lab: DC1, DC2, and SAMNAT. During the initial setup, the installation language is set to English, the regional format is set to Hebrew (Israel), and the keyboard layout is set to US.

> Using the same operating system across all servers is not a strict requirement, but it provides a more consistent platform for Active Directory, DNS, DHCP, file services, and RRAS. A mixed Windows Server environment can also work when the selected versions support the required roles, domain and forest functional levels, and application compatibility.

![Windows Server 2019 Language Setup](images/02-lab-environment/03-windows-server-language-setup.png)

<p><sub><strong>Screenshot 004 - Windows Server 2019 Language Setup:</strong> Windows Server 2019 setup with the installation language, regional format, and keyboard layout selected.</sub></p>

---------

## Active Directory Domain Services

Active Directory is the foundation of the lab. DC1 is prepared first because it becomes the first domain controller in the forest and establishes the domain naming structure. Once the domain exists, DC2 is joined and promoted as an additional domain controller to demonstrate redundancy, replication, and role distribution.

This section also includes account hardening and administrative group preparation because those decisions affect how the rest of the environment is managed.

**Implemented controls:**

- Promoted DC1 into a new Active Directory forest.
- Added DC2 as an additional domain controller.
- Created a controlled administrator account and documented built-in Administrator hardening.
- Created administrative and departmental groups.
- Demonstrated user creation with DSADD and PowerShell.
- Validated AD object replication between domain controllers.

### Rename DC1 before promotion

The first server is renamed before domain promotion. Clear server naming is important because the hostname becomes part of the domain infrastructure and appears in DNS, ADUC, and management tools.

> A domain controller name becomes part of many administrative views and records. Renaming the server before promotion is cleaner than renaming it after it already holds domain-controller services, DNS records, and replication metadata.

![DC1 Computer Name](images/03-active-directory/01-dc1-computer-name.png)

<p><sub><strong>Screenshot 006 - DC1 Computer Name:</strong> DC1 computer rename before domain promotion.</sub></p>

### Add Active Directory Domain Services

The AD DS and DNS roles are selected from Server Manager. DNS is installed with AD DS because the domain depends on DNS for locating domain controllers and services.

> Active Directory Domain Services is the Windows role that provides centralized authentication, users, groups, computers, and domain policy. DNS is installed alongside it because domain clients use DNS records to find domain controllers and logon services.

![Add AD DS Role](images/03-active-directory/02-add-ad-ds-role.png)

<p><sub><strong>Screenshot 007 - Add AD DS Role:</strong> Adding Active Directory Domain Services and DNS roles.</sub></p>

### Create the new forest

DC1 is promoted into a new forest using `samueldomain.com` as the root domain. This creates the directory structure, domain naming context, and initial domain services.

> A forest is the top-level security and directory boundary in Active Directory. Creating the first forest and root domain turns the server from a standalone machine into the first authority for identity, authentication, and policy in the lab.

![New Forest Root Domain](images/03-active-directory/04-new-forest-root-domain.png)

<p><sub><strong>Screenshot 009 - New Forest Root Domain:</strong> New forest deployment with `samueldomain.com` as the root domain.</sub></p>

### Controlled admin account and built-in account hardening

After domain creation, a controlled administrative account is created and used instead of relying on the default built-in Administrator account. At this stage, the built-in Administrator account is also disabled as part of the hardening process.

This is a security-focused change, not just an administrative preference. The built-in Administrator account has the well-known RID 500, which makes it predictable during RID enumeration and account discovery. Attackers often look for default privileged accounts first, so using a named controlled admin account and disabling or renaming the default account reduces exposure to default-user targeting while keeping administrative access available.

> Administrative work should be tied to controlled, named accounts instead of a default account that every attacker expects to exist. This also makes auditing cleaner because privileged actions can be connected to a specific administrative identity.

![Create Controlled Admin Account](images/03-active-directory/06-create-controlled-admin-account.png)

<p><sub><strong>Screenshot 011 - Create Controlled Admin Account:</strong> Controlled administrative account created before hardening the default built-in Administrator account.</sub></p>

> Security note: In a production environment, this control should be combined with least privilege, privileged access management, MFA for administrative access, and auditing of privileged logons.

### Promote DC2 into the existing domain

DC2 is joined to the existing domain and promoted as an additional domain controller. This improves resilience and provides another server for domain services.

> A second domain controller gives the domain another copy of the directory database and another server that can answer logon and directory requests. This reduces dependence on DC1 and demonstrates the idea of redundancy in identity infrastructure.

![Promote DC2 Existing Domain](images/03-active-directory/10-promote-dc2-existing-domain.png)

<p><sub><strong>Screenshot 015 - Promote DC2 Existing Domain:</strong> DC2 promotion into the existing domain.</sub></p>

### Transfer the RID Master role

The RID Master FSMO role is transferred to DC2 as part of the domain-controller administration exercise. The RID Master allocates relative IDs used to create unique SIDs for new AD objects.

> FSMO means Flexible Single Master Operations. These are special Active Directory roles that must be handled by one domain controller at a time to avoid conflicts. The RID Master gives domain controllers pools of Relative IDs, and those RIDs become part of each user, group, or computer SID. Without available RID pools, the domain can run into problems creating new security principals.

![Transfer RID Master to DC2](images/03-active-directory/13-transfer-rid-master-to-dc2.png)

<p><sub><strong>Screenshot 018 - Transfer RID Master to DC2:</strong> RID Master role transfer confirmation.</sub></p>

### Create administrative and department groups

The lab creates groups such as `Sales` and `Sys_Admins`. These groups are later used for file permissions, remote management, and Group Policy targeting.

> Groups are the clean way to manage access. Instead of assigning permissions one user at a time, users are placed into groups and permissions are assigned to the group, which is easier to audit and maintain.

![Sales and SysAdmins Groups](images/03-active-directory/15-sales-and-sysadmins-groups.png)

<p><sub><strong>Screenshot 020 - Sales and SysAdmins Groups:</strong> Sales and Sys_Admins group membership.</sub></p>

### Create users and groups with DSADD

The lab uses `dsadd` to create a domain user and two security groups directly from Command Prompt. Each command identifies the Active Directory object by its distinguished name, which includes the object name, container, and domain path.

```cmd
dsadd user "CN=David Scotch,CN=Users,DC=SamuelDomain,DC=com" ^
    -samid davids ^
    -upn davids@samueldomain.com ^
    -fn David ^
    -ln Scotch ^
    -pwd 1234qweR ^
    -pwdneverexpires yes

dsadd group "CN=Group 1,CN=Users,DC=SamuelDomain,DC=com" ^
    -scope g ^
    -secgrp yes ^
    -members "CN=Shon Wiskey,CN=Users,DC=SamuelDomain,DC=com"

dsadd group "CN=Group 2,CN=Users,DC=SamuelDomain,DC=com" ^
    -scope g ^
    -secgrp yes ^
    -members "CN=David Scotch,CN=Users,DC=SamuelDomain,DC=com"
```

> `dsadd` demonstrates repeatable administration without relying on the graphical console. The password and non-expiring setting are retained for this isolated lab; production accounts should use protected credential handling, password expiration, and least-privilege group membership.

![DSADD Script Run](images/03-active-directory/17-dsadd-users-and-groups-script-run.png)

<p><sub><strong>Screenshot 022 - DSADD Script Run:</strong> DSADD script execution and created groups/users.</sub></p>

### Create users with PowerShell

PowerShell is used to add ten domain users, `user50` through `user59`, to the `PS` organizational unit. These accounts can then be assigned to security groups and used in later administration, access-control, Group Policy, and service-validation tasks.

> Automating account creation keeps usernames, UPNs, OU placement, and initial account settings consistent. The `-lt 60` condition stops the loop before 60, producing exactly ten accounts.

```powershell
Import-Module ActiveDirectory

$domainDn = "DC=SamuelDomain,DC=com"
$ouName = "PS"
$ouPath = "OU=$ouName,$domainDn"
$initialPassword = ConvertTo-SecureString "1234qweR" -AsPlainText -Force

if (-not (Get-ADOrganizationalUnit -Identity $ouPath -ErrorAction SilentlyContinue)) {
    New-ADOrganizationalUnit `
        -Name $ouName `
        -Path $domainDn `
        -ProtectedFromAccidentalDeletion $true
}

for ($i = 50; $i -lt 60; $i++) {
    $username = "user$i"
    $displayName = "User $i"

    New-ADUser `
        -Name $displayName `
        -DisplayName $displayName `
        -SamAccountName $username `
        -UserPrincipalName "$username@samueldomain.com" `
        -Path $ouPath `
        -AccountPassword $initialPassword `
        -Enabled $true `
        -ChangePasswordAtLogon $true
}
```

### Validate AD replication

Before comparing directory objects, DC2 is discovered through Active Directory and added to the **All Servers** view in Server Manager. This gives the administrator a central console for monitoring and opening management tools against both domain controllers.

> Adding DC2 to Server Manager does not enable replication by itself. AD DS replication is established when DC2 is promoted as an additional domain controller; this screen confirms that the server can be discovered in the domain and added to the management console before replication is checked in ADUC.

![Add DC2 to Server Manager](images/03-active-directory/21-server-manager-add-dc2.png)

<p><sub><strong>Screenshot 026 - Add DC2 to Server Manager:</strong> DC2 discovered through Active Directory and selected for addition to Server Manager.</sub></p>

Active Directory Users and Computers is opened separately against `samdc2.samueldomain.com` and `samdc1.samueldomain.com`. The `PS` organizational unit and the same user objects are visible through both domain controllers, confirming that the directory changes replicated between DC1 and DC2.

The directory view also contains `user60`, an additional test account outside the ten-account PowerShell loop documented above. The loop itself intentionally creates `user50` through `user59`.

> Replication is what keeps domain controllers synchronized. If users or groups appear on one domain controller but not another, clients may receive inconsistent authentication or policy results depending on which domain controller they contact.

![AD Replication Visible on Both DCs](images/03-active-directory/22-ad-replication-visible-on-both-dcs.png)

<p><sub><strong>Screenshot 027 - AD Replication Visible on Both DCs:</strong> The PS organizational unit and matching user objects visible through both DC2 and DC1.</sub></p>

---------

## NAT and Routing with RRAS

SAMNAT is configured with an internal LAN interface and an external bridged interface. RRAS provides NAT so internal lab systems can reach external networks without assigning each internal host direct external access.

PAT is documented here as a NAT translation method, not a standalone protocol. It allows multiple internal systems to share one external address by translating sessions with unique port mappings.

The purpose of this section is to make the isolated lab network usable for validation tasks such as DNS forwarding tests, updates, and external connectivity checks while keeping the internal subnet controlled.

**Implemented controls:**

- Renamed SAMWINPC1 and SAMNAT and joined both systems to `samueldomain.com`.
- Configured separate LAN and WAN interfaces on SAMNAT.
- Installed the Remote Access role for RRAS.
- Enabled NAT and LAN routing.
- Marked the WAN adapter as the public NAT interface.
- Validated outbound connectivity from internal systems.

### Name and join SAMWINPC1 and SAMNAT to the domain

The Windows 10 client is renamed `SAMWINPC1` and joined to `samueldomain.com`. A clear client hostname makes the workstation easy to identify in Active Directory, DNS, DHCP leases, Group Policy results, and administrative logs. Domain membership allows the client to authenticate domain users and receive centrally managed settings.

![SAMWINPC1 Domain Membership](images/04-nat-routing/01-samwinpc1-domain-membership.png)

<p><sub><strong>Screenshot 028 - SAMWINPC1 Domain Membership:</strong> SAMWINPC1 configured with its computer name and joined to the samueldomain.com domain.</sub></p>

The routing server is renamed `SAMNAT` and joined to the same domain. After the domain change, Windows reports the full computer name as `SAMNAT.SamuelDomain.com`. `SAMNAT` is the short hostname, while `SAMNAT.SamuelDomain.com` is the fully qualified domain name that identifies the server inside the domain DNS namespace.

> RRAS and NAT can operate on a standalone Windows Server, so domain membership is not a technical requirement for packet routing. In this lab, joining SAMNAT to the domain provides centralized administration, domain authentication, DNS registration, and consistent access to management tools.

![SAMNAT Domain Membership](images/04-nat-routing/02-samnat-domain-membership.png)

<p><sub><strong>Screenshot 029 - SAMNAT Domain Membership:</strong> SAMNAT joined to SamuelDomain.com with the full computer name SAMNAT.SamuelDomain.com.</sub></p>

### Configure SAMNAT network interfaces

SAMNAT has a LAN interface for the internal `192.168.116.0/24` network and a WAN interface connected to the bridged external network.

> A NAT router needs at least two sides: an inside network and an outside network. The LAN adapter faces the private domain network, while the WAN adapter provides the path toward the external network.

![SAMNAT NIC Setup](images/04-nat-routing/03-samnat-domain-join-and-nic-setup.png)

<p><sub><strong>Screenshot 030 - SAMNAT NIC Setup:</strong> SAMNAT domain membership and network interface setup.</sub></p>

### Install the Remote Access role

The Remote Access role is installed to provide Routing and Remote Access Service functionality.

> RRAS is the Windows Server feature that can provide routing, NAT, and remote access capabilities. In this lab it is used as a router/NAT service so internal machines do not need direct external network exposure.

![Remote Access Role Selection](images/04-nat-routing/07-remote-access-role-selection.png)

<p><sub><strong>Screenshot 034 - Remote Access Role Selection:</strong> Remote Access role selected for NAT/RRAS.</sub></p>

### Enable NAT and LAN routing

RRAS is configured with NAT and LAN routing. This allows internal hosts to send traffic through SAMNAT.

> NAT translates private internal addresses into an address that can communicate externally. PAT, often called NAT overload, also tracks sessions by port number so multiple internal machines can share the same outside path at the same time.

![NAT and LAN Routing Selection](images/04-nat-routing/09-nat-and-lan-routing-selection.png)

<p><sub><strong>Screenshot 036 - NAT and LAN Routing Selection:</strong> RRAS custom configuration with NAT and LAN routing.</sub></p>

### Mark the WAN interface as public

The WAN interface is selected as the public interface connected to the external network. NAT is enabled on this interface.

> RRAS must know which adapter is internal and which one is external. Marking the wrong adapter as public can break routing or accidentally expose the wrong side of the network.

![NAT Public Interface Configuration](images/04-nat-routing/10-nat-public-interface-configuration.png)

<p><sub><strong>Screenshot 037 - NAT Public Interface Configuration:</strong> NAT public interface configuration.</sub></p>

### Validate internet connectivity from DC1

After NAT is configured, DC1 can reach an external DNS address. This proves that internal systems can route outbound traffic through SAMNAT.

> A connectivity test confirms that the design works end to end: DC1 sends traffic to its gateway, SAMNAT translates and routes it, and the response returns correctly. This validation is needed before later DNS forwarding and update-related tests.

![DC1 Internet Connectivity Test](images/04-nat-routing/11-dc1-internet-connectivity-test.png)

<p><sub><strong>Screenshot 038 - DC1 Internet Connectivity Test:</strong> DC1 ping test to public DNS through NAT.</sub></p>

---------

## DHCP Services and Failover

DHCP is installed on DC1 to automatically provide IP configuration to client systems. Instead of manually configuring every workstation, DHCP distributes the address, subnet mask, gateway, DNS servers, and lease information from a central service.

The lab also configures a DHCP failover relationship with DC2. This demonstrates service continuity: if one DHCP server is unavailable, the partner server can continue supporting client address assignment.

From a security and operations point of view, DHCP has to be predictable. Wrong gateway or DNS options can break authentication, redirect traffic, or make clients use the wrong name-resolution path, so the scope, exclusions, and authorization are treated as controls rather than simple setup steps.

**Implemented controls:**

- Installed and authorized the DHCP Server role.
- Created a controlled DHCP scope for client addressing.
- Configured exclusions for reserved infrastructure addresses.
- Assigned gateway and DNS options to clients.
- Built a DHCP failover relationship with DC2.

### Install the DHCP Server role

The DHCP Server role is selected in Server Manager. This prepares DC1 to distribute IP configuration to client systems.

> DHCP removes the need to manually configure every client with an IP address, gateway, and DNS server. In a domain, this is especially important because clients must receive the correct internal DNS settings to find Active Directory.

![Select DHCP Server Role](images/05-dhcp/02-select-dhcp-server-role.png)

<p><sub><strong>Screenshot 042 - Select DHCP Server Role:</strong> DHCP Server role selection.</sub></p>

### Authorize DHCP in Active Directory

The DHCP server is authorized so it can issue leases in the AD domain environment. Authorization helps prevent an unmanaged DHCP server from silently handing out incorrect network settings inside the domain.

> In Active Directory environments, DHCP authorization is a protection mechanism against rogue DHCP servers. A rogue server can give clients the wrong gateway or DNS server, which can cause outages or enable traffic redirection.

![DHCP Authorization](images/05-dhcp/04-dhcp-authorization.png)

<p><sub><strong>Screenshot 044 - DHCP Authorization:</strong> DHCP authorization using domain credentials.</sub></p>

### Create the DHCP scope

The DHCP scope defines the client address range. This lab uses a controlled range inside the internal subnet.

> A scope tells DHCP which addresses it is allowed to lease. Defining a scope prevents random addressing and keeps client IP assignments inside the planned network range.

![DHCP Scope Address Range](images/05-dhcp/07-dhcp-scope-address-range.png)

<p><sub><strong>Screenshot 047 - DHCP Scope Address Range:</strong> DHCP scope address range configuration.</sub></p>

### Add exclusions

The first addresses in the range are excluded so they can remain reserved for static infrastructure or other planned use.

> Exclusions prevent DHCP from leasing addresses that should stay reserved for servers, routers, printers, or future infrastructure. This avoids IP conflicts between manually configured systems and DHCP clients.

![DHCP Exclusion Range](images/05-dhcp/08-dhcp-exclusion-range.png)

<p><sub><strong>Screenshot 048 - DHCP Exclusion Range:</strong> DHCP exclusion range configuration.</sub></p>

### Configure DHCP options

The scope provides gateway and DNS settings to clients. The gateway points to SAMNAT and DNS points to the domain DNS servers.

> DHCP options are just as important as the IP address itself. The default gateway tells clients how to leave the subnet, and DNS options tell them where to resolve domain names and locate domain controllers.

![DNS Server Option](images/05-dhcp/12-dns-server-option.png)

<p><sub><strong>Screenshot 052 - DNS Server Option:</strong> DHCP DNS server option for domain clients.</sub></p>

### Validate automatic client configuration

The Windows 10 client receives its IP configuration automatically from DHCP. This validates the DHCP scope and options.

> Client validation proves that the DHCP server is not only configured, but actually usable from the endpoint side. It also confirms that the client receives the intended DNS and gateway values.

![Windows 10 DHCP Address Validation](images/05-dhcp/13-win10-dhcp-address-validation.png)

<p><sub><strong>Screenshot 053 - Windows 10 DHCP Address Validation:</strong> Windows 10 receiving automatic DHCP address configuration.</sub></p>

### Configure DHCP failover

The lab configures a DHCP failover relationship with DC2. This is more accurately described as DHCP failover, not a failover cluster.

> DHCP failover lets two DHCP servers share responsibility for leases. If one server is unavailable, the other can continue leasing addresses, which improves availability for client network access.

![DHCP Failover Relationship](images/05-dhcp/17-dhcp-failover-relationship.png)

<p><sub><strong>Screenshot 057 - DHCP Failover Relationship:</strong> DHCP failover relationship settings.</sub></p>

---------

## Remote Administration

Remote Desktop is enabled for the `Sys_Admins` group so administrators can manage servers from the Windows 10 workstation. The lab also demonstrates RDP access through a NAT forwarding rule.

Publishing RDP through NAT is treated here as lab-only evidence. In production, this should be replaced with VPN, RD Gateway, MFA, restricted source IPs, and explicit firewall rules.

This section is included because infrastructure administration is not only about installing services; administrators also need controlled, auditable ways to access servers. The lab validates both internal RDP access and an external-side forwarding scenario.

**Implemented controls:**

- Enabled Remote Desktop access for the administrator group.
- Validated RDP access from the Windows 10 workstation.
- Configured a lab-only NAT forwarding rule for RDP.
- Confirmed external-side RDP connectivity through SAMNAT.
- Documented production security considerations for exposed RDP.

### Enable RDP for administrators

The server is configured to allow RDP access for the administrative group.

> RDP allows administrators to manage servers remotely, but it should be limited to approved administrative users. Granting access through a group keeps the permission easier to review and remove.

![Enable Remote Desktop for SysAdmins](images/06-remote-management/01-enable-remote-desktop-for-sysadmins.png)

<p><sub><strong>Screenshot 058 - Enable Remote Desktop for SysAdmins:</strong> Remote Desktop enabled for the Sys_Admins group.</sub></p>

### Validate RDP from the client

The Windows 10 client connects to a server through Remote Desktop using an authorized administrative user.

> Testing from the client side proves that firewall rules, group membership, credentials, and Remote Desktop settings all work together. It also confirms that access is usable for real administration, not only enabled in a settings window.

![RDP Connection as User3](images/06-remote-management/04-rdp-connection-as-user3.png)

<p><sub><strong>Screenshot 061 - RDP Connection as User3:</strong> RDP connection from Windows 10 as User 3.</sub></p>

### Configure NAT forwarding for RDP lab access

RRAS NAT is configured with a forwarding rule for RDP testing from the external side of the lab.

> Port forwarding maps traffic arriving on the NAT server to an internal machine. This is useful for a lab demonstration, but exposing RDP directly is risky in production because it increases brute-force and credential-attack exposure.

![NAT RDP Port Forward Rule](images/06-remote-management/06-nat-rdp-port-forward-rule.png)

<p><sub><strong>Screenshot 063 - NAT RDP Port Forward Rule:</strong> NAT port forwarding rule for RDP lab access.</sub></p>

### Validate the forwarded RDP connection

The RDP connection is initiated from the physical host computer rather than from another virtual machine inside the lab. The host connects to SAMNAT's WAN address at `192.168.68.109:5589`, and RRAS forwards the session to the internal RDP destination.

> Testing from the physical computer validates the complete external-to-internal path: the connection reaches the WAN interface, matches the forwarding rule, and is translated to the internal system. Direct RDP forwarding remains a lab-only design; production access should use VPN or RD Gateway with MFA and source restrictions.

![RDP Port Forward Validation](images/06-remote-management/07-rdp-port-forward-validation.png)

<p><sub><strong>Screenshot 064 - RDP Port Forward Validation:</strong> RDP connection initiated from the physical host through SAMNAT's forwarded lab port.</sub></p>

---------

## DNS Services and Name Resolution

DNS supports the domain and provides internal name resolution, external forwarding, controlled domain blocking, zone management, stub zones, and round robin behavior. In Active Directory, DNS is critical because clients use it to locate domain controllers and domain services.

This section demonstrates both internal DNS administration and external resolution behavior. The configuration includes forwarders, custom zones, conditional forwarding, a stub zone, secondary zone behavior, host records, and round robin testing.

DNS is treated here as both an infrastructure service and a security control. If DNS is wrong, domain logon, Kerberos, Group Policy, software deployment, and file access can all fail. If DNS is intentionally controlled, it can also be used to steer or block name resolution in a lab-safe way.

**Implemented controls:**

- Verified domain DNS settings on client and server systems.
- Configured external DNS forwarding.
- Created custom zones for controlled name-resolution behavior.
- Configured conditional forwarding and stub zone examples.
- Built primary and secondary zone behavior.
- Validated host records and round robin DNS responses.

### Confirm DNS client settings

Domain systems are configured to use the internal domain DNS servers. This is required for domain join, authentication, and service discovery.

> Active Directory depends on DNS service records. If a client uses an external DNS server instead of the domain DNS server, it may resolve internet names but still fail to find domain controllers, logon services, or Group Policy paths.

![Windows 10 DNS Client Settings](images/07-dns/05-win10-dns-client-settings.png)

<p><sub><strong>Screenshot 069 - Windows 10 DNS Client Settings:</strong> Windows 10 DNS client settings pointing to the domain DNS server.</sub></p>

### Configure external DNS forwarding

External forwarding is configured so internal DNS servers can forward unresolved queries to an upstream DNS provider.

> Internal DNS should answer domain-related queries itself, but it still needs a path for internet names. A forwarder lets the domain DNS server keep control of internal resolution while sending unknown external queries to a trusted upstream resolver.

![DC1 External Forwarder](images/07-dns/08-dc1-external-forwarder.png)

<p><sub><strong>Screenshot 072 - DC1 External Forwarder:</strong> DC1 external DNS forwarder configured for recursive resolution.</sub></p>

### Create a controlled zone for Facebook blocking

The lab creates a DNS zone for `facebook.com` and adds a controlled host record that prevents normal resolution. This demonstrates DNS-based name-resolution control in a lab context.

> DNS can be used to influence where clients go when they request a name. In this lab, a controlled zone demonstrates how name resolution can block or redirect access, which is useful for understanding both defensive filtering and the risk of malicious DNS manipulation.

![Facebook Zone Name](images/07-dns/11-facebook-zone-name.png)

<p><sub><strong>Screenshot 075 - Facebook Zone Name:</strong> Facebook.com zone name configuration.</sub></p>

### Validate the Facebook resolution result

The client validation shows that the configured DNS response prevents normal access to the target domain.

> A DNS control is only useful if the endpoint receives the expected answer. Testing from the client confirms that the DNS zone is actually affecting user traffic, not just existing in DNS Manager.

![Facebook Resolution Block Validation](images/07-dns/13-facebook-resolution-block-validation.png)

<p><sub><strong>Screenshot 077 - Facebook Resolution Block Validation:</strong> Client validation showing blocked or unreachable Facebook resolution.</sub></p>

### Discover Google name servers

`nslookup` is run from the Windows 10 client with the query type set to `NS`. The client sends the request to DC1 at `192.168.116.200` and receives the authoritative Google name servers, including `ns1.google.com`, `ns2.google.com`, `ns3.google.com`, and `ns4.google.com`.

> The successful response confirms that the client is using DC1 as its DNS server and that DC1 can resolve external DNS data. The `Server: Unknown` label only indicates that a reverse PTR record is not available for the DNS server address; it does not mean the lookup failed.

![Google nslookup Output](images/07-dns/14-google-nslookup-output.png)

<p><sub><strong>Screenshot 078 - Google nslookup Output:</strong> `nslookup` output for Google DNS records.</sub></p>

### Configure a Google conditional forwarder

DC1 is configured with a conditional forwarder for `google.com` that sends matching queries to `ns1.google.com` at `216.239.32.10`. Queries for other domains continue to use the server's normal DNS resolution path.

> Conditional forwarding creates a domain-specific resolution path. It is commonly used when an organization must resolve a partner domain, a private namespace, or another Active Directory forest through designated DNS servers without changing resolution for every other domain. Google is used here to demonstrate the mechanism in the lab.

![Google Conditional Forwarder](images/07-dns/15-google-conditional-forwarder.png)

<p><sub><strong>Screenshot 079 - Google Conditional Forwarder:</strong> Google conditional forwarder configured with a master server.</sub></p>

### Configure a Yahoo stub zone

A stub zone for `yahoo.com` is created from the master server `ns2.yahoo.com` at `68.142.255.16`. DC1 uses that server to obtain the zone's SOA record, NS records, and the address records needed to locate Yahoo's authoritative DNS servers.

> Unlike a conditional forwarder, which sends matching queries to a fixed DNS server, a stub zone maintains a small, refreshable list of authoritative servers for the target zone. This is useful when DNS should follow the authoritative server set as it changes without storing a complete copy of the external zone.

![Yahoo Stub Zone Master Servers](images/07-dns/17-yahoo-stub-zone-master-servers.png)

<p><sub><strong>Screenshot 081 - Yahoo Stub Zone Master Servers:</strong> Stub zone master servers for Yahoo.</sub></p>

### Create a secondary DNS zone

The lab creates a primary zone on DC1 and a secondary zone on DC2. The secondary zone receives data from the primary DNS server.

> A secondary zone is a read-only copy of a DNS zone transferred from a primary server. This improves availability for name resolution and demonstrates how DNS data can be replicated between DNS servers.

![Secondary Zone Master IP](images/07-dns/21-secondary-zone-master-ip.png)

<p><sub><strong>Screenshot 085 - Secondary Zone Master IP:</strong> Primary DNS server IP used for secondary zone transfer.</sub></p>

### Create and validate a New Host record

The **New Host** action in DNS Manager creates an `A` record that maps a readable host name to an IPv4 address. In this step, the name `mail` is mapped to DC1 at `192.168.116.200`, creating the fully qualified domain name `mail.samueldomain.com`.

> New Host records allow users and applications to reach systems by name instead of remembering IP addresses. They can identify web servers, mail services, file servers, application endpoints, printers, and other network devices. The `ping -a mail` test from the Windows 10 client confirms that the new name resolves to the intended address.

![Mail A Record Created](images/07-dns/23-mail-a-record-created.png)

<p><sub><strong>Screenshot 087 - Mail A Record Created:</strong> Mail A record created for lab name-resolution validation.</sub></p>

After the record is created, PC1 resolves `mail.samueldomain.com` to `192.168.116.200` and receives replies from DC1. This confirms that the New Host record is available to domain clients through the internal DNS service.

![Mail Host Record Validation](images/07-dns/24-mail-record-ping-validation.png)

<p><sub><strong>Screenshot 088 - Mail Host Record Validation:</strong> PC1 resolving and reaching the new `mail.samueldomain.com` host record.</sub></p>

### Configure and validate DNS round robin

DNS round robin is a basic load-distribution method that places several IP addresses behind one host name. DC1 rotates the order of those addresses in its responses, helping spread client requests across multiple equivalent servers instead of directing every request to one system.

First, three `A` records are created with the same test hostname and different Google server addresses. The **Enable round robin** option is then enabled in the advanced properties of the DC1 DNS service. Finally, repeated `nslookup` queries are run from PC1 to confirm that the returned address order changes.

> Round robin can reduce the concentration of traffic on one server and improve basic service distribution. It is not a full load balancer because DNS does not check whether a server is healthy, and client-side DNS caching can influence which address is selected.

![Round Robin Host Records](images/07-dns/25-round-robin-host-records.png)

<p><sub><strong>Screenshot 089 - Round Robin Host Records:</strong> Three host records using the same name and different IP addresses for round robin.</sub></p>

![Enable DNS Round Robin](images/07-dns/26-round-robin-enabled.png)

<p><sub><strong>Screenshot 090 - Enable DNS Round Robin:</strong> Round robin enabled in the advanced properties of the DC1 DNS service.</sub></p>

![Round Robin Validation](images/07-dns/27-round-robin-validation.png)

<p><sub><strong>Screenshot 091 - Round Robin Validation:</strong> Repeated `nslookup` queries from PC1 showing the returned addresses in a different order.</sub></p>

---------

## Roaming and Mandatory Profiles

Roaming profiles allow user profile data to follow the user across workstations. An AD user account represents identity and authentication, while a Windows user profile contains the user environment, settings, and profile data loaded during logon.

The goal of this section is to show centralized user-environment management. Roaming profiles support mobility between computers, while mandatory profiles enforce a consistent, non-persistent user environment.

**Implemented controls:**

- Created a centralized profile storage folder.
- Shared the profile location for domain access.
- Assigned the roaming profile path through AD user properties.
- Validated server-side profile folder creation after logon.
- Converted a roaming profile into a mandatory profile.

### Create the profile storage folder

A profile folder is created on DC1 to store roaming profile data centrally.

> Roaming profiles need a central network location where profile data can be saved and loaded. Without a shared storage location, the user profile remains tied to one workstation.

![Profile Folder Created](images/08-roaming-profiles/01-profile-folder-created.png)

<p><sub><strong>Screenshot 092 - Profile Folder Created:</strong> Roaming profile root folder created on DC1.</sub></p>

### Configure profile share permissions

The folder is shared so users can access their roaming profile location through the network.

> The share makes the folder reachable over the network, while permissions control who can read or write profile data. Profile shares must be protected because they can contain user settings and personal application data.

![Advanced Sharing for Profile Folder](images/08-roaming-profiles/04-advanced-sharing-for-profile-folder.png)

<p><sub><strong>Screenshot 095 - Advanced Sharing for Profile Folder:</strong> Advanced sharing configuration for the profile folder.</sub></p>

### Assign the profile path in AD

The user object receives a profile path that points to the roaming profile share.

> Active Directory stores the profile path on the user object so Windows knows where to load and save that user's profile during logon and logoff. This connects identity management with the user's working environment.

![AD Profile Path](images/08-roaming-profiles/05-ad-profile-path.png)

<p><sub><strong>Screenshot 096 - AD Profile Path:</strong> Active Directory profile path configured for a user.</sub></p>

### Confirm profile folder creation

After the user signs in, the server-side profile folder is created automatically.

> This proves the profile configuration works from the user's session, not only from the administrator's settings. It confirms that Windows can create and use the central profile location.

![User Profile Folder Created](images/08-roaming-profiles/06-user-profile-folder-created.png)

<p><sub><strong>Screenshot 097 - User Profile Folder Created:</strong> User profile folders created after logon.</sub></p>

### Convert the profile to mandatory

After the roaming profile has been created and the user is logged off, the administrator opens the server-side profile folder and renames `NTUSER.DAT` to `NTUSER.MAN`. Windows then loads the profile as mandatory during the next sign-in.

> `NTUSER.DAT` contains user-specific registry settings such as desktop and application preferences. Renaming it to `NTUSER.MAN` makes those settings read-only from the user's perspective: the user can work normally during the session, but profile changes are discarded at sign-out. Mandatory profiles are useful for shared workstations, training rooms, and other environments that require the same controlled desktop at every logon.

![NTUSER.MAN Mandatory Profile](images/08-roaming-profiles/11-ntuser-man-mandatory-profile.png)

<p><sub><strong>Screenshot 102 - NTUSER.MAN Mandatory Profile:</strong> `NTUSER.MAN` conversion for a mandatory profile.</sub></p>

The User Profiles control panel reports the account profile as `Mandatory`, confirming that Windows recognizes the conversion rather than treating it as a standard local or roaming profile.

![Mandatory Profile Validation](images/08-roaming-profiles/12-mandatory-profile-visible.png)

<p><sub><strong>Screenshot 103 - Mandatory Profile Validation:</strong> The User Profiles window on PC1 reporting `SAMUELDOMAIN\user10` as a Mandatory profile.</sub></p>

---------

## File Services and Access Control

DC2 is configured as a file server. The lab creates home folders, a shared DATA folder, a script share, mapped drives, and an FSRM quota.

The purpose is to demonstrate how enterprise file access is controlled through layers: share permissions, NTFS permissions, AD groups, mapped drives, and quota limits. AD defines users and groups, NTFS and share permissions enforce access, and FSRM controls storage consumption.

**Implemented controls:**

- Installed the file server role on DC2.
- Created home folders and mapped them through AD.
- Configured group-based DATA share permissions.
- Validated allowed and denied user actions.
- Built a script-based drive mapping with `net use`.
- Enforced a 5 GB hard quota with FSRM.

### Install the File Server role

DC2 is prepared to host file shares for domain users.

> A file server centralizes storage so users do not depend only on local workstation disks. It also allows administrators to enforce permissions, quotas, and file-type controls from one managed server.

![File Server Role Selection](images/09-file-server/01-file-server-role-selection.png)

<p><sub><strong>Screenshot 104 - File Server Role Selection:</strong> File Server role selected on DC2.</sub></p>

### Create the home folder root

The home folder root is created and shared so each user can receive a personal mapped folder.

> Home folders give users a dedicated network location for personal files. Keeping them under one root folder makes permissions, backups, quotas, and administration easier to manage.

![Home Folder Root Created](images/09-file-server/02-home-folder-root-created.png)

<p><sub><strong>Screenshot 105 - Home Folder Root Created:</strong> Home folder root created on DC2.</sub></p>

### Map home folders through AD

The AD user properties are configured with a home folder path. Windows creates the user-specific folder when the mapping is applied.

> Mapping home folders through Active Directory keeps storage assignment tied to the user account. When the user signs in, Windows can automatically present the same home drive without manual mapping on each workstation.

![AD Home Folder Mapping](images/09-file-server/05-ad-home-folder-mapping.png)

<p><sub><strong>Screenshot 108 - AD Home Folder Mapping:</strong> Home folder mapping configured in AD user properties.</sub></p>

### Validate mapped home drives

The Windows 10 client shows the mapped home drive, confirming the user folder mapping worked.

> Validation from the client proves that the user can actually see and use the mapped drive. This checks AD settings, share access, network connectivity, and permissions together.

![Mapped Home Drive Visible](images/09-file-server/07-mapped-home-drive-visible.png)

<p><sub><strong>Screenshot 110 - Mapped Home Drive Visible:</strong> Mapped home drive visible on the client.</sub></p>

### Configure DATA share permissions

The DATA share is configured with group-based permissions. `Sys_Admins` receive Modify permission, while Sales users receive Read and Execute.

The security goal is to avoid assigning access directly to individual users. Group-based access is easier to review, easier to remove, and safer when users move between roles.

> Share permissions and NTFS permissions work together. The effective access is the most restrictive result between them, so both layers must match the intended access model.

![SysAdmins Modify Permission](images/09-file-server/11-sysadmins-modify-permission.png)

<p><sub><strong>Screenshot 114 - SysAdmins Modify Permission:</strong> Sys_Admins Modify permission on the DATA share.</sub></p>

The `Sales_group` receives **Read and Execute**, **List folder contents**, and **Read** permissions. These permissions allow Sales users to open the shared folder and read its contents without granting the ability to modify or delete files.

![Sales Read and Execute Permission](images/09-file-server/12-sales-read-execute-permission.png)

<p><sub><strong>Screenshot 115 - Sales Read and Execute Permission:</strong> Sales_group granted Read and Execute access to the DATA folder.</sub></p>

### Validate restricted Sales permissions

The Sales user test confirms that the user can access the share but cannot delete files. This denied action is important validation: it proves that the permission boundary works, not only that access is possible.

> Access-control testing should include both allowed and blocked actions. A successful denial proves that least privilege is enforced and that the Sales role cannot perform administrative file operations.

![Sales Delete Denied Test](images/09-file-server/14-sales-delete-denied-test.png)

<p><sub><strong>Screenshot 117 - Sales Delete Denied Test:</strong> Sales user denied delete operation.</sub></p>

### Create a script-based drive mapping

A shared `Script` folder is prepared on DC2 to store the batch file used by domain clients. In this isolated lab, `Everyone` receives Modify access so the script can be reached and updated during testing.

> Broad `Everyone` Modify permission is convenient for a training environment but is not appropriate for production. A real deployment should normally grant users only Read and Execute access while limiting script modification to an administrative group, because a writable logon or mapping script could be altered to execute unwanted commands.

![Script Folder Permissions](images/09-file-server/15-script-share-everyone-modify.png)

<p><sub><strong>Screenshot 118 - Script Folder Permissions:</strong> Lab permissions on the Script folder allowing Everyone to modify its contents.</sub></p>

The folder is shared as `\\SAMDC2\Script`, giving workstations a UNC path that does not depend on the local drive layout of the server.

![Script Share Network Path](images/09-file-server/16-script-share-network-path.png)

<p><sub><strong>Screenshot 119 - Script Share Network Path:</strong> Script folder shared from DC2 through the `\\SAMDC2\Script` network path.</sub></p>

The `Script.bat` file contains `net use X: \\Samdc2\data`. When the batch file runs, Windows connects the DATA share and assigns it the drive letter `X:`. This demonstrates a simple, repeatable method for providing users with a familiar drive letter for a network resource.

> `net use` is the classic Windows command for connecting a network share. Group Policy Preferences are generally easier to target and maintain in a larger production environment, but the batch-file method clearly demonstrates the underlying mapping command.

![Net Use Batch Script](images/09-file-server/17-net-use-batch-script.png)

<p><sub><strong>Screenshot 120 - Net Use Batch Script:</strong> Batch script using `net use` to map the DATA share.</sub></p>

After the script runs on `SAMWINPC1`, File Explorer shows `data (\\Samdc2) (X:)` under Network locations. This confirms that the client resolved DC2, reached the DATA share, and created the requested drive mapping.

![Script Mapped Drive Validation](images/09-file-server/18-script-mapped-drive-validation.png)

<p><sub><strong>Screenshot 121 - Script Mapped Drive Validation:</strong> DATA share mapped as drive X on SAMWINPC1 after running the batch script.</sub></p>

### Configure a 5 GB hard quota

File Server Resource Manager is used to enforce a hard quota on home folders. This is an FSRM/file-server control, not an Active Directory quota.

> FSRM means File Server Resource Manager. A hard quota blocks users from exceeding the limit, which protects shared storage from uncontrolled growth and helps enforce storage policy.

![Hard Quota 5GB](images/09-file-server/21-hard-quota-5gb.png)

<p><sub><strong>Screenshot 124 - Hard Quota 5GB:</strong> Hard quota configured for a 5 GB limit.</sub></p>

---------

## Group Policy Hardening and Software Deployment

Group Policy is used to restrict standard users, add `Sys_Admins` to local Administrators, and deploy software.

This section demonstrates centralized endpoint control. Instead of configuring each workstation manually, GPOs apply consistent restrictions and settings based on domain structure and security groups.

The hardening focus is to reduce the standard user's ability to change workstation behavior, launch administrative tools, or move data through removable media.

**Implemented controls:**

- Created client-hardening GPOs.
- Restricted Command Prompt and Control Panel access for standard users.
- Blocked removable storage access.
- Configured a Restricted Groups policy intended to add `Sys_Admins` to local Administrators on managed workstations.
- Deployed Notepad++ through GPO software installation.

### Open Group Policy Management

The Group Policy Management console provides centralized control for domain-linked policies.

> Group Policy Management is where administrators create, link, and review GPOs. A GPO, or Group Policy Object, is a collection of settings that can be applied to users or computers in the domain.

![Group Policy Management Opened](images/10-group-policy-hardening/01-group-policy-management-opened.png)

<p><sub><strong>Screenshot 127 - Group Policy Management Opened:</strong> Group Policy Management opened.</sub></p>

### Create a client-hardening GPO

A new GPO is created for client restrictions such as Command Prompt, Control Panel, and removable storage controls.

> A dedicated hardening GPO keeps security settings organized and easier to review. It also separates workstation restrictions from unrelated domain policies, which makes troubleshooting cleaner.

![Create New Hardening GPO](images/10-group-policy-hardening/03-create-new-hardening-gpo.png)

<p><sub><strong>Screenshot 129 - Create New Hardening GPO:</strong> Creating a new hardening GPO.</sub></p>

### Restrict Command Prompt access

Inside **User Configuration > Policies > Administrative Templates > System**, the **Prevent access to the command prompt** setting is enabled in the `BLOCK CMD & CP` GPO. This prevents targeted standard users from launching `cmd.exe` and reduces their ability to run interactive command-line tools or unapproved batch commands.

> Blocking Command Prompt is an endpoint restriction, not a complete application-control solution. PowerShell, scripts, and other interpreters should be governed separately when stronger execution control is required, for example through AppLocker or Windows Defender Application Control.

![Command Prompt Access Blocked](images/10-group-policy-hardening/05-command-prompt-deny-policy.png)

<p><sub><strong>Screenshot 131 - Command Prompt Access Blocked:</strong> Prevent access to the command prompt enabled in the BLOCK CMD & CP GPO.</sub></p>

### Restrict access to Control Panel and PC settings

Inside **User Configuration > Policies > Administrative Templates > Control Panel**, the **Prohibit access to Control Panel and PC settings** policy is enabled. This prevents standard users from opening both the traditional Control Panel and the Windows Settings application through `Control.exe` and `SystemSettings.exe`.

> Centralizing this restriction helps prevent users from changing system options that could weaken the workstation baseline, interfere with support, or bypass other administrative controls. Because it is a user-side policy, it follows the targeted domain user when that user signs in to an affected workstation.

![Control Panel and PC Settings Restricted](images/10-group-policy-hardening/04-hardening-gpo-editor-settings.png)

<p><sub><strong>Screenshot 130 - Control Panel and PC Settings Restricted:</strong> Prohibit access to Control Panel and PC settings enabled in the BLOCK CMD & CP GPO.</sub></p>

### Validate standard-user restriction

The standard user is blocked from accessing Control Panel, confirming that the hardening policy applies.

> GPO settings are only valuable if they actually apply to the intended users and computers. This test confirms that standard users receive the restriction and cannot bypass the baseline through Control Panel.

![Standard User Control Panel Blocked](images/10-group-policy-hardening/12-standard-user-control-panel-blocked.png)

<p><sub><strong>Screenshot 138 - Standard User Control Panel Blocked:</strong> Standard user blocked from Control Panel.</sub></p>

### Configure removable storage restrictions

The `BLOCK Disk On Key` GPO is linked at the domain level so the removable-storage restriction can be processed by the targeted domain users and computers.

![Block Disk On Key GPO Link](images/10-group-policy-hardening/09-gpo-linking-overview.png)

<p><sub><strong>Screenshot 135 - Block Disk On Key GPO Link:</strong> BLOCK Disk On Key GPO linked beneath the SamuelDomain.com domain.</sub></p>

The removable-storage policy denies access to removable media. This helps reduce data exfiltration, unauthorized copying, and the introduction of untrusted files from USB storage devices.

> Removable media can be used to copy data out of the environment or introduce untrusted files. Blocking it through policy is a common endpoint-hardening control for standard users.

![Removable Storage Deny Policy](images/10-group-policy-hardening/08-removable-storage-deny-policy.png)

<p><sub><strong>Screenshot 134 - Removable Storage Deny Policy:</strong> Removable storage deny policy.</sub></p>

### Add Sys_Admins to local Administrators

The `Add to Local Admins` GPO is linked under `Sys_Admins` in Group Policy Management. This screenshot records where the policy was linked before its Restricted Groups setting is reviewed.

![Add to Local Admins GPO](images/10-group-policy-hardening/10-add-sysadmins-to-local-admins-gpo.png)

<p><sub><strong>Screenshot 136 - Add to Local Admins GPO:</strong> Add to Local Admins GPO linked beneath the Sys_Admins OU.</sub></p>

Restricted Groups are configured with the intention of placing `Sys_Admins` into the local Administrators group on managed workstations. Because this setting is under **Computer Configuration**, the GPO must be linked where the target computer accounts are located, or otherwise scoped so those computers receive it.

> Restricted Groups can enforce local group membership from the domain. Linking the GPO only to an OU that contains administrator user accounts does not apply the computer-side setting to workstations. Correct validation should include `gpresult /scope computer /r` and `net localgroup administrators` on a target client.

> Security note: Local administrator membership should be limited, reviewed, and monitored. In production, this kind of access should be tied to least privilege and privileged-access procedures.

![Restricted Groups Membership](images/10-group-policy-hardening/11-restricted-groups-membership.png)

<p><sub><strong>Screenshot 137 - Restricted Groups Membership:</strong> Restricted Groups membership configuration.</sub></p>

### Apply Sys_Admins workstation rules

The `Allow CMD & CP` GPO is linked beneath `Sys_Admins`. It provides a separate user-policy scope for approved administrators who need Command Prompt and Control Panel access for support and troubleshooting.

![SysAdmins GPO Link](images/10-group-policy-hardening/02-gpo-list-overview.png)

<p><sub><strong>Screenshot 128 - SysAdmins GPO Link:</strong> Allow CMD & CP GPO linked beneath the Sys_Admins OU.</sub></p>

Inside this policy, **Prevent access to the command prompt** is set to **Disabled**. Disabling the restriction means the policy does not block `cmd.exe` for the targeted Sys_Admins users.

![SysAdmins Command Prompt Rule](images/10-group-policy-hardening/06-sysadmins-command-prompt-exception.png)

<p><sub><strong>Screenshot 132 - SysAdmins Command Prompt Rule:</strong> Command Prompt restriction disabled in the Sys_Admins policy.</sub></p>

The **Prohibit access to Control Panel and PC settings** setting is also disabled in the same administrative policy, allowing the targeted administrators to open management interfaces required for their work.

![SysAdmins Control Panel Rule](images/10-group-policy-hardening/07-sysadmins-control-panel-exception.png)

<p><sub><strong>Screenshot 133 - SysAdmins Control Panel Rule:</strong> Control Panel and PC Settings restriction disabled in the Sys_Admins policy.</sub></p>

### Deploy software through GPO

The Notepad++ 8.5 MSI package is assigned under **Computer Configuration > Policies > Software Settings > Software installation**. The package uses the UNC source `\\samdc2\Software\Notepad++.msi`, allowing targeted domain computers to reach the same network location while Group Policy is processed.

> Assigning an MSI through Computer Configuration allows Windows to install an approved application centrally instead of requiring a manual installation on every endpoint. A UNC path is essential because client computers cannot access a package stored only on the administrator's local `C:` drive.

![Software Installation Assigned](images/10-group-policy-hardening/15-software-installation-assigned.png)

<p><sub><strong>Screenshot 141 - Software Installation Assigned:</strong> Notepad++ 8.5 assigned through GPO from the shared UNC package path on SAMDC2.</sub></p>

### Validate software deployment

The Windows 10 client `SAMWINPC1` shows Notepad++ installed after the computer received the software-deployment policy. Together with the preceding GPO configuration, this provides end-to-end evidence that the MSI was assigned from the server share and successfully deployed to the target workstation through Group Policy.

> This validation connects the administrative configuration to the endpoint result: the first screenshot shows the package assigned through GPO from `\\samdc2\Software\Notepad++.msi`, and the second shows Notepad++ available on the Windows 10 client.

![Software Deployment Validation](images/10-group-policy-hardening/16-software-deployment-validation.png)

<p><sub><strong>Screenshot 142 - Software Deployment Validation:</strong> Notepad++ installed and available on SAMWINPC1 after GPO software deployment.</sub></p>

---------

## Password Policy and Account Security

The lab configures a domain-wide password baseline through Group Policy. A centrally managed policy ensures that domain users follow the same credential requirements regardless of which joined workstation they use.

Password policy is only one layer of account defense. It should support, not replace, account lockout policy, MFA, privileged account monitoring, and a clear process for handling compromised credentials.

**Implemented controls:**

- Configured domain password history and age requirements.
- Enforced minimum password length and complexity.
- Linked the `PassPolicy` GPO at the domain level so it can provide the password baseline for domain accounts.

### Configure the domain password policy

The `PassPolicy` GPO defines a consistent password baseline for domain accounts. It remembers the previous eight passwords, sets a maximum password age of 75 days, prevents another password change for 30 days, requires at least eight characters, and enables Windows complexity requirements.

Each setting addresses a different credential risk:

- **Password history: 8 passwords** prevents users from immediately reusing recent credentials.
- **Maximum password age: 75 days** limits how long the same password remains active in this lab. In modern production environments, forced periodic changes should be balanced against current security guidance and should always occur after suspected compromise.
- **Minimum password age: 30 days** prevents users from rapidly changing passwords several times to bypass password history. This value is intentionally strict for the exercise; production environments normally use a shorter minimum age to avoid blocking legitimate password changes.
- **Minimum length: 8 characters** increases the number of possible password combinations. For production systems, longer password phrases are preferable because length usually provides stronger resistance to guessing and offline cracking than complexity alone.
- **Complexity enabled** requires a mixture of character categories and prevents use of the account name inside the password. This helps reject simple, predictable choices, but it should be combined with longer passwords and known-compromised-password screening.
- **Reversible encryption: Not Defined** means this GPO does not enable that option. Reversible password storage should remain disabled unless a documented legacy requirement makes it unavoidable, because it provides protection similar to storing plaintext credentials.

> A domain password policy reduces the risk of weak passwords, repeated credential reuse, password-history bypass, and long-lived compromised credentials. Linking the policy at the domain root is important because domain-account password settings are processed as a domain-wide security baseline.

![Domain Password Policy Settings](images/11-password-policy/01-domain-password-policy-settings.png)

<p><sub><strong>Screenshot 144 - Domain Password Policy Settings:</strong> Domain password policy settings.</sub></p>

The `PassPolicy` GPO is linked directly beneath `SamuelDomain.com` in Group Policy Management. This placement makes the policy part of the domain-level configuration rather than a setting limited to one workstation or user OU.

![Domain Password Policy Linked](images/11-password-policy/02-password-policy-linked.png)

<p><sub><strong>Screenshot 145 - Domain Password Policy Linked:</strong> PassPolicy linked at the SamuelDomain.com domain level.</sub></p>

## Testing and Verification

- Domain join was validated for Windows Server and Windows 10 systems.
- Active Directory object creation was validated in Active Directory Users and Computers.
- AD synchronization was validated by viewing created objects across both domain controllers.
- Internet connectivity was validated from internal systems through SAMNAT using ping tests.
- DHCP assignment was validated on the Windows 10 client with automatically assigned IP configuration.
- DHCP failover was configured with DC2 as the partner server.
- DNS forwarding and zone behavior were validated with `nslookup`, DNS Manager, and ping tests.
- RDP access was validated from the Windows 10 workstation and through a lab NAT forwarding rule.
- Roaming and mandatory profile behavior was validated through Windows user profile views and server-side profile folders.
- File share permissions were validated through successful and blocked user actions.
- The FSRM hard quota was configured for storage governance.
- GPO hardening was validated by standard-user restrictions and Sys_Admins exception testing.
- Notepad++ was visible on the Windows 10 client after the software-deployment exercise.
- The domain password baseline and its domain-level GPO link were documented.

## Results

The lab produced a working Windows domain infrastructure with redundant domain controller services, centralized DNS and DHCP, outbound NAT routing, domain-based remote administration, profile management, shared file services, GPO hardening, software deployment, and password policy controls.

Additional implementation details and production considerations are documented in [docs/notes.md](docs/notes.md).

## Skills Demonstrated

- Windows Server administration
- Active Directory forest and domain deployment
- Domain controller promotion and replication validation
- FSMO role awareness
- User, group, and OU management
- PowerShell automation for AD object creation
- Legacy `dsadd` command usage
- RRAS NAT/PAT configuration
- DHCP scope and failover configuration
- DNS forwarding, zones, host records, stub zones, and round robin
- Remote administration with RDP
- Roaming and mandatory profile management
- NTFS and share permission design
- Mapped drive scripting
- File Server Resource Manager quotas
- Group Policy hardening
- GPO software deployment
- Password policy administration

## Repository Structure

```text
windows-server-active-directory-lab/
|-- README.md
|-- IMAGE_MANIFEST.md
|-- docs/
|   `-- notes.md
`-- images/
    |-- 01-topology/
    |-- 02-lab-environment/
    |-- 03-active-directory/
    |-- 04-nat-routing/
    |-- 05-dhcp/
    |-- 06-remote-management/
    |-- 07-dns/
    |-- 08-roaming-profiles/
    |-- 09-file-server/
    |-- 10-group-policy-hardening/
    `-- 11-password-policy/
```

## Notes

- Lab credentials and the lab domain remain visible because they belong to the isolated training environment.
- RDP exposure through NAT is documented as a lab-only validation, not as a production recommendation.
