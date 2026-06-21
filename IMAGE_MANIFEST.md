# Image Manifest

This manifest lists every evidence image included in the repository. Images are organized in the same technical order as the lab workflow.

| Section | Count |
|---------|------:|
| Topology | 1 |
| Lab Environment | 5 |
| Active Directory | 23 |
| NAT and Routing | 11 |
| DHCP | 17 |
| Remote Management | 7 |
| DNS | 27 |
| Roaming Profiles | 12 |
| File Server | 23 |
| Group Policy Hardening | 16 |
| Password Policy | 5 |
| **Total** | **147** |

## Topology

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 001 | [`images/01-topology/01-network-topology.png`](images/01-topology/01-network-topology.png) | Network topology with DC1, DC2, NAT server, Win10 client, LAN and internet path |

## Lab Environment

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 002 | [`images/02-lab-environment/01-lab-addressing-plan.png`](images/02-lab-environment/01-lab-addressing-plan.png) | Lab addressing plan, roles, hostnames, domain and admin account details |
| Screenshot 003 | [`images/02-lab-environment/02-vm-inventory.png`](images/02-lab-environment/02-vm-inventory.png) | VMware inventory showing lab virtual machines |
| Screenshot 004 | [`images/02-lab-environment/03-windows-server-iso-selection.png`](images/02-lab-environment/03-windows-server-iso-selection.png) | Windows Server 2019 ISO selection during installation |
| Screenshot 005 | [`images/02-lab-environment/04-windows-server-language-setup.png`](images/02-lab-environment/04-windows-server-language-setup.png) | Windows Server setup language and keyboard configuration |
| Screenshot 006 | [`images/02-lab-environment/05-vmware-tools-installation.png`](images/02-lab-environment/05-vmware-tools-installation.png) | VMware Tools installation option |

## Active Directory

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 007 | [`images/03-active-directory/01-dc1-computer-name.png`](images/03-active-directory/01-dc1-computer-name.png) | DC1 computer rename before domain promotion |
| Screenshot 008 | [`images/03-active-directory/02-add-ad-ds-role.png`](images/03-active-directory/02-add-ad-ds-role.png) | Adding Active Directory Domain Services and DNS roles |
| Screenshot 009 | [`images/03-active-directory/03-server-manager-add-roles.png`](images/03-active-directory/03-server-manager-add-roles.png) | Server Manager Add Roles and Features entry point |
| Screenshot 010 | [`images/03-active-directory/04-new-forest-root-domain.png`](images/03-active-directory/04-new-forest-root-domain.png) | New forest deployment with SamuelDomain.com root domain |
| Screenshot 011 | [`images/03-active-directory/05-ad-ds-paths.png`](images/03-active-directory/05-ad-ds-paths.png) | AD DS database, log and SYSVOL paths |
| Screenshot 012 | [`images/03-active-directory/06-create-controlled-admin-account.png`](images/03-active-directory/06-create-controlled-admin-account.png) | Creating a controlled admin account from the built-in Administrator account |
| Screenshot 013 | [`images/03-active-directory/07-disable-built-in-administrator.png`](images/03-active-directory/07-disable-built-in-administrator.png) | DC2 computer name and domain membership dialog during the domain-join workflow |
| Screenshot 014 | [`images/03-active-directory/08-dc2-static-ip-primary-dns.png`](images/03-active-directory/08-dc2-static-ip-primary-dns.png) | DC2 static IP and DNS configuration |
| Screenshot 015 | [`images/03-active-directory/09-samnat-static-ip-primary-dns.png`](images/03-active-directory/09-samnat-static-ip-primary-dns.png) | SAMNAT static IP and DNS configuration |
| Screenshot 016 | [`images/03-active-directory/10-promote-dc2-existing-domain.png`](images/03-active-directory/10-promote-dc2-existing-domain.png) | DC2 promotion into the existing domain |
| Screenshot 017 | [`images/03-active-directory/11-dc2-ad-ds-role-selection.png`](images/03-active-directory/11-dc2-ad-ds-role-selection.png) | AD DS role installation for DC2 |
| Screenshot 018 | [`images/03-active-directory/12-operations-master-window.png`](images/03-active-directory/12-operations-master-window.png) | Operations Master window before RID role transfer |
| Screenshot 019 | [`images/03-active-directory/13-transfer-rid-master-to-dc2.png`](images/03-active-directory/13-transfer-rid-master-to-dc2.png) | RID Master role transfer confirmation |
| Screenshot 020 | [`images/03-active-directory/14-fsmo-roles-overview.png`](images/03-active-directory/14-fsmo-roles-overview.png) | FSMO role overview diagram |
| Screenshot 021 | [`images/03-active-directory/15-sales-and-sysadmins-groups.png`](images/03-active-directory/15-sales-and-sysadmins-groups.png) | Sales and Sys_Admins group membership |
| Screenshot 022 | [`images/03-active-directory/16-add-sysadmins-to-domain-admins.png`](images/03-active-directory/16-add-sysadmins-to-domain-admins.png) | Adding Sys_Admins to Domain Admins |
| Screenshot 023 | [`images/03-active-directory/17-dsadd-users-and-groups-script-run.png`](images/03-active-directory/17-dsadd-users-and-groups-script-run.png) | DSADD script execution and created groups/users |
| Screenshot 024 | [`images/03-active-directory/18-bulk-dsadd-users-from-script.png`](images/03-active-directory/18-bulk-dsadd-users-from-script.png) | Bulk DSADD user creation from generated script |
| Screenshot 025 | [`images/03-active-directory/19-powershell-created-ou-user-group.png`](images/03-active-directory/19-powershell-created-ou-user-group.png) | PowerShell-created OU, user and group in ADUC |
| Screenshot 026 | [`images/03-active-directory/20-powershell-ad-object-creation-script.png`](images/03-active-directory/20-powershell-ad-object-creation-script.png) | PowerShell script for OU, user and group creation |
| Screenshot 027 | [`images/03-active-directory/21-powershell-bulk-user-loop.png`](images/03-active-directory/21-powershell-bulk-user-loop.png) | PowerShell loop for bulk AD user creation |
| Screenshot 028 | [`images/03-active-directory/22-powershell-created-users.png`](images/03-active-directory/22-powershell-created-users.png) | Created user objects after PowerShell bulk run |
| Screenshot 029 | [`images/03-active-directory/23-ad-replication-visible-on-both-dcs.png`](images/03-active-directory/23-ad-replication-visible-on-both-dcs.png) | AD object synchronization visible on both domain controllers |

