#Lab Network Overview#
	This HomeLab uses a VirtualBox host-only network for internal 
communication between the physical host and lab virtual machines. Internet access 
is provided separately through VirtualBox NAT.

#Network Segments#
Network		Purpose				Addressing
 NAT		Internet access for VMs		10.0.2.0/24
 Lab		Internal lab communication	10.10.10.0/24


| Device           | Interface  | Network | Address      | Purpose  |
| ---------------- | ---------- | ------- | -----------  | -------- |
| Physical Windows | Host-only  | Lab     | 10.10.10.1   | Host     |
| LAB-UBUNTU-01    | enp0s3     | NAT     | 10.0.2.15    | Internet |
| LAB-UBUNTU-01    | enp0s8     | Lab     | 10.10.10.30  | Internal |
| WinLab           | Ethernet   | NAT     | 10.0.2.15    | Internet |
| WinLab           | Ethernet 2 | Lab     | 10.10.10.100 | Internal |
| WIN11-Lab	   | Ethernet 2 | NAT     | 10.0.2.15	 | Internet |
| WIN11-LAB	   | Ethernet	| Lab     | 10.10.10.110 | Domain   |
| LAB-DC01	   | Ethernet	| NAT	  | 10.0.2.15	 | Internet |	
| LAB-DC01	   | Ethernet 2 | Lab	  | 10.10.10.10  | AD/DNS   |


This lab utilizes LAB-DC01 as the domain controller for the lab.local Active Directory
domain.

Service					Server		Address
-------------------------------------------------------------------|
Active Directory Domain Services	LAB-DC01	10.10.10.10|
DNS					LAB-DC01	10.10.10.10|
Domain					lab.local	—	   |
--------------------------------------------------------------------

#Domain Workstation#

WIN11-LAB is joined to the lab.local domain and is located in the LabComputers/Workstations 
OU(organizational unit).

lab.local
└── LabComputers
    └── Workstations
        └── WIN11-LAB
#Network Design#

The lab separates Internet access from internal lab traffic.

                         Internet
                            │
                     VirtualBox NAT
                            │
            ┌───────────────┼───────────────┐
            │               │               │
        LAB-DC01        WIN11-LAB        WinLab
       10.0.2.15         10.0.2.15        10.0.2.15
            │
            │
      Host-Only Network
       10.10.10.0/24
            │
     ┌──────┼───────────────┐
     │      │               │
  Host    LAB-DC01      WIN11-LAB
 .1        .10             .110
            │
            ├── DNS
            └── Active Directory

        LAB-UBUNTU-01
             .30

          WinLab
            .100

	The host-only network allows the lab systems to communicate with each other without 
exposing the internal lab network directly to the physical network.
