#LAB-04

For this session, I'm building a SIEM tool. A SIEM (Security Information and Event Managment) tool collects logs from every machine on the network and flags anything suspicious, like failed SSH attempts. 
I'll be using WAZUH as its free and open soruce.

#The plan for this lab
Session 1 (lab-04): Build the wazuh server and get the dashboard working
Session 2 (lab-04b): Connect the wazuh server to company-srv01 and watch the logs as they arrive.

Before building anything, I had to decide where WAZUH would be on the network. I cant do LAN as thats where Kali was, and this is a SIEM for the company server, It'd be stupid. The attacker shouldnt be able to reach the SIEM.
I instead decided on the company server, as the security tools belong next to the server they are protecting.

I thought about downloading the Wazuh on the company server, then i looked up why you shouldnt do this.
1. If company-svr01 gets  hacked, the hacker would be able to also get the logs. This would meant that the hacker could also make their presence unknown by deleting logs and modifying logs, getting rid of evidence.
2. Port 443 was already taken by nginx. Wazuhs dashboard uses port 443, and nginx already uses port 443 on the company netowork. Two services cant listen on the same port, so it wouldnt have worked out in the first place.
3. It wouldnt have fit. When I created company-srv01, I'd only thought about nginx and the minimum requirements to host it. I needed 4 CPUS and 8 GB RAM. company-srv01 only had 2 CPUS and 2 GB RAM.

# Getting the right ubuntu

When installing Ubuntu on my VM, I hadnt realised that my version of Ubuntu, 26.04.1, doesnt support wazzuh, which instead uses Ubuntu version 24.04. I had to get the direct link to the installation of this Ubuntu version,
https://releases.ubuntu.com/noble/ubuntu-24.04.5-live-server-amd64.iso
and then created the new VM.

# Creating the VM
I gave Ubuntu 8192 MB of RAM and 4 CPUs, which was wazuh's minimum for a all in one installation. My laptop has 32 GB ram which is the only reason why im able to run all these VM's at once. I then gave it 50 VDI 
(virtualbox disk image), which is Wazuh's reccomendation for storing about 90 days worth of alerts. I didnt pre allocate it so the   file only grows as data is written instead of taking 50gb off of my laptop straight away,
I was running out of storage when making this.


#Network adaptor
Adaptor 1 was set to NAT on default. NAT would give Wazuh its own route to the internet which wouldve skipped my firewall completely. I changed it to internal network and renamed it to company net, the same as company-srv01.
Its mac address was 08:00:27:8A:6B:FA, which would then later show up in OPNsense's DHCP leases, aswell as tcpdump.

# Installing ubuntu
I set the language to English UK
Network: DHCP (automatic) for now, it should get a 192.168.20.x address
Server name: wazuh-server01. It had to be different from company-srv01 because every log is labelled with a hostname. If 2 machines had the same name, I wouldnt be able to tell which alert came from which sever.
I then ticked install OpenSSH server, and skipped extra snaps.

At the end, the installer asked me to remove the installation medium. In the VM, I went to devices -> optical drives, remove disk from drive. If I dont do this, the vm would go back to the installer upon rebooting it.
The VM showed Failed unmounting cdrom.mount. When i checked the storage settings, the optical drive said empty. VirtualBox had already ejected the ISO. I then just pressed enter to reboot.