## NAT and Routing

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 030 | [`images/04-nat-routing/01-samnat-domain-join-and-nic-setup.png`](images/04-nat-routing/01-samnat-domain-join-and-nic-setup.png) | SAMNAT domain membership and NIC setup |
| Screenshot 031 | [`images/04-nat-routing/02-wan-interface-ip-address.png`](images/04-nat-routing/02-wan-interface-ip-address.png) | WAN interface IPv4 address on SAMNAT |
| Screenshot 032 | [`images/04-nat-routing/03-lan-interface-ip-address.png`](images/04-nat-routing/03-lan-interface-ip-address.png) | LAN interface IPv4 address on SAMNAT |
| Screenshot 033 | [`images/04-nat-routing/04-samnat-routing-topology.png`](images/04-nat-routing/04-samnat-routing-topology.png) | SAMNAT routing topology with LAN and bridged WAN |
| Screenshot 034 | [`images/04-nat-routing/05-remote-access-role-selection.png`](images/04-nat-routing/05-remote-access-role-selection.png) | Remote Access role selected for NAT/RRAS |
| Screenshot 035 | [`images/04-nat-routing/06-routing-and-remote-access-tool.png`](images/04-nat-routing/06-routing-and-remote-access-tool.png) | Routing and Remote Access management tool |
| Screenshot 036 | [`images/04-nat-routing/07-nat-and-lan-routing-selection.png`](images/04-nat-routing/07-nat-and-lan-routing-selection.png) | RRAS custom configuration with NAT and LAN routing |
| Screenshot 037 | [`images/04-nat-routing/08-nat-public-interface-configuration.png`](images/04-nat-routing/08-nat-public-interface-configuration.png) | NAT public interface configuration |
| Screenshot 038 | [`images/04-nat-routing/09-dc1-internet-connectivity-test.png`](images/04-nat-routing/09-dc1-internet-connectivity-test.png) | DC1 ping test to public DNS through NAT |
| Screenshot 039 | [`images/04-nat-routing/10-dc2-internet-connectivity-test.png`](images/04-nat-routing/10-dc2-internet-connectivity-test.png) | DC2 ping test through NAT |
| Screenshot 040 | [`images/04-nat-routing/11-win10-internet-connectivity-test.png`](images/04-nat-routing/11-win10-internet-connectivity-test.png) | Windows 10 ping test through NAT |

