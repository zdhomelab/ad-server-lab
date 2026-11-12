# Windows Server Domain Lab: File, Print and Backup Servers

A home-lab project that adds three Windows Server 2025 member servers to an existing Active Directory domain (`zeroday.local`). The servers run as VMs on a Proxmox mini PC.

| Server | Role | Notes |
|---|---|---|
| SRV-DC01 | Domain controller and DNS | Existing, on pve01 |
| SRV-FS01 | File server | Department shares secured with AD groups (AGDLP) |
| SRV-PS01 | Print server | HP printer published to AD and deployed by Group Policy |
| SRV-BK01 | Backup server | System State backup of the DC, AD Recycle Bin |

## What this project shows

- Installing Proxmox VE on a mini PC and creating Windows Server VMs with VirtIO drivers
- Static IP configuration and joining servers to an Active Directory domain
- Department file shares using global groups (GG-) nested in domain local groups (DL-), with share and NTFS permissions
- Installing a printer through an IP port with a vendor driver, publishing it to AD and deploying it with Group Policy
- Locking down a backup share to a domain controller computer account
- Taking a System State backup with Windows Server Backup and enabling the AD Recycle Bin
- Testing with a restore of a deleted user

## Phases

1. **Proxmox install** on the mini PC (pve02), static host IP, ISO storage.
2. **VM creation** for SRV-FS01, SRV-PS01, SRV-BK01 (VirtIO disk and network, host CPU type).
3. **Windows install, static IP, domain join**, and a Lab-Servers OU.
4. **File server**: 50 GB data disk as D:, File Server and FSRM roles, AGDLP groups, HR share, NTFS permissions, client tests.
5. **Print server**: Print and Document Services, HP printer by IP, share and AD publish, printer security, Group Policy deployment with security filtering, client test.
6. **Backup server**: 150 GB backup disk as E:, locked-down `backups` share, System State backup, AD Recycle Bin, delete-and-restore test.

The full step-by-step guide is in the Word document `Windows_Server_Domain_Implementation_Guide.docx`.

## Key design decisions

- **AGDLP model:** users go in GG- global groups, global groups go in DL- domain local groups, and DL- groups receive NTFS and share permissions.
- **Inheritance disabled on the HR folder.** SYSTEM was removed with the inherited permissions and added back with Full control, so Windows and backup tools keep access.
- **Group Policy security filtering:** Domain Computers (read) plus GG-HR. Authenticated Users was removed only after Domain Computers was added, so computers can still read the policy.
- **Printer driver:** the Microsoft IPP class driver did not install this HP model, so the HP driver package was used with Have Disk.
- **Backup isolation:** the DC backs up to a different physical host. The backup share gives Administrators Full control, SYSTEM Full control, and the DC computer account (SRV-DC01$) Modify only.

## Screenshots

### 1. Proxmox hosts
![Proxmox hosts](Screenshots/01a-proxmox-pve01-hosts.png)

### 2. pve02 hostname and static IP
![pve02 hostname and IP](Screenshots/02-pve02-hostname-ip.png)

### 3 and 6. pve02 dashboard and VM list
![pve02 dashboard](Screenshots/01b-proxmox-pve02-summary.png)

### 4. ISO images in storage
![ISO storage](Screenshots/04-proxmox-iso-storage.png)

### 5. SRV-FS01 hardware
![SRV-FS01 hardware](Screenshots/05-srv-fs01-hardware.png)

### 7. Static IP (ipconfig /all)
![ipconfig](Screenshots/07-srv-fs01-ipconfig.png)

### 8. Domain membership
![Domain membership](Screenshots/08-srv-fs01-about.png)

### 9. Lab-Servers OU
![Lab-Servers OU](Screenshots/09-ad-lab-servers-ou.png)

### 10. File Server role
![File Server role](Screenshots/10-file-server-role.png)

### 11. HR share permissions
![HR share permissions](Screenshots/11-hr-share-permissions.png)

### 12. HR NTFS advanced permissions
![HR NTFS permissions](Screenshots/12-hr-ntfs-advanced.png)

### 14. HR share from a client
![HR share from client](Screenshots/14-hr-share-from-client.png)

### 15. Print and Document Services
![Print role](Screenshots/15-print-role.png)

### 16. HP Printer in Print Management
![HP Printer list](Screenshots/16-hp-printer-list.png)

### 17. Printer security
![Printer security](Screenshots/17-hp-printer-security.png)

### 18. Printer for hr user01
![Printer for hr user01](Screenshots/18-printer-hr-user01.png)

### 18b. Printer default on client
![Printer default](Screenshots/18b-printer-default-client.png)

### 19a. Backup disk E:
![Backup disk](Screenshots/19a-backup-disk.png)

### 19. Backup share permissions
![Backup share permissions](Screenshots/19-backup-share-permissions.png)

### 20. System State backup result
![System State backup](Screenshots/20-system-state-backup.png)

### 21. AD Recycle Bin
![Recycle Bin](Screenshots/21-recycle-bin-enabled.png)

### 22. Deleted user
![Deleted user](Screenshots/22-deleted-user.png)

### 23. Access denied on Finance
![Access denied](Screenshots/23-access-denied-finance.png)

### 24. Restored user
![Restored user](Screenshots/24-user-restored.png)


## Requirements

- Proxmox VE 9.x
- Windows Server 2025 Standard Evaluation (180 days)
- virtio-win drivers
- An HP Smart Tank 650 or a similar IPP-capable printer, with its driver package

## Licence

Lab documentation for personal and educational use.
