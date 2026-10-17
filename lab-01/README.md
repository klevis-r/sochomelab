#Day 1

Goal: Set up OPNsense and build a segmented network

I chose to download OPNsense for my dhq as its open source, free, has a web interface that also provides logs and also a firewall businesses used. 
To download it, I went on https://opnsense.org/download/, selected amd64 as the system architecture, dvd image type and a mirror location near me.
The reason why I used dvd is because it uses an ISO file which VMs use to boot the ISO as if it were a real disc.

After putting the ISO file into Oracle VirtualBox, I had to set up the 3 network adapters.
Adapter1 (NAT) = WAN, becomes the access to the internet
Adapter2 = Internal Network, name = attacker-net becomes LAN
Adapter3 = Internal Network, name = company-net  becomes OPT1
These are all never bridged, otherwise it would provide a direct connection to my real home network which should'nt be considered when running the attacks in the  future.

Upon setting up the OPNsense and entering the VM, it had automatically set LAN = em0 and WAN=em1.
<img width="725" height="490" alt="image" src="https://github.com/user-attachments/assets/299a1a6f-2ada-4a74-91e1-7f0a87864689" />
This was the first issue that I went into. OPNsense was treating LAN as the internet side, which meant it is now untrusted, and WAN as the attacker-net, which meant it is now trusted.
This means that LANS allow everything rule was applying to the network and WANS block everything was applying to my kali VM. OPNsense's DHCP server was running on the internet instead of kali's server, which meant that kali wouldnt be able to recieve an address.
Instead, the WAN was set to ask for the address by the DHCP, which meant that the DHCP client, which in this case is the WAN, was asking the DHCP server, which in this case is LAN (kali), for an IP address. However, Kali itself is also asking for an IP address. There is no DHCP that is labelling either of the clients with IP's.