## DHCP

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 041 | [`images/05-dhcp/01-open-add-roles-for-dhcp.png`](images/05-dhcp/01-open-add-roles-for-dhcp.png) | Opening Add Roles and Features for DHCP |
| Screenshot 042 | [`images/05-dhcp/02-select-dhcp-server-role.png`](images/05-dhcp/02-select-dhcp-server-role.png) | DHCP Server role selection |
| Screenshot 043 | [`images/05-dhcp/03-dhcp-installation-complete.png`](images/05-dhcp/03-dhcp-installation-complete.png) | DHCP installation completion prompt |
| Screenshot 044 | [`images/05-dhcp/04-dhcp-authorization.png`](images/05-dhcp/04-dhcp-authorization.png) | DHCP authorization using domain credentials |
| Screenshot 045 | [`images/05-dhcp/05-dhcp-manager-opened.png`](images/05-dhcp/05-dhcp-manager-opened.png) | DHCP Manager available in Server Manager |
| Screenshot 046 | [`images/05-dhcp/06-new-dhcp-scope.png`](images/05-dhcp/06-new-dhcp-scope.png) | New DHCP scope creation |
| Screenshot 047 | [`images/05-dhcp/07-dhcp-scope-address-range.png`](images/05-dhcp/07-dhcp-scope-address-range.png) | DHCP scope address range configuration |
| Screenshot 048 | [`images/05-dhcp/08-dhcp-exclusion-range.png`](images/05-dhcp/08-dhcp-exclusion-range.png) | DHCP exclusion range configuration |
| Screenshot 049 | [`images/05-dhcp/09-dhcp-lease-duration.png`](images/05-dhcp/09-dhcp-lease-duration.png) | DHCP lease duration set to eight hours |
| Screenshot 050 | [`images/05-dhcp/10-dhcp-dora-process.png`](images/05-dhcp/10-dhcp-dora-process.png) | DORA process reference diagram |
| Screenshot 051 | [`images/05-dhcp/11-default-gateway-option.png`](images/05-dhcp/11-default-gateway-option.png) | DHCP default gateway option |
| Screenshot 052 | [`images/05-dhcp/12-dns-server-option.png`](images/05-dhcp/12-dns-server-option.png) | DHCP DNS server option |
| Screenshot 053 | [`images/05-dhcp/13-win10-dhcp-address-validation.png`](images/05-dhcp/13-win10-dhcp-address-validation.png) | Windows 10 receiving automatic DHCP address |
| Screenshot 054 | [`images/05-dhcp/14-network-connectivity-validation.png`](images/05-dhcp/14-network-connectivity-validation.png) | Connectivity validation across the lab network |
| Screenshot 055 | [`images/05-dhcp/15-dhcp-role-on-dc2.png`](images/05-dhcp/15-dhcp-role-on-dc2.png) | DHCP Server role installation on DC2 |
| Screenshot 056 | [`images/05-dhcp/16-dhcp-failover-partner-server.png`](images/05-dhcp/16-dhcp-failover-partner-server.png) | DHCP failover partner server selection |
| Screenshot 057 | [`images/05-dhcp/17-dhcp-failover-relationship.png`](images/05-dhcp/17-dhcp-failover-relationship.png) | DHCP failover relationship settings |

