# SOC Homelab

A home lab I'm building to learn how networks are attacked, defended and monitored, as preparation for a SOC analyst role.

## Setup

Everything runs in VirtualBox on a Linux Mint laptop.

| Machine | Role | Network |
|---|---|---|
| OPNsense | Firewall and router between the networks | WAN (NAT), LAN, OPT1, ANALYST |
| Kali Linux | Attacker machine | LAN: `attacker-net`, 192.168.10.0/24 |
| Ubuntu Server (company-srv01) | The "company" server | OPT1: `company-net`, 192.168.20.0/24 |
| Ubuntu Server (wazuh-server01) | Wazuh SIEM, collects and alerts on logs | OPT1: `company-net`, 192.168.20.130 (reserved) |
| Lubuntu (siemview) | Analyst PC, only opens the Wazuh dashboard | ANALYST: `analyst-net`, 192.168.30.0/24 |

## Labs

| Lab | What I did |
|---|---|
| [Lab 01](lab-01/) | Installed OPNsense and built the segmented network. Fixed interfaces assigned the wrong way round, and a DHCP problem I traced with tcpdump. |
| [Lab 02](lab-02/) | Connected the Ubuntu server to company-net and fixed DHCP not being served on OPT1. |
| [Lab 03](lab-03/) | Wrote firewall rules to block attacker-net from reaching company-net, and proved it with ping tests and the firewall log. |
| [Lab 03b](lab-03b/) | Least privilege: allowed only HTTPS (port 443) from LAN to the company server, placed above the block rule so everything else stays blocked. |
| [Lab 04](lab-04/) | Built a Wazuh SIEM server on company-net: right Ubuntu version, extended the disk with LVM, changed the leaked admin password safely, and reserved its IP in OPNsense. Proved Kali can't reach it. |
| [Lab 04b](lab-04b/) | Built a separate analyst network and a Lubuntu analyst PC. One logged rule lets it reach only Wazuh on HTTPS (/32), with no internet, which I proved with DNS and direct IP tests in Live View. Also fixed a hole that let Kali reach company-net. |

## Skills so far

- Setting up network segmentation with a firewall
- Troubleshooting DHCP and routing
- Writing and ordering firewall rules
- Capturing and reading traffic with tcpdump
- Linux command line
- Setting up a SIEM (Wazuh) and managing its passwords safely
- DHCP reservations and LVM disk management
- Least-privilege rules, temporary rules with a backout, and checking them in the firewall's Live View

## Next
- Lab 05: connect company-srv01 to Wazuh and watch its logs arrive
- Later: detecting an SSH brute-force attack with a Python script
