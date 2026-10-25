Lab 03 - Firewall rules
Started 22nd octobor 2026 - finished 24th october 2026

Note: why a large part of this lab was reconstructed
Some of the early notes of this lab were done on the 15th of october, right after lab 02 was finished. However, I ended up losing all my notes from that session, so I couldnt remember what i changed. 
What I did know was that Ubuntu couldnt resolve names. This was not a DNS issue, and was instead a issue from the firewall, em0. Instead of just guessing, I decided I'd use a feature built into the OPNsense website, config history.

Instead of guessing, I rebuilt what happened from evidence:
- **OPNsense config history** (System → Configuration → History) showed that on **15 Oct 2026 at 18:02 UTC**, `root@192.168.10.142` (me, from Kali) added an OPT1 pass rule, "allow company-net to the internet", below an existing block rule, "blocks the company-net to attacker-net to prevent lateral movement" (Step 4).
The current rule order and live ping test confirmed what those rules actually do (Steps 3 and 5).

Everything from 22nd of October was recorded as I went. So lessons learnt, make sure to take screenshots during the lab and not after, I also decided I'd be using obsidian vault to store information that I might use to document things as well as images aside them, making things less of a hassle for me.

Todays goal: company-net (the ubuntu vm) must be able to reach DNS and the internet attacker-net (kali vm) must not be able to reach the company-net. Prove these 2 with a test

First, I ran all the vm's, OPNsense -> Kali -> Ubuntu.
The first test I did was on ubuntu, to check if Ubuntu was able to reach the internet. I ran ping -c 3 google.com which queries google and saw google.com resolved to 142.250.117.113. 3 sent, 3 received, 0% packet loss.
This meant that the DNS and internet works and the "Temporary failure resolving archive.ubuntu.com" error was fixed from the end of Lab 02. This was what reminded me that I was able to access the configuration logs through the OPNsense website, which is where I went next, to see how the firewall rules were set. 
Under the interface rules, I could see the configurations of the cards. LAN had its destination set to any, and OPT1 had only blocked OPT1 from being able to access LAN. This was a problem as the kali vm was still able to ping the ubuntu server, which organizations generally try to avoid, as direct access with an organization's server is a security risk.
To test if this was the case, I went on the Kali CLI and tried to ping the Ubuntu server through the command ping -c 3 192.168.20.161.
It shows that all packets were transmitted, suggesting that this was the case. I could also see the TTL (Time to live, tells us how many hops a packet has gone through) had decreased, meaning that the firewall had allowed Kali to access the Ubuntu server. I then checked if Ubuntu was able to ping kali through ping -c 3 192.168.10.1.
Here the scenario was different, all packets were dropped. This is because of my old configuration which I'd failed to document. The ubuntu server drops all packets that it sends because of the block configuration I'd set, OPT1 blocks all packets being sent to kali. 
I wanted to find this fix, so I went onto the configuration history. I couldnt understand any of it, so I asked Cluade AI for assistance.
