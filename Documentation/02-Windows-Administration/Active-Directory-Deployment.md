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
| Operating System | Windows Server 2022 Standard Eval      |
| Internal IP      | `10.10.10.10`                          |
| NAT IP           | `10.0.2.15`                            |
| Domain           | `lab.local`                            |
| DNS Server       | `10.10.10.10`                          |
| Roles            | Active Directory Domain Services, DNS  |


    LAB-DC01 was configured as the domain controller for the lab.local domain. It provides
centralized identity management, domain authentication and internal DNS services.
    The host-only interface connects the domain controller to the internal lab network, 
while the NAT interface provides a separate path for network access.

## DNS Configuration

-The domain environment relies on `LAB-DC01` for internal DNS resolution.

-The Windows client is configured to use 10.10.10.10 as its DNS server.


DNS resolution was tested via Powershell and Command Prompt using:

nslookup lab.local
nslookup LAB-DC01.lab.local
nslookup -type=SRV _ldap._tcp.dc._msdcs.lab.local

    These queries were used to verify domain name resolution, domain controller name resolution and the availability of Active Directory service records.

    The domain controller's DNS service was also included in the service health checks.

    Network note: The domain controller has both NAT and host-only network interfaces. DNS responses may contain multiple addresses, so client-facing name resolution should be checked to ensure that domain clients can reach the domain controller through 
the internal lab network.


## Domain Workstation

A Windows 11 Enterprise Evaluation virtual machine was created for use as the primary Active Directory client.

| Setting     | Configuration  |
| ----------- | -------------- |
| Hostname    | `WIN11-LAB`    |
| Internal IP | `10.10.10.110` |
| NAT IP      | `10.0.2.15`    |
| DNS         | `10.10.10.10`  |
| Domain      | `lab.local`    |

-The workstation was successfully joined to the `lab.local` domain.

-Domain membership was verified from the workstation using the following commands:

whoami

and:

(Get-CimInstance Win32_ComputerSystem).Domain

-The workstation was subsequently moved from the default `Computers` container into:

lab.local
└── LabComputers
    └── Workstations
        └── WIN11-LAB

    The workstation is used to test domain authentication, user permissions and delegated 
help desk tasks.

## Organizational Units
    Custom OUs(Organizational Units) were created to organize user accounts, computers,
security groups and servers.

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

     The User Accounts OU was used instead of creating a custom OU named Users, because Active Directory already provides a built-in Users container at the domain level. 
The built-in container was left unchanged.

    OU placement organizes objects and provides a scope for delegated permissions and Group Policy. OU membership does not automatically add a user to a security group 
with a similar name.


## Domain User Accounts
The following accounts were created for identity management and access-control testing.

### 'lab-user'

  Location: User Accounts\Employees

  Purpose: Standard employee account for testing normal user permissions and help desk workflows.

  Group memberships: Employees, Domain Users

    The account was verified as a standard user without local administrator privileges on the workstation.


### 'helpdesk-user'

  Location: User Accounts\IT

  Purpose: Help desk technician account used to test delegated administrative permissions.

  Group memberships: Employees, IT-HelpDesk, Domain Users

    The account is used to perform approved account-management tasks without granting 
Domain Admin membership.


### 'jvarela'

  Location: User Accounts\IT

  Purpose: IT administrator test account for evaluating administrative group membership and access.

  Group memberships: IT-Admins, Domain Users

    The account is separate from the help desk account. Membership in the custom IT-Admins group does not, by itself, grant Domain Admin privileges; permissions depend 
on the access rights assigned to the group.


### 'unlock-test'

  Location: User Accounts\Employees

  Purpose: Temporary test account used to verify account-lockout behavior and delegated unlocking.

  Group memberships: Standard domain user membership as applicable.

    This account was used for lockout testing rather than risking access to the primary 
employee or administrative accounts.



## Security Groups

The following Global Security Groups were created under Groups\Security Groups.

Group      | Purpose

Employees  | Identifies standard employee accounts

IT-HelpDesk| Identifies help desk technicians who receive delegated support permissions

IT-Admins  | Identifies IT administrative users for permissions assigned to that group


## Group Memberships

The relevant group memberships are:

User         |  Security group memberships
------------------------------------------------------|
lab-user     |  Employees
------------------------------------------------------|
helpdesk-user|  Employees, IT-HelpDesk
------------------------------------------------------|
jvarela      |  IT-Admins
------------------------------------------------------|
unlock-test  |  Created for account management testing

*Domain users also receive the built-in Domain Users membership where applicable.

*No custom group was added to Domain Admins. Administrative privileges are kept separate
from standard employee and help desk permissions.

