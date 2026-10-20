# SOC Homelab

A home lab I'm building to learn how networks are attacked, defended and monitored, as preparation for a SOC analyst role.

## Setup

Everything runs in VirtualBox on a Linux Mint laptop.

| Machine | Role | Network |
|---|---|---|
| OPNsense | Firewall and router between the networks | WAN (NAT), LAN, OPT1 |
| Kali Linux | Attacker machine | LAN: `attacker-net`, 192.168.10.0/24 |
| Ubuntu Server (company-srv01) | The "company" server | OPT1: `company-net`, 192.168.20.0/24 |

## Labs

| Lab | What I did |
|---|---|
| [Lab 01](lab-01/) | Installed OPNsense and built the segmented network. Fixed interfaces assigned the wrong way round, and a DHCP problem I traced with tcpdump. |
| [Lab 02](lab-02/) | Connected the Ubuntu server to company-net and fixed DHCP not being served on OPT1. |
| [Lab 03](lab-03/) | Wrote firewall rules to block attacker-net from reaching company-net, and fixed a disabled WAN interface. |

## Skills so far

- Setting up network segmentation with a firewall
- Troubleshooting DHCP and routing
- Capturing and reading traffic with tcpdump
- Linux command line

## Next
- Lab 03: firewall rules between attacker-net and company-net
- Lab 04: Wazuh SIEM to collect and alert on logs
- Lab 05: detecting an SSH brute-force attack with a Python script