## Remote Management

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 058 | [`images/06-remote-management/01-enable-remote-desktop-for-sysadmins.png`](images/06-remote-management/01-enable-remote-desktop-for-sysadmins.png) | Remote Desktop enabled for the Sys_Admins group |
| Screenshot 059 | [`images/06-remote-management/02-sysadmins-group-membership.png`](images/06-remote-management/02-sysadmins-group-membership.png) | Sys_Admins group membership used for remote administration |
| Screenshot 060 | [`images/06-remote-management/03-samnat-local-server-status.png`](images/06-remote-management/03-samnat-local-server-status.png) | SAMNAT local server properties showing remote management settings |
| Screenshot 061 | [`images/06-remote-management/04-rdp-connection-as-user3.png`](images/06-remote-management/04-rdp-connection-as-user3.png) | RDP connection from Windows 10 as User 3 |
| Screenshot 062 | [`images/06-remote-management/05-samnat-wan-interface-ip.png`](images/06-remote-management/05-samnat-wan-interface-ip.png) | SAMNAT WAN interface IP used for port forwarding test |
| Screenshot 063 | [`images/06-remote-management/06-nat-rdp-port-forward-rule.png`](images/06-remote-management/06-nat-rdp-port-forward-rule.png) | NAT port forwarding rule for RDP lab access |
| Screenshot 064 | [`images/06-remote-management/07-rdp-port-forward-validation.png`](images/06-remote-management/07-rdp-port-forward-validation.png) | External RDP validation through the forwarded lab port |

## DNS

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 065 | [`images/07-dns/01-dns-hierarchy-reference.png`](images/07-dns/01-dns-hierarchy-reference.png) | DNS hierarchy reference diagram |
| Screenshot 066 | [`images/07-dns/02-samnat-dns-client-settings.png`](images/07-dns/02-samnat-dns-client-settings.png) | SAMNAT DNS client settings |
| Screenshot 067 | [`images/07-dns/03-dc1-dns-client-settings.png`](images/07-dns/03-dc1-dns-client-settings.png) | DC1 DNS client settings |
| Screenshot 068 | [`images/07-dns/04-dc2-dns-client-settings.png`](images/07-dns/04-dc2-dns-client-settings.png) | DC2 DNS client settings |
| Screenshot 069 | [`images/07-dns/05-win10-dns-client-settings.png`](images/07-dns/05-win10-dns-client-settings.png) | Windows 10 DNS client settings |
| Screenshot 070 | [`images/07-dns/06-dns-forwarding-topology.png`](images/07-dns/06-dns-forwarding-topology.png) | DNS forwarding topology |
| Screenshot 071 | [`images/07-dns/07-dc2-external-forwarder.png`](images/07-dns/07-dc2-external-forwarder.png) | DC2 external DNS forwarder |
| Screenshot 072 | [`images/07-dns/08-dc1-external-forwarder.png`](images/07-dns/08-dc1-external-forwarder.png) | DC1 external DNS forwarder |
| Screenshot 073 | [`images/07-dns/09-new-zone-wizard.png`](images/07-dns/09-new-zone-wizard.png) | New zone wizard for DNS management |
| Screenshot 074 | [`images/07-dns/10-facebook-primary-zone-type.png`](images/07-dns/10-facebook-primary-zone-type.png) | Primary zone selection for Facebook blocking lab |
| Screenshot 075 | [`images/07-dns/11-facebook-zone-name.png`](images/07-dns/11-facebook-zone-name.png) | Facebook.com zone name configuration |
| Screenshot 076 | [`images/07-dns/12-add-facebook-host-record.png`](images/07-dns/12-add-facebook-host-record.png) | New host record for Facebook blocking test |
| Screenshot 077 | [`images/07-dns/13-facebook-resolution-block-validation.png`](images/07-dns/13-facebook-resolution-block-validation.png) | Client validation showing blocked/unreachable Facebook resolution |
| Screenshot 078 | [`images/07-dns/14-google-nslookup-output.png`](images/07-dns/14-google-nslookup-output.png) | nslookup output for Google DNS records |
| Screenshot 079 | [`images/07-dns/15-google-conditional-forwarder.png`](images/07-dns/15-google-conditional-forwarder.png) | Google conditional forwarder configured with master server |
| Screenshot 080 | [`images/07-dns/16-yahoo-nslookup-name-servers.png`](images/07-dns/16-yahoo-nslookup-name-servers.png) | Yahoo name server discovery with nslookup |
| Screenshot 081 | [`images/07-dns/17-yahoo-stub-zone-master-servers.png`](images/07-dns/17-yahoo-stub-zone-master-servers.png) | Stub zone master servers for Yahoo |
| Screenshot 082 | [`images/07-dns/18-dns-zones-overview.png`](images/07-dns/18-dns-zones-overview.png) | DNS zones overview after configuration |
| Screenshot 083 | [`images/07-dns/19-sam53-primary-zone.png`](images/07-dns/19-sam53-primary-zone.png) | Primary zone selection for SAM53 |
| Screenshot 084 | [`images/07-dns/20-create-secondary-zone-on-dc2.png`](images/07-dns/20-create-secondary-zone-on-dc2.png) | Secondary zone creation on DC2 |
| Screenshot 085 | [`images/07-dns/21-secondary-zone-master-ip.png`](images/07-dns/21-secondary-zone-master-ip.png) | Primary DNS server IP used for secondary zone transfer |
| Screenshot 086 | [`images/07-dns/22-secondary-zone-visible-on-dc2.png`](images/07-dns/22-secondary-zone-visible-on-dc2.png) | Secondary zone visible on DC2 |
| Screenshot 087 | [`images/07-dns/23-mail-a-record-created.png`](images/07-dns/23-mail-a-record-created.png) | Mail A record created for lab name-resolution validation |
| Screenshot 088 | [`images/07-dns/24-mail-record-ping-validation.png`](images/07-dns/24-mail-record-ping-validation.png) | Ping validation for the mail host record |
| Screenshot 089 | [`images/07-dns/25-round-robin-host-records.png`](images/07-dns/25-round-robin-host-records.png) | Multiple host records used for DNS round robin |
| Screenshot 090 | [`images/07-dns/26-round-robin-enabled.png`](images/07-dns/26-round-robin-enabled.png) | DNS round robin enabled |
| Screenshot 091 | [`images/07-dns/27-round-robin-validation.png`](images/07-dns/27-round-robin-validation.png) | Round robin validation showing changing returned addresses |

