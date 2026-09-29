# HomeLab

A VirtualBox-based home lab built to develop practical IT administration and cybersecurity skills through hands-on configuration, troubleshooting and documentation.

The lab is designed to simulate a small organization's internal IT environment and currently focuses on networking, Windows administration, Active Directory, DNS, Linux administration and help-desk troubleshooting.

> **Status:** Active / In Development

---

## 🎯 Goals

The primary goal of this project is to build practical experience that complements my academic cybersecurity coursework and professional certifications.

This lab is being used to practice:

* Windows system administration
* Active Directory administration
* DNS configuration and troubleshooting
* Network configuration and troubleshooting
* Linux administration
* User and group management
* Security group management
* Windows domain environments
* Help-desk troubleshooting
* Incident documentation
* Basic security administration
* Future Group Policy and access-control configuration
* Future security monitoring and detection

The lab is intentionally documented as it develops so that configuration changes, troubleshooting processes and lessons learned can be reviewed later.

---

## 🖥️ Lab Environment

The lab is built using **Oracle VirtualBox** and consists of multiple virtual machines connected through isolated and NAT networks.

### Network Architecture

The lab uses two primary VirtualBox network types:

| Network   | Purpose                              | Address Range   |
| --------- | ------------------------------------ | --------------- |
| NAT       | Internet access for virtual machines | `10.0.2.0/24`   |
| Host-Only | Internal lab communication           | `10.10.10.0/24` |

The host-only network allows the lab machines to communicate with one another without exposing the internal lab environment directly to the physical network.

### Current Devices

| Device        | Operating System                 | Internal IP    | Primary Role            |
| ------------- | -------------------------------- | -------------- | ----------------------- |
| Physical Host | Windows 11                       | `10.10.10.1`   | VirtualBox host         |
| LAB-DC01      | Windows Server                   | `10.10.10.10`  | Domain Controller / DNS |
| LAB-UBUNTU-01 | Ubuntu Linux                     | `10.10.10.30`  | Linux server            |
| WinLab        | Windows                          | `10.10.10.100` | Windows lab workstation |
| WIN11-LAB     | Windows 11 Enterprise Evaluation | `10.10.10.110` | Domain workstation      |

---

## 🏢 Active Directory Environment

The lab currently contains a functioning Active Directory domain:

```text
Domain: lab.local
Domain Controller: LAB-DC01
DNS Server: 10.10.10.10
```

`LAB-DC01` provides:

* Active Directory Domain Services
* Internal DNS
* Domain authentication
* User and group management

`WIN11-LAB` has been successfully joined to the `lab.local` domain.

### Organizational Unit Structure

The current Active Directory structure is:

```text
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
```

The built-in `Users` container and `Domain Controllers` OU remain in place.

---

## 👤 Users & Groups

The lab currently contains several accounts for testing different levels of access.

### Users

| User            | OU                        | Purpose                        |
| --------------- | ------------------------- | ------------------------------ |
| `jvarela`       | User Accounts → IT        | IT administrative test account |
| `lab-user`      | User Accounts → Employees | Standard employee account      |
| `helpdesk-user` | User Accounts → IT        | Help-desk account              |

### Security Groups

| Group         | Purpose                    |
| ------------- | -------------------------- |
| `Employees`   | Standard employee accounts |
| `IT-HelpDesk` | Help-desk users            |
| `IT-Admins`   | IT administrative users    |

Current group membership:

```text
lab-user
└── Employees

helpdesk-user
├── Employees
└── IT-HelpDesk

jvarela
└── IT-Admins
```

Administrative permissions will be deliberately delegated as the lab progresses rather than simply placing users into `Domain Admins`.

---

## 🌐 Networking

The internal lab network is:

```text
10.10.10.0/24
```

Current addressing:

```text
Physical Host     10.10.10.1
LAB-DC01          10.10.10.10
LAB-UBUNTU-01     10.10.10.30
WinLab            10.10.10.100
WIN11-LAB         10.10.10.110
```

The environment has been used to practice:

* IPv4 addressing
* Subnet configuration
* Host-only networking
* NAT
* TCP connectivity testing
* ICMP troubleshooting
* DNS troubleshooting
* Network adapter configuration
* Windows firewall/network profiles
* VirtualBox networking

Example troubleshooting tools used include:

```powershell
ipconfig /all
ping
Test-NetConnection
Get-NetAdapter
Get-NetConnectionProfile
nslookup
whoami
whoami /groups
```

---

## 🛠️ Help-Desk Troubleshooting

The lab includes a dedicated documentation area for simulated help-desk incidents.

### Incident #001 — Internal Server Connectivity

A simulated user reported being unable to access an internal Linux server.

The investigation included:

