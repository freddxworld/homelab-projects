# Issues Encountered

## Issue 1: Windows VM Could Not Reach Ubuntu VM

### Symptoms

- SSH connection failed
- Ping requests timed out

### Investigation

Checked network configuration:

ip a

Checked routing table:

ip route

Discovered:

Windows VM: 192.168.0.x
Ubuntu VM: 192.168.1.101

Ubuntu was using an old static IP configuration.

### Root Cause

Ubuntu Server was configured with:

- Static IP: 192.168.1.101
- Gateway: 192.168.1.1

The network had been migrated to 192.168.0.0/24.

### Resolution

- Reviewed Netplan configuration
- Changed Ubuntu networking from static addressing to DHCP
- Applied configuration using netplan
- Verified new IP address
- Confirmed SSH and ping connectivity

### Result

Windows and Ubuntu VMs successfully communicated and SSH access was restored.