## Roaming Profiles

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 092 | [`images/08-roaming-profiles/01-profile-folder-created.png`](images/08-roaming-profiles/01-profile-folder-created.png) | Roaming profile root folder created on DC1 |
| Screenshot 093 | [`images/08-roaming-profiles/02-profile-folder-disable-inheritance.png`](images/08-roaming-profiles/02-profile-folder-disable-inheritance.png) | Profile folder permissions before disabling inheritance |
| Screenshot 094 | [`images/08-roaming-profiles/03-share-permissions-for-profile-folder.png`](images/08-roaming-profiles/03-share-permissions-for-profile-folder.png) | Share permissions for profile folder |
| Screenshot 095 | [`images/08-roaming-profiles/04-advanced-sharing-for-profile-folder.png`](images/08-roaming-profiles/04-advanced-sharing-for-profile-folder.png) | Advanced sharing configuration for profile folder |
| Screenshot 096 | [`images/08-roaming-profiles/05-ad-profile-path.png`](images/08-roaming-profiles/05-ad-profile-path.png) | Active Directory profile path configured for user |
| Screenshot 097 | [`images/08-roaming-profiles/06-user-profile-folder-created.png`](images/08-roaming-profiles/06-user-profile-folder-created.png) | User profile folders created after logon |
| Screenshot 098 | [`images/08-roaming-profiles/07-roaming-profile-visible-on-client.png`](images/08-roaming-profiles/07-roaming-profile-visible-on-client.png) | Roaming profile visible in Windows user profiles |
| Screenshot 099 | [`images/08-roaming-profiles/08-admin-access-owner-change-required.png`](images/08-roaming-profiles/08-admin-access-owner-change-required.png) | Owner change required for administrative profile access |
| Screenshot 100 | [`images/08-roaming-profiles/09-domain-admin-owner-set.png`](images/08-roaming-profiles/09-domain-admin-owner-set.png) | Domain admin ownership applied to profile folder |
| Screenshot 101 | [`images/08-roaming-profiles/10-profile-folder-permissions-updated.png`](images/08-roaming-profiles/10-profile-folder-permissions-updated.png) | Profile folder permissions updated |
| Screenshot 102 | [`images/08-roaming-profiles/11-ntuser-man-mandatory-profile.png`](images/08-roaming-profiles/11-ntuser-man-mandatory-profile.png) | NTUSER.MAN conversion for mandatory profile |
| Screenshot 103 | [`images/08-roaming-profiles/12-mandatory-profile-visible.png`](images/08-roaming-profiles/12-mandatory-profile-visible.png) | Mandatory profile visible on client |

