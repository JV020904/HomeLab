# Incident #001

**Ticket:** HD-001
**Priority:** Normal
**User:** lab-user
**Computer:** WinLab

## Issue

**User Report:**

> "I was trying to connect to one of our internal Linux servers, but it isn't working. I was able to connect to it yesterday. I haven't changed anything 
since then."

----------------------------------------------------------

## Initial Hypothesis

The issue may be related to network configuration. Possible causes include the 
workstation being on the wrong subnet or the network adapter being connected 
to the incorrect VirtualBox host-only network.

-----------------------------------------------------------

## Troubleshooting Process

### 1. Verify that the Ubuntu host is reachable

From `WinLab`, I used `ping` to test connectivity to the Ubuntu server:

ping 10.10.10.30


*Result:* Successful.

This confirmed that `WinLab` could reach the Ubuntu server at the IP layer.

### 2. Verify TCP connectivity to the SSH service

*I tested whether TCP port 22 was reachable:

*Command: Test-NetConnection 10.10.10.30 -Port 22

*Result:* Successful.

The test showed:

* Source address: `10.10.10.100`
* Source interface: `Ethernet 2`
* Remote address: `10.10.10.30`
* Remote port: `22`

This confirmed that TCP connectivity to the SSH service was working.

### 3. Verify the network interface and addressing

The connection was using:

* Interface: `Ethernet 2`
* IP address: `10.10.10.100`
* Ubuntu server: `10.10.10.30`
* Network: `10.10.10.0/24`

Both systems are therefore on the same host-only network.

**Finding:** The initial network configuration hypothesis was not supported by the testing. The workstation was connected to the correct network and had 
connectivity to the Ubuntu server.

### 4. Test the actual SSH connection

Because the server is accessed through SSH, I tested the application-level 
connection directly:
	ssh jose@10.10.10.30

	*Result: Successful.
The SSH client presented a password prompt, and I was able to authenticate
 successfully using the Ubuntu account credentials.

This confirms that:

* The Ubuntu host is reachable.
* TCP port 22 is accessible.
* The SSH service is running and responding.
* SSH authentication is functioning.
* The Windows client can successfully establish an SSH session with the Ubuntu server.

---

## Root Cause

**Unable to reproduce the reported issue.**

No network, connectivity, SSH service or authentication problem was identified 
during troubleshooting.

*The user's reported inability to access the server could not be reproduced
 during the investigation.

----------------------------------------------------------------

## Resolution

*No configuration changes were necessary.

*The SSH connection was successfully established from `WinLab` to `LAB-UBUNTU-01`
 using the following command: 
	ssh jose@10.10.10.30

----------------------------------------------------------------

## Verification

The issue was considered resolved/cleared after successfully establishing an authenticated SSH session with the Ubuntu server.

-----------------------------------------------------------------

## Troubleshooting Summary

The investigation followed a bottom-up troubleshooting approach:

Network configuration
        ↓
IP connectivity
        ↓
TCP connectivity
        ↓
SSH service
        ↓
Authentication

	
	*Each layer was successfully verified.

	*Final Status: Unable to reproduce / No fault found during investigation.
