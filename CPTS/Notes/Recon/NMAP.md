Enumeration is the most critical part of pen testing. It gives us a strong foundation to work from and allows us to see services running on a machine. The more information we have the more vectors of attack we have.


--top-ports scan the most common top ports.
To be able to track how our sent packets are handled, we deactivate the ICMP echo requests (`-Pn`), DNS resolution (`-n`), and ARP ping scan (`--disable-arp-ping`) again.

to ping for hostname intiate a ping on port 445 with ```
```shell
sudo nmap <IP> -p445 STMIP -T5 -sV
```
*-sV scan version*

### Saving Results
results can be saved in different formats 
- -oN normal output (.nmap file)
- -oG Greppable
- -oX XML 

With XML we can easily create HTML reports using `xsltproc`

## Service Enumeration
use -sV
-n no dns resoultion
--packet-trace

```shell-session
nmap -Pn --disable-arp-ping -p53 -sU -sC 10.129.2.48
```
^ Version ENUM

