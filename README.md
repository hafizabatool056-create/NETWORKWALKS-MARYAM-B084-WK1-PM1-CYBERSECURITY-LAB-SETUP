# Cybersecurity Lab Setup: WK1-PM1

**Network Walks Cybersecurity Internship · Batch B084 · Week 1 · Project Module 1**
**Prepared by:** Maryam Batool

## Overview

This project sets up a cybersecurity testing lab on a personal Windows laptop. Oracle VirtualBox is the base platform, Kali Linux is the attacking machine, and both live on a NAT Network in the `10.0.0.0/24` subnet.

## Requirements and status

| # | Requirement | Status |
| --- | --- | --- |
| 1 | VirtualBox as the base (latest recommended version) | Done |
| 2 | Kali Linux set up as the attacking machine | Done |
| 3 | Network set up in subnet 10.0.0.0/24 | Done |
| 4 | NATNetwork with 10.0.0.0/24 | Done |
| 5 | Clipboard and drag/drop enabled in VM settings | Done |
| 6 | Shared folder enabled with host Downloads folder | Done |
| 7 | Kali IP address 10.0.0.2/24 | Partial: Kali received 10.0.0.3/24 from DHCP, static 10.0.0.2/24 is still to be applied (see Issues) |
| 8 | Kali has full Internet access | Done |

## Environment

| Item | Value |
| --- | --- |
| Host OS | Windows 10, 64-bit |
| Host RAM | 8 GB |
| VirtualBox | 7.2.22 |
| Kali Linux | 2026.3 (VirtualBox prebuilt image, amd64) |
| VM resources | 2 GB RAM, 2 CPU cores, 80 GB disk |
| Network | NATNetwork, 10.0.0.0/24 |

## Implementation steps

### Step 1: Installed VirtualBox
Downloaded the latest version from virtualbox.org and installed it with the default options.

![VirtualBox version](images/fig1-virtualbox-version.png)
*Figure 1: VirtualBox version shown in Help > About*

### Step 2: Created the NAT Network
Opened File > Tools > Network Manager > NAT Networks, created a new network named `NatNetwork` and set the IPv4 prefix to `10.0.0.0/24`.

![NAT Network](images/fig2-nat-network.png)
*Figure 2: NAT Network settings showing 10.0.0.0/24*

### Step 3: Set up Kali Linux
Downloaded the Kali VirtualBox image from kali.org and added it to VirtualBox. In Settings > Network, set Adapter 1 to NAT Network and selected `NatNetwork`.

![Kali network adapter](images/fig3-kali-network-adapter.png)
*Figure 3: Kali VM network adapter attached to the NAT Network*

### Step 4: Enabled clipboard and drag/drop
In Settings > General > Features, set Shared Clipboard and Drag-and-Drop to Bidirectional.

![Clipboard and drag/drop](images/fig4-clipboard-dragdrop.png)
*Figure 4: General > Features settings*

### Step 5: Enabled the shared folder
In Settings > Shared Folders, added the host Downloads folder with Auto-mount and Make Global selected. Inside Kali the share appears under `/media/sf_Downloads`.

![Shared folder setting](images/fig5-shared-folder-setting.png)
*Figure 5: Shared Folders settings in VirtualBox*

![Shared folder in Kali](images/fig6-shared-folder-in-kali.png)
*Figure 6: Host Downloads files visible inside Kali (`ls /media/sf_Downloads`)*

### Step 6: Checked the IP address
Installed the VirtualBox guest tools in Kali, then ran `ip a`. Kali received `10.0.0.3/24` from the NAT Network DHCP server (marked `dynamic`). The required static address `10.0.0.2/24` is still to be configured (see Issues).

![IP address](images/fig7-ip-address.png)
*Figure 7: Kali network configuration showing 10.0.0.3/24 (DHCP)*

## Verification

| Check | Command | Result |
| --- | --- | --- |
| Gateway | `ping -c 3 10.0.0.1` | Pass: 3/3 replies, 0% packet loss, avg 0.390 ms |
| Internet by IP | `ping -c 3 8.8.8.8` | Pass: 3/3 replies, 0% packet loss, avg 38.952 ms |
| Internet by name | `ping -c 3 google.com` | Pass: resolved to 142.250.202.14, 0% packet loss, avg 41.079 ms |
| Shared folder | `ls /media/sf_Downloads` | Pass: host Downloads files listed |
| Static IP | `ip a` | Pending: shows 10.0.0.3/24 (DHCP) |

![Ping tests](images/fig8-ping-tests.png)
*Figure 8: Terminal output of the ping tests*

## Issues faced and resolution

| Issue | Cause | Resolution |
| --- | --- | --- |
| Kali got `10.0.0.3/24` instead of the required `10.0.0.2/24` | The NAT Network has DHCP enabled and no static address was configured | Set a manual IPv4 address with `nmcli` (command below). To be applied and re-verified. |

```bash
nmcli con show
sudo nmcli con mod "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli con up "Wired connection 1"
ip a
```

## Conclusion

The lab runs Kali Linux 2026.3 on VirtualBox 7.2.22 inside the NAT Network `10.0.0.0/24`, with clipboard, drag/drop and the shared folder working and full Internet access confirmed by ping tests. The static address `10.0.0.2/24` is the one remaining item. I learned how a NAT Network keeps lab machines isolated while still giving them Internet access, and how DHCP and static addressing differ.