1. Testing basic network connectivity with `ping`
2. Testing TCP port 22 with `Test-NetConnection`
3. Verifying the source interface and IP address
4. Testing an actual SSH connection
5. Confirming that the SSH service was reachable and responding

The investigation demonstrated that the underlying network path and SSH service were functional.

Incident documentation is maintained in:

```text
Documentation/03-Help-Desk-Tickets/
```

---

## 📁 Documentation

The project is being documented alongside the lab configuration.

Current documentation:

```text
Documentation/
│
├── 01-Lab-Foundation/
│   ├── Network-Architecture.md
│   └── SSH-Networking-Troubleshooting.md
│
├── 02-Windows-Administration/
│   ├── Windows-VM-Configuration.md
│   └── Active-Directory-Deployment.md
│
└── 03-Help-Desk-Tickets/
    └── incident001.md
```

The documentation is intended to demonstrate not only the final configuration but also the troubleshooting process used to reach it.

---

## 🔐 Security Considerations

This environment is intended to remain an isolated practice environment.

Some important practices being followed include:

* Using an isolated host-only network for internal lab traffic
* Separating Internet access through NAT
* Avoiding real credentials in the repository
* Using lab-only passwords
* Avoiding unnecessary `Domain Admins` membership
* Using security groups for access management
* Documenting configuration changes
* Keeping virtual machine disks and sensitive configuration files out of Git

Virtual machine disk images, snapshots, credentials and other sensitive files are excluded through `.gitignore`.

---

## 🚧 Planned Work

The lab is still actively being developed.

### Active Directory

* [x] Deploy Windows Server domain controller
* [x] Configure `lab.local`
* [x] Configure internal DNS
* [x] Create organizational units
* [x] Join Windows workstation to the domain
* [x] Create domain users
* [x] Create security groups
* [x] Configure initial group memberships
* [ ] Test domain authentication
* [ ] Test standard user permissions
* [ ] Configure help-desk delegation
* [ ] Create and deploy Group Policy Objects
* [ ] Configure password/account policies
* [ ] Practice account lockout and recovery scenarios

### Windows Administration

* [x] Windows VM deployment
* [x] Network adapter configuration
* [x] Static IP configuration
* [x] DNS configuration
* [x] Windows network profile troubleshooting
* [ ] Remote administration
* [ ] Windows event log analysis
* [ ] Local and domain permission troubleshooting
* [ ] Software deployment
* [ ] Group Policy administration

### Networking

* [x] VirtualBox NAT networking
* [x] Host-only networking
* [x] IPv4 configuration
* [x] ICMP troubleshooting
* [x] TCP connectivity testing
* [x] DNS troubleshooting
* [ ] Packet capture and analysis
* [ ] Network segmentation exercises
* [ ] Service troubleshooting

### Help Desk / IT Support

* [x] Create simulated help-desk incidents
* [x] Document troubleshooting methodology
* [ ] User account troubleshooting
* [ ] Password/account lockout scenarios
* [ ] File/share permission issues
* [ ] Printer troubleshooting
* [ ] Software installation issues
* [ ] Domain authentication issues
* [ ] Escalation procedures

### Cybersecurity

Future phases will expand the lab toward defensive security and incident response, including:

* Windows event monitoring
* Security log analysis
* SIEM integration
* Network traffic analysis
* Detection engineering
* Vulnerability scanning
* Endpoint security
* Incident response exercises
* Attack/defense simulations

---

## 🧰 Technologies & Tools

Current technologies include:

* **Virtualization:** Oracle VirtualBox
* **Operating Systems:** Windows 11, Windows Server, Ubuntu Linux
* **Directory Services:** Active Directory Domain Services
* **DNS:** Windows Server DNS
* **Networking:** IPv4, NAT, Host-Only Networking, TCP/IP
* **Administration:** PowerShell, Windows Server Manager, Active Directory Users and Computers
* **Troubleshooting:** `ping`, `Test-NetConnection`, `ipconfig`, `nslookup`
* **Version Control:** Git / GitHub

Additional cybersecurity tools and technologies will be introduced as the lab develops.

---

## 📈 Project Philosophy

This lab is intentionally built incrementally.

Rather than configuring an entire environment from the beginning, each component is introduced, tested and documented before moving to the next stage.

This approach is intended to reinforce three skills:

**Configure → Troubleshoot → Document**

The goal is not simply to have a working virtual environment, but to develop the ability to understand why the environment works, diagnose when it does not and clearly document the resolution.

---

## 📌 Project Status

**Current Phase:** Active Directory user and group management

The core Active Directory environment is operational. The next stage will focus on validating domain accounts, testing permissions and implementing controlled help-desk delegation.

---

## Author

**Jose Varela**

Computer Science graduate | M.S. Cybersecurity student

GitHub: [JV020904](https://github.com/JV020904)
