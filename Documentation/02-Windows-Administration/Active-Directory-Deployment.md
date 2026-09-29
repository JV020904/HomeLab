# Active Directory Deployment

## Overview

The HomeLab was expanded to include an Active Directory environment for practicing Windows administration, identity management, DNS, domain authentication, organizational 
units, security groups and Group Policy.

The Active Directory domain is hosted on `LAB-DC01` and uses the internal lab 
network `10.10.10.0/24`.

## Domain Controller

| Setting          | Configuration                          |
| ---------------- | -------------------------------------- |
| Hostname         | `LAB-DC01`                             |
| Operating System | Windows Server                         |
| Internal IP      | `10.10.10.10`                          |
| Domain           | `lab.local`                            |
| DNS Server       | `10.10.10.10`                          |
| Role             | Active Directory Domain Services / DNS |

`LAB-DC01` was configured as the domain controller for the `lab.local` domain.

## DNS Configuration

-The domain environment relies on `LAB-DC01` for internal DNS resolution.

The Windows client was configured to use:

DNS Server: 10.10.10.10


DNS resolution was verified via Powershell using:

nslookup lab.local


-The domain successfully resolved to the domain controller.

-The Active Directory LDAP service was also verified using:
nslookup -type=SRV _ldap._tcp.dc._msdcs.lab.local


This confirmed that the domain controller's Active Directory DNS records were available.

## Domain Workstation

A Windows 11 Enterprise Evaluation virtual machine was created for use as the primary Active Directory client.

| Setting     | Configuration  |
| ----------- | -------------- |
| Hostname    | `WIN11-LAB`    |
| Internal IP | `10.10.10.110` |
| NAT IP      | `10.0.2.15`    |
| DNS         | `10.10.10.10`  |
| Domain      | `lab.local`    |

The workstation was successfully joined to the `lab.local` domain.

Domain membership was verified from the workstation using the following commands:

whoami

and:

(Get-CimInstance Win32_ComputerSystem).Domain

The workstation was subsequently moved from the default `Computers` container into:

lab.local
└── LabComputers
    └── Workstations
        └── WIN11-LAB

## Organizational Units

Custom organizational units were created to organize users, computers, groups and servers within the lab environment.

lab.local
│
├── User Accounts
│   ├── Employees
│   └── IT
│
├── LabComputers
│   └── Workstations
│
├── Groups
│   └── Security Groups
│
├── Servers
│
└── Help Desk

-The `User Accounts` OU was used instead of creating an OU named `Users` because Active Directory already contains a built-in `Users` container at the domain level.

-The built-in `Users` container was left unchanged.



## Domain Users

Several accounts were created for testing identity management and future help-desk scenarios.

### `jvarela`

Location:

```text
User Accounts
└── IT
```

Purpose:

Primary administrative IT test account for the lab.

### `lab-user`

Location:

```text
User Accounts
└── Employees
```

Purpose:

Standard employee account for testing normal user permissions and help-desk scenarios.

### `helpdesk-user`

Location:

```text
User Accounts
└── IT
```

Purpose:

Help-desk technician account for testing delegated administrative permissions.

## Security Groups

The following security groups were created under:

```text
Groups
└── Security Groups
```

### Employees

Used to identify standard employee accounts.

### IT-HelpDesk

Used to identify users who will eventually receive delegated help-desk permissions.

### IT-Admins

Used to identify IT administrative users.

The groups were created as **Global Security Groups**.

## Group Membership

Current membership configuration:

| User            | Group                      |
| --------------- | -------------------------- |
| `lab-user`      | `Employees`                |
| `helpdesk-user` | `Employees`, `IT-HelpDesk` |
| `jvarela`       | `IT-Admins`                |

No custom group was added to `Domain Admins`. Administrative privileges will be delegated deliberately in later stages of the lab rather than granting unnecessary domain-wide privileges.

## Current State

At this stage, the lab has:

* A functioning Active Directory domain
* A Windows Server domain controller
* DNS integrated with Active Directory
* A Windows 11 domain-joined workstation
* Custom organizational units
* Domain user accounts
* Security groups
* Initial group memberships
* A dedicated workstation OU

The next stage will focus on testing domain accounts from `WIN11-LAB`, verifying standard versus administrative permissions and implementing controlled delegation for the help-desk account.