## File Server

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 104 | [`images/09-file-server/01-file-server-role-selection.png`](images/09-file-server/01-file-server-role-selection.png) | File Server role selected on DC2 |
| Screenshot 105 | [`images/09-file-server/02-home-folder-root-created.png`](images/09-file-server/02-home-folder-root-created.png) | Home folder root created on DC2 |
| Screenshot 106 | [`images/09-file-server/03-home-folder-share-permissions.png`](images/09-file-server/03-home-folder-share-permissions.png) | Home folder share permissions |
| Screenshot 107 | [`images/09-file-server/04-home-folder-ntfs-permissions.png`](images/09-file-server/04-home-folder-ntfs-permissions.png) | Home folder NTFS permissions after inheritance change |
| Screenshot 108 | [`images/09-file-server/05-ad-home-folder-mapping.png`](images/09-file-server/05-ad-home-folder-mapping.png) | Home folder mapping configured in AD user properties |
| Screenshot 109 | [`images/09-file-server/06-user-home-folders-created.png`](images/09-file-server/06-user-home-folders-created.png) | User home folders created automatically |
| Screenshot 110 | [`images/09-file-server/07-mapped-home-drive-visible.png`](images/09-file-server/07-mapped-home-drive-visible.png) | Mapped home drive visible on the client |
| Screenshot 111 | [`images/09-file-server/08-data-share-created.png`](images/09-file-server/08-data-share-created.png) | DATA share creation and sharing settings |
| Screenshot 112 | [`images/09-file-server/09-data-share-permissions-baseline.png`](images/09-file-server/09-data-share-permissions-baseline.png) | DATA share permissions baseline |
| Screenshot 113 | [`images/09-file-server/10-data-share-test-file.png`](images/09-file-server/10-data-share-test-file.png) | Test text file created in DATA share |
| Screenshot 114 | [`images/09-file-server/11-sysadmins-modify-permission.png`](images/09-file-server/11-sysadmins-modify-permission.png) | Sys_Admins Modify permission on DATA share |
| Screenshot 115 | [`images/09-file-server/12-sales-read-execute-permission.png`](images/09-file-server/12-sales-read-execute-permission.png) | Sales group Read and Execute permission on DATA share |
| Screenshot 116 | [`images/09-file-server/13-sysadmin-create-file-test.png`](images/09-file-server/13-sysadmin-create-file-test.png) | Sys_Admins file creation test |
| Screenshot 117 | [`images/09-file-server/14-sales-delete-denied-test.png`](images/09-file-server/14-sales-delete-denied-test.png) | Sales user denied delete operation |
| Screenshot 118 | [`images/09-file-server/15-script-share-everyone-modify.png`](images/09-file-server/15-script-share-everyone-modify.png) | Script share with Everyone Modify permission in the lab |
| Screenshot 119 | [`images/09-file-server/16-script-share-network-path.png`](images/09-file-server/16-script-share-network-path.png) | Script share network path |
| Screenshot 120 | [`images/09-file-server/17-net-use-batch-script.png`](images/09-file-server/17-net-use-batch-script.png) | Batch script using net use to map DATA share |
| Screenshot 121 | [`images/09-file-server/18-script-mapped-drive-validation.png`](images/09-file-server/18-script-mapped-drive-validation.png) | Mapped drive validation after running script |
| Screenshot 122 | [`images/09-file-server/19-fsrm-role-selection.png`](images/09-file-server/19-fsrm-role-selection.png) | File Server Resource Manager role selection |
| Screenshot 123 | [`images/09-file-server/20-create-home-folder-quota.png`](images/09-file-server/20-create-home-folder-quota.png) | Creating quota for home folders |
| Screenshot 124 | [`images/09-file-server/21-hard-quota-5gb.png`](images/09-file-server/21-hard-quota-5gb.png) | Hard quota configured for 5 GB limit |
| Screenshot 125 | [`images/09-file-server/22-create-avi-file-screen.png`](images/09-file-server/22-create-avi-file-screen.png) | Creating file screen for AVI blocking |
| Screenshot 126 | [`images/09-file-server/23-avi-file-screen-settings.png`](images/09-file-server/23-avi-file-screen-settings.png) | AVI file screen settings |

