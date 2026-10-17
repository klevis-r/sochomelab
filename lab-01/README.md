# Lab 01: Building a segmented network with OPNsense

**Date:** 13 October 2026

## Goal

Set up OPNsense and build a segmented network.

## Choosing and downloading OPNsense

I chose OPNsense for my homelab because it's open source, free, has a web interface that also provides logs, and is a firewall that real businesses use.

To download it, I went to https://opnsense.org/download/ and selected:

- **System architecture:** amd64
- **Image type:** dvd
- **Mirror location:** one near me

I used the **dvd** image because it's an ISO file, and VirtualBox boots VMs from an ISO as if it were a real disc.

## Setting up the VM's network adapters

After loading the ISO into Oracle VirtualBox, I set up 3 network adapters:

| VirtualBox adapter | Attached to | Becomes in OPNsense |
|---|---|---|
| Adapter 1 | NAT | **WAN**: the way out to the internet |
| Adapter 2 | Internal Network `attacker-net` | **LAN** |
| Adapter 3 | Internal Network `company-net` | **OPT1** |

None of them are **Bridged**. Bridged would connect the VM directly to my real home network, which I don't want when I'm running attacks in the future.

## Problem 1: WAN and LAN were the wrong way round

When I first set up OPNsense, it had assigned **LAN = em0** and **WAN = em1**.

<img width="725" height="490" alt="OPNsense interface assignment" src="https://github.com/user-attachments/assets/299a1a6f-2ada-4a74-91e1-7f0a87864689" />

**Why this was a problem:**

- em0 is plugged into NAT (the internet side), but OPNsense was treating it as the **LAN**, the trusted side.
- em1 is plugged into attacker-net, but OPNsense was treating it as the **WAN**, the untrusted side.
- So the LAN's "allow everything" rule was applying to the **internet-facing** side, and the WAN's "block everything" rule was applying to **attacker-net**, where Kali is.
- OPNsense's **DHCP server** was running on the internet side instead of in attacker-net.
- The **WAN is a DHCP client** (it asks for an address). Because em1 was labelled WAN, the firewall's card in attacker-net was **asking** for an address instead of **giving them out**. Kali is also a DHCP client, so attacker-net had two devices asking for addresses and **nothing handing them out**.

In a real setting, this misconfiguration would expose the internal network to the internet.

**The fix:** on the OPNsense console I chose **option 1, "Assign Interfaces"**. It asked about LAGGs and VLANs, and I said **no** to both because I didn't need them. Then I assigned:

- **WAN → em0**
- **LAN → em1** (attacker-net)
- **OPT1 → em2** (company-net)

and confirmed. Now every label matches the network its card is actually connected to.

<img width="670" height="900" alt="Interfaces after reassignment" src="https://github.com/user-attachments/assets/406baea8-d1b6-4ad7-b980-5a845d5ea0dd" />

## Problem 2: Kali had no IP address

<img width="830" height="800" alt="Kali ip a with no IPv4 address and ping failing" src="https://github.com/user-attachments/assets/07c9d7a2-ac64-4118-a710-ed934930a876" />

Running `ip a` (which shows all network interfaces and their IPv4 and IPv6 addresses), `eth0` only had an **IPv6 link-local address** (`fe80::…`), which a device gives itself automatically. There was **no IPv4 address**, which meant no address had been given out by DHCP, so there was no connection.

The second command, `ping -c 4 192.168.10.1`, failed with **"Network is unreachable"**. Without an IPv4 address, Kali doesn't think it's on an IPv4 network at all, so the ping never left Kali.

Then I ran `nmcli device status`, which showed:

```
eth0  ethernet  connecting (getting IP configuration)
```

This means Kali **was** sending DHCP requests and waiting for a reply that never came.


## Result

<img width="1033" height="1040" alt="Kali with IPv4 address 192.168.10.142" src="https://github.com/user-attachments/assets/7a38b7bd-bab9-4c7a-b0b8-0c34d4f343f7" />

After the fix, `ip a` shows `inet 192.168.10.142/24`. **Kali now has an IPv4 address**, marked "dynamic" with a 24-hour lease, which shows it came from the firewall's DHCP server.
