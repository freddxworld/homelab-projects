# Project 3 - Active Directory Lab

## Overview

The goal of this project is to build a small Active Directory environment using Windows Server and Windows 11 in Proxmox. This lab is designed to simulate a basic enterprise network and provide hands-on experience with common IT administration tasks.

---

## Objectives

- Install and configure Active Directory Domain Services (AD DS)
- Configure DNS
- Create and manage users
- Create and manage groups
- Create Organizational Units (OUs)
- Join a Windows 11 workstation to the domain
- Practice password resets and account management
- Implement basic Group Policy Objects (GPOs)

---

## Lab Environment

### Hypervisor

- Proxmox VE

### Virtual Machines

#### Windows Server 2022

Role:
- Domain Controller
- Active Directory
- DNS Server

Resources:
- 2 vCPU
- 4 GB RAM
- 50 GB Storage

#### Windows 11

Role:
- Domain Joined Workstation

Resources:
- 2 vCPU
- 4 GB RAM
- 50 GB Storage

---

## Planned Domain Structure

Domain:

```text
homelab.local
```

Organizational Units:

```text
Homelab
├── Users
├── Groups
├── Computers
└── Servers
```

Example Users:

```text
Freddy
Alice
Bob
```

Example Groups:

```text
IT_Admins
HR_Users
Helpdesk
```

---

## Tasks Completed

### Infrastructure

- [x] Deploy Windows Server 2022 VM
- [x] Deploy Windows 11 VM

### Active Directory

- [x] Install Active Directory Domain Services
- [x] Promote server to Domain Controller
- [x] Create domain
- [x] Configure DNS

### User Management

- [x] Create users
- [x] Create groups
- [x] Add users to groups
- [x] Reset passwords
- [x] Disable accounts

### Domain Management

- [x] Join Windows 11 to domain
- [x] Verify domain login

### Group Policy

- [x] Create first GPO
- [x] Apply policy to workstation
- [x] Verify policy enforcement

---

## Troubleshooting

See:
- issues.md

---

## Lessons Learned

See:
- lessons-learned.md

---

## Skills Demonstrated

- Active Directory
- Windows Server Administration
- DNS
- User and Group Management
- Domain Services
- Group Policy
- Windows Administration
- Troubleshooting

---

## Resume Bullet

Built a Windows Active Directory lab in Proxmox using Windows Server 2022 and Windows 11. Configured Active Directory Domain Services, DNS, user and group management, domain-joined workstations, and Group Policy to simulate enterprise IT administration tasks.