## Group Policy Hardening

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 127 | [`images/10-group-policy-hardening/01-group-policy-management-opened.png`](images/10-group-policy-hardening/01-group-policy-management-opened.png) | Group Policy Management opened |
| Screenshot 128 | [`images/10-group-policy-hardening/02-gpo-list-overview.png`](images/10-group-policy-hardening/02-gpo-list-overview.png) | GPO list overview |
| Screenshot 129 | [`images/10-group-policy-hardening/03-create-new-hardening-gpo.png`](images/10-group-policy-hardening/03-create-new-hardening-gpo.png) | Creating a new hardening GPO |
| Screenshot 130 | [`images/10-group-policy-hardening/04-hardening-gpo-editor-settings.png`](images/10-group-policy-hardening/04-hardening-gpo-editor-settings.png) | Hardening GPO editor settings |
| Screenshot 131 | [`images/10-group-policy-hardening/05-removable-storage-policy.png`](images/10-group-policy-hardening/05-removable-storage-policy.png) | Removable storage policy configuration |
| Screenshot 132 | [`images/10-group-policy-hardening/06-sysadmins-command-prompt-exception.png`](images/10-group-policy-hardening/06-sysadmins-command-prompt-exception.png) | Sys_Admins exception policy for command prompt |
| Screenshot 133 | [`images/10-group-policy-hardening/07-sysadmins-control-panel-exception.png`](images/10-group-policy-hardening/07-sysadmins-control-panel-exception.png) | Sys_Admins exception policy for Control Panel |
| Screenshot 134 | [`images/10-group-policy-hardening/08-removable-storage-deny-policy.png`](images/10-group-policy-hardening/08-removable-storage-deny-policy.png) | Removable storage deny policy |
| Screenshot 135 | [`images/10-group-policy-hardening/09-gpo-linking-overview.png`](images/10-group-policy-hardening/09-gpo-linking-overview.png) | GPO linking overview |
| Screenshot 136 | [`images/10-group-policy-hardening/10-add-sysadmins-to-local-admins-gpo.png`](images/10-group-policy-hardening/10-add-sysadmins-to-local-admins-gpo.png) | GPO used to add Sys_Admins to local Administrators |
| Screenshot 137 | [`images/10-group-policy-hardening/11-restricted-groups-membership.png`](images/10-group-policy-hardening/11-restricted-groups-membership.png) | Restricted Groups membership configuration |
| Screenshot 138 | [`images/10-group-policy-hardening/12-standard-user-control-panel-blocked.png`](images/10-group-policy-hardening/12-standard-user-control-panel-blocked.png) | Standard user blocked from Control Panel |
| Screenshot 139 | [`images/10-group-policy-hardening/13-sysadmin-access-validation.png`](images/10-group-policy-hardening/13-sysadmin-access-validation.png) | Sys_Admins access validation |
| Screenshot 140 | [`images/10-group-policy-hardening/14-notepad-msi-package.png`](images/10-group-policy-hardening/14-notepad-msi-package.png) | Notepad++ MSI package prepared for deployment |
| Screenshot 141 | [`images/10-group-policy-hardening/15-software-installation-assigned.png`](images/10-group-policy-hardening/15-software-installation-assigned.png) | Software installation package assigned through GPO |
| Screenshot 142 | [`images/10-group-policy-hardening/16-software-deployment-validation.png`](images/10-group-policy-hardening/16-software-deployment-validation.png) | Notepad++ deployment validation on Windows 10 |

## Password Policy

| Screenshot | Image | Description |
|------------|-------|-------------|
| Screenshot 143 | [`images/11-password-policy/01-domain-password-policy-settings.png`](images/11-password-policy/01-domain-password-policy-settings.png) | Domain password policy settings |
| Screenshot 144 | [`images/11-password-policy/02-password-policy-linked.png`](images/11-password-policy/02-password-policy-linked.png) | Password policy linked in Group Policy Management |
| Screenshot 145 | [`images/11-password-policy/03-sales-password-policy-settings.png`](images/11-password-policy/03-sales-password-policy-settings.png) | Sales-specific password policy settings |
| Screenshot 146 | [`images/11-password-policy/04-default-policy-unlinked-for-demo.png`](images/11-password-policy/04-default-policy-unlinked-for-demo.png) | Default policy temporarily unlinked during Sales policy demonstration |
| Screenshot 147 | [`images/11-password-policy/05-password-change-validation.png`](images/11-password-policy/05-password-change-validation.png) | Password change validation for Sales policy demonstration |