## Delegated Help Desk Permissions

    Delegated permissions were configured on the User Accounts\Employees OU using the 
Active Directory Users and Computers (ADUC) Delegation of Control Wizard and the OU's 
advanced security settings.

*The IT-HelpDesk group was granted permission to perform selected account-management 
tasks on descendant user objects within the Employees OU.

### Password resets

*The Delegation of Control Wizard was configured for the purpose of resetting  user 
passwords and to force password change at next logon.

*Verification: Signed in to WIN11-LAB as LAB\helpdesk-user, opened ADUC and successfully 
reset the password for lab-user without using Domain Admin credentials.

### Account unlocking

    The IT-HelpDesk group was configured with permission to write the lockoutTime attribute
on descendant user objects in the Employees OU.

*Verification: After configuring the domain lockout policy and locking the unlock-test account through failed authentication attempts, LAB\helpdesk-user successfully unlocked the account through ADUC. The account's locked state was verified using 
PowerShell.

### Enable and disable account permissions

    The help desk account was tested for account enablement and disablement. These 
actions were not permitted with the current delegation. 
*This restriction is intentional: enabling and disabling accounts can have significant operational and security consequences. Additional permissions can be delegated 
later if the lab's requirements change.

## Least-privilege design

    The IT-HelpDesk group is not a member of Domain Admins. Its account-management 
permissions are scoped to the Employees OU rather than granted across the entire domain.

    The lab follows least privilege by separating standard user access, routine help desk tasks and IT administration. Additional access should be granted only when there is 
a defined operational need.

## Domain Password and Account Lockout Policy

The domain password policy was inspected using:

Get-ADDefaultDomainPasswordPolicy |
    Select-Object LockoutThreshold, LockoutDuration, LockoutObservationWindow

    The original lockout threshold was 0, meaning account lockout was disabled. 
For testing purposes, the threshold was changed to 3.

The resulting policy is:

Lockout Setting    |  Value

threshold          |  3 failed attempts

duration           |  30 minutes

observation window |  30 minutes

    The policy was tested using the dedicated unlock-test account. After three failed authentication attempts, Active Directory reported the account as locked. 
The help desk unlock workflow was then successfully tested.

     The threshold change applies to the domain password policy, not only to the test account. It is retained as a lab configuration to support realistic account-lockout 
exercises.

## Domain and DNS Health Checks

Domain-controller services and workstation connectivity were tested after deployment.

### Service status

The following services were checked on LAB-DC01:

Get-Service NTDS, DNS, KDC, Netlogon |
    Select-Object Name, Status, StartType

The checks covered Active Directory Domain Services, DNS, Kerberos Key Distribution Center and Netlogon.

### Domain controller diagnostics

The following commands were used:

dcdiag /q
nltest /dsgetdc:lab.local

    The diagnostics reported no issues in the tests performed, and the domain controller 
was successfully located.

### Client connectivity

From WIN11-LAB, the following connectivity tests were performed:

Test-NetConnection 10.10.10.10 -Port 53
Test-NetConnection 10.10.10.10 -Port 88
Test-NetConnection 10.10.10.10 -Port 389

These tests checked TCP connectivity to DNS, Kerberos and LDAP on the domain controller.
*The tests completed successfully.

    Successful TCP connectivity is useful evidence of reachability, but it does not 
independently verify every aspect of DNS, Kerberos or LDAP operation.

## Current State

This lab now includes:

* A functioning Active Directory domain, lab.local

* A Windows Server 2022 domain controller running AD DS and DNS

* A Windows 11 domain-joined workstation

* Custom OUs for users, computers, groups and servers

* Separate standard user, help desk and IT administrator accounts

* Global Security Groups for employees, help desk and IT administrators

* Delegated password-reset and account-unlock permissions scoped to the Employees OU

* A tested domain account-lockout policy

* Successful domain controller, DNS and client connectivity checks

## Future Improvements

Potential future enhancements include:

  Test and document the exact boundaries of help desk permissions, including attempts 
to modify users outside the Employees OU.

  Add Group Policy Objects (GPOs) for workstation security and user configuration.

  Delegate account enablement and disablement only if a defined help desk workflow 
requires it.

  Add additional incident tickets covering password resets, locked accounts, DNS failures 
and domain authentication troubleshooting.

  Document IT administrator permissions separately and grant only the rights needed for 
specific administrative tasks.

  Review DNS registration and the domain controller's multiple network interfaces to 
ensure clients use the intended internal addresses.


  Overall, this environment provides a foundation for practicing Windows administration,
identity management, access control, Active Directory, and entry-level IT support 
troubleshooting.
