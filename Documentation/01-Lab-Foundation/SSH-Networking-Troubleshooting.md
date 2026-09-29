# SSH Connectivity Troubleshooting

## Environment

Host: 
Windows 11

Hypervisor:
Oracle VirtualBox

Guest:
Ubuntu Server

Ubuntu IP:
10.10.10.30

Host-only network:
10.10.10.0/24

SSH:
TCP/22

## Problem

Windows host could not SSH into Ubuntu.

Error:
ssh: connect to host 10.10.10.30 port 22: Connection timed out

## Initial Tests

Ping:
Failed

TCP/22:
Failed

## Investigation

Checked Ubuntu network interfaces.

enp0s3:
10.0.2.15/24
NAT

enp0s8:
10.10.10.30/24
Host-only

Discovered that Adapter 2 was attached to the wrong VirtualBox
host-only network.

## Resolution

Attached Adapter 2 to the correct host-only network.

Restarted Ubuntu.

## Verification

Ping:
Successful

TCP/22:
Successful

SSH:
Successful

## Root Cause

Ubuntu's host-only network interface was connected to the wrong
VirtualBox host-only network.

## Lessons Learned
 * SSH is not always configured initially when creating a network environment
 * Firewalls can be configured to allow/deny traffic on the basis of IP address version (v4 vs v6)
 * A single lost packet immediately after establishing/reinitializing a virtual network can happen for several mundane reasons:
   -Interface initialization, ARP resolution, first packet arriving before the network stack is fully ready, etc. 

