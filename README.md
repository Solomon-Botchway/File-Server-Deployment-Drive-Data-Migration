# File-Server-Deployment-Drive-Data-Migration
A dedicated file server (FS01) was deployed to separate business data from the Domain Controller and create a more realistic enterprise architecture.
# Dedicated File Server Deployment & Data Migration

## Project Overview

A dedicated file server (FS01) was deployed to separate business data from the Domain Controller and create a more realistic enterprise architecture.

Prior to the migration, departmental data, software repositories, user home directories, wallpaper resources, screensavers, and archive storage were hosted directly on DC01.

The project migrated these services to a dedicated Windows Server 2022 file server while maintaining user access through Group Policy and Active Directory Security Groups.

---

## Environment

### Before Migration

```text
DC01
├── Active Directory
├── DNS
├── Group Policy
├── Department Shares
├── User Home Folders
├── Software Repository
├── Wallpaper Repository
├── Screensavers
└── Archive Storage
```

### After Migration

```text
DC01
├── Active Directory
├── DNS
└── Group Policy

FS01
├── Department Shares
├── User Home Folders
├── Software Repository
├── Wallpapers
├── Screensavers
└── Archive Storage
```

---

## Infrastructure Built

### File Server

```text
Server Name:
FS01

Operating System:
Windows Server 2022

IP Address:
192.168.10.20
```

### Storage Design

A dedicated data volume was created to separate operating system files from business data.

```text
C:
Operating System

D:
Business Data
```

Folder Structure:

```text
D:\Shares

├── Departments
│   ├── IT_Data
│   ├── HR_Data
│   ├── Finance_Data
│   ├── Sales_Data
│   └── CS_Data

├── UserHomes

├── Archives

├── Software

├── Wallpapers

└── Screensavers
```

---

## Resources Migrated

Successfully migrated:

- Department Drives
- Home Folders
- Software Repository
- Wallpaper Repository
- Screensaver Repository
- Archive Storage

All related Group Policy Objects were updated to point to:

```text
\\FS01\
```

instead of:

```text
\\DC01\
```

---

# Share Permissions vs NTFS Permissions

One of the most important lessons learned during the migration involved understanding how Windows processes network access.

## Local Access

When opening files locally:

```text
D:\Shares\Finance_Data
```

Windows evaluates:

```text
NTFS Permissions Only
```

---

## Network Access

When opening files over the network:

```text
\\FS01\Finance_Data$
```

Windows evaluates:

```text
Share Permissions
AND
NTFS Permissions
```

The most restrictive permission wins.

---

## Issue Discovered

Although Domain Administrators could access departmental folders locally, network access failed.

Example:

```text
SOLOTECH\Administrator

Local Access:
Success

Network Access:
Access Denied
```

---

## Root Cause

Share Permissions only allowed department security groups.

Example:

```text
Finance-Team
```

Domain Administrators were missing from the Share Permissions.

Although NTFS granted access, Share Permissions blocked network access.

---

## Resolution

Share Permissions were redesigned using enterprise best practices.

### Share Permissions

```text
Authenticated Users
    Change
    Read

Domain Admins
    Full Control
```

### NTFS Permissions

Department access controlled exclusively through Active Directory Security Groups.

Example:

```text
Finance-Team
    Modify

Domain Admins
    Full Control

SYSTEM
    Full Control
```

This simplified administration while maintaining security.

---

# Group Policy Updates

Updated:

### Drive Mapping

```text
\\DC01\Finance_Data$
```

to:

```text
\\FS01\Finance_Data$
```

### Home Folders

```text
\\DC01\UserHomes$
```

to:

```text
\\FS01\UserHomes$
```

### Software Deployment

```text
\\DC01\Software$
```

to:

```text
\\FS01\Software$
```

### Wallpapers

```text
\\FS01\Wallpapers$
```

### Screensavers

```text
\\FS01\Screensavers$
```

---

# Validation

Successfully verified:

✅ Department drives mapped from FS01

✅ Home folders accessible from FS01

✅ Software deployment functioning

✅ Wallpapers deployed correctly

✅ Screensavers applied correctly

✅ Archive access functioning

✅ Security group permissions retained

✅ Domain Administrator network access restored

---

# Technical Skills Demonstrated

### Infrastructure

- Windows Server 2022
- Active Directory
- File Server Administration
- Storage Design

### File Services

- Share Permissions
- NTFS Permissions
- Hidden Administrative Shares
- Data Migration

### Group Policy

- Drive Mapping
- Software Deployment
- Wallpaper Deployment
- Screensaver Deployment

### Security

- Role-Based Access Control (RBAC)
- Least Privilege
- Permission Auditing
- Access Validation

### Troubleshooting

- Firewall Configuration
- DNS Registration
- Share Permission Conflicts
- Access Denied Investigations
- GPO Path Validation

--- 

## Discovery

 ```markdown
## Migration Issue Encountered

During migration from DC01 to FS01, Domain Administrators could access the folders locally but were denied access over the network.

### Root Cause
Domain Administrators were present in NTFS permissions but missing from Share Permissions.

## Resolution Implemented:
### Share Permissions Authenticated Users Read Change Domain Admins Full Control
### NTFS Permissions Finance-Team Modify Domain Admins Full Control SYSTEM Full Control
