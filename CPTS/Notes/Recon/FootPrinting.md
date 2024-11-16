## Enumeration Principles
Enumeration is information gathering via active methods(scans) and passive methods (OSINT).

- What can we see?
- What reasons can we have for seeing it?
- What image does what we see create for us?
- What do we gain from it?
- How can we use it?
- What can we not see?
- What reasons can there be that we do not see?
- What image results for us from what we do not see?

***Three core principles***

| **`No.`** | **`Principle`**                                                        |
| --------- | ---------------------------------------------------------------------- |
| 1.        | There is more than meets the eye. Consider all points of view.         |
| 2.        | Distinguish between what we see and what we do not see.                |
| 3.        | There are always ways to gain more information. Understand the target. |

## Enumeration Methodology
Three different levels of enumeration: `Infrastructure-based enumeration` `Host-based enumeration` `OS-based enumeration`

![[Enumeration.png]]
6 Core Layers

| **Layer**                | **Description**                                                                                        | **Information Categories**                                                                         |
| ------------------------ | ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| `1. Internet Presence`   | Identification of internet presence and externally accessible infrastructure.                          | Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures      |
| `2. Gateway`             | Identify the possible security measures to protect the company's external and internal infrastructure. | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare                  |
| `3. Accessible Services` | Identify accessible interfaces and services that are hosted externally or internally.                  | Service Type, Functionality, Configuration, Port, Version, Interface                               |
| `4. Processes`           | Identify the internal processes, sources, and destinations associated with the services.               | PID, Processed Data, Tasks, Source, Destination                                                    |
| `5. Privileges`          | Identification of the internal permissions and privileges to the accessible services.                  | Groups, Users, Permissions, Restrictions, Environment                                              |
| `6. OS Setup`            | Identification of the internal components and systems setup.                                           | OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files |

## Domain information
Domain information is a core component of any pentest.
Information is gathered passively so we stay hidden.
We can use third party services to gather information more effectively. 

Combination of first and second principles of enumeration, `What we see` and `What we dont see`

### Online Presence
Once we have a basic understanding of a target we then get a first impression of their presence  on the internet.

A good first point of presence is the SSL certificate. 
Certificate can be used for several domains which are all likely still active.

https://crt.sh

***shodan***

## Cloud Resources
Often cloud storage is added to the DNS list when used for administrative purposes by other employees. This step makes it much easier for the employees to reach and manage them

greyhatwarfare - Lists buckets and looks for leaked credentials online. 

## Staff
People are the weakest link in cyber security.
Linkedin Job posts can provide info about infrastructure used.

OSINT on employees can reveal information about services.

Github Profiles could contain APO keys or hardcoded JWT tokens
## FTP
Most linux FTP servers use vsFTP and the configuration file is found at 
``/etc/vsftpd.conf``

FTP users are usually found at `/etc/ftpusers`

##### Dangerous Settings
|   |   |
|---|---|
|`anonymous_enable=YES`|Allowing anonymous login?|
|`anon_upload_enable=YES`|Allowing anonymous to upload files?|
|`anon_mkdir_write_enable=YES`|Allowing anonymous to create new directories?|
|`no_anon_password=YES`|Do not ask anonymous for password?|
|`anon_root=/home/username/ftp`|Directory for anonymous.|
|`write_enable=YES`|Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE?|

*CHECK ANONYMOUS LOGIN*

*status/debug* for settings

ls -R for recursive

## SMB
*Server Message Block* regulates access to files and resources. 
Alternative implimentation on UNIX environments called SaMBa

|**SMB Version**|**Supported**|**Features**|
|---|---|---|
|CIFS|Windows NT 4.0|Communication via NetBIOS interface|
|SMB 1.0|Windows 2000|Direct connection via TCP|
|SMB 2.0|Windows Vista, Windows Server 2008|Performance upgrades, improved message signing, caching feature|
|SMB 2.1|Windows 7, Windows Server 2008 R2|Locking mechanisms|
|SMB 3.0|Windows 8, Windows Server 2012|Multichannel connections, end-to-end encryption, remote storage access|
|SMB 3.0.2|Windows 8.1, Windows Server 2012 R2||
|SMB 3.1.1|Windows 10, Windows Server 2016|Integrity checking, AES-128 encryption|
*Default Config*: `/etc/samba/smb.conf`
Kali `smbclient //IP`
To enumerate:
```shell-session
smbstatus
```

### RPCclient
rpcclient *Remote Procedure Call* to preform MSRPC functions.

***Enumeration***:
	srvinfo
	enumdomains
	querydominfo
	netshareenumall
	netsharegetinfo

Enumerate Network Shares

***User Enumeration***: 
	enumdomusers
	queryuser XXXX
	querygroup

Dumping User RIDS with ***impacket-samrdump***

***smbmap*** to enum SMB

automated tool ***enum4linux-ng*** 
```shell-session
https://github.com/cddmp/enum4linux-ng.git
```


## NFS
Network File System

|   |   |
|---|---|
|`NFSv2`|It is older but is supported by many systems and was initially operated entirely over UDP.|
|`NFSv3`|It has more features, including variable file size and better error reporting, but is not fully compatible with NFSv2 clients.|
|`NFSv4`|It includes Kerberos, works through firewalls and on the Internet, no longer requires portmappers, supports ACLs, applies state-based operations, and provides performance improvements and high security. It is also the first version to have a stateful protocol.|

***default config***
```shell-session
/etc/exports 
```

Ports *111* and *2049* are essential

Nmap script to map NFS
```shell-session
sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049
```

***Showmount*** to show available nfs shares
```shell-session
showmount -e 10.129.14.128
```

***Mounting NFS Shares***
```shell-session
mkdir target-NFS
sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
cd target-NFS
tree .
```

unmount with *unmount*

## DNS
|**Server Type**|**Description**|
|---|---|
|`DNS Root Server`|The root servers of the DNS are responsible for the top-level domains (`TLD`). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The [Internet Corporation for Assigned Names and Numbers](https://www.icann.org/) (`ICANN`) coordinates the work of the root name servers. There are `13` such root servers around the globe.|
|`Authoritative Nameserver`|Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point.|
|`Non-authoritative Nameserver`|Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.|
|`Caching DNS Server`|Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.|
|`Forwarding Server`|Forwarding servers perform only one function: they forward DNS queries to another DNS server.|
|`Resolver`|Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.|
DNS mainly unencrypted
![[Pasted image 20241115003247.png]]

|**DNS Record**|**Description**|
|---|---|
|`A`|Returns an IPv4 address of the requested domain as a result.|
|`AAAA`|Returns an IPv6 address of the requested domain.|
|`MX`|Returns the responsible mail servers as a result.|
|`NS`|Returns the DNS servers (nameservers) of the domain.|
|`TXT`|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|`CNAME`|This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.|
|`PTR`|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|`SOA`|Provides information about the corresponding DNS zone and email address of the administrative contact.|

***DNS server software called Bind9*** Most common for linux
```shell-session
cat /etc/bind/named.conf.local
```

***Dangerous Settings***

|   |   |
|---|---|
|`allow-query`|Defines which hosts are allowed to send requests to the DNS server.|
|`allow-recursion`|Defines which hosts are allowed to send recursive requests to the DNS server.|
|`allow-transfer`|Defines which hosts are allowed to receive zone transfers from the DNS server.|
|`zone-statistics`|Collects statistical data of zones.|
Dig @10.129.85.246 to search a specific dns server. We need to know the subdomain.

For dns enumeration we need to use dnsenum 

***AXFR*** Zone Transfers
can add internal. subdomain to check for internal zone transfers.

***Do AXFR then do other subdomains listed if succeeds***
```shell
dnsenum --dnsserver STMIP --enum -p 0 -s 0 -f /usr/share/SecLists/Discovery/DNS/namelist.txt dev.inlanefreight.htb
```

## SMTP
Simple Mail Transfer Protocol (Mail Server)
To prevent spam most SMTP servers use ESMTP with SMTP-AUTH

STMP works unencrypted and transmits all data in plaintext.

| Client (`MUA`)  | `➞` | Submission Agent (`MSA`) | `➞` | Open Relay (`MTA`)  | `➞` | Mail Delivery Agent (`MDA`) | `➞` | Mailbox (`POP3`/`IMAP`) |
| --------------- | --- | ------------------------ | --- | ------------------- | --- | --------------------------- | --- | ----------------------- |
| Mail user Agent |     | Mail Submission Agent    |     | Mail Transfer Agent |     |                             |     |                         |

#### Disadvantages
- SMTP doesnt return usable delivery confirmation.
- Users are not authenticated when a connection is established. Open SMTP are often misused to spam en-masse

|**Command**|**Description**|
|---|---|
|`AUTH PLAIN`|AUTH is a service extension used to authenticate the client.|
|`HELO`|The client logs in with its computer name and thus starts the session.|
|`MAIL FROM`|The client names the email sender.|
|`RCPT TO`|The client names the email recipient.|
|`DATA`|The client initiates the transmission of the email.|
|`RSET`|The client aborts the initiated transmission but keeps the connection between client and server.|
|`VRFY`|The client checks if a mailbox is available for message transfer.|
|`EXPN`|The client also checks if a mailbox is available for messaging with this command.|
|`NOOP`|The client requests a response from the server to prevent disconnection due to time-out.|
|`QUIT`|The client terminates the session.|
```shell-session
sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```
Open Relay script to enumerate open smtp server.

***SMTP-USER-ENUM*** to enumerate smtp users
```
smtp-user-enum -M VRFY -U ../SecLists/Usernames/Names/names.txt -t 10.129.85.246 -m 60 -w 20
```

## IMAP/POP3
with *Internet Message Access Protocol* access to emails directly from mail server is possible
POP3 is not.

#### IMAP Commands

|**Command**|**Description**|
|---|---|
|`1 LOGIN username password`|User's login.|
|`1 LIST "" *`|Lists all directories.|
|`1 CREATE "INBOX"`|Creates a mailbox with a specified name.|
|`1 DELETE "INBOX"`|Deletes a mailbox.|
|`1 RENAME "ToRead" "Important"`|Renames a mailbox.|
|`1 LSUB "" *`|Returns a subset of names from the set of names that the User has declared as being `active` or `subscribed`.|
|`1 SELECT INBOX`|Selects a mailbox so that messages in the mailbox can be accessed.|
|`1 UNSELECT INBOX`|Exits the selected mailbox.|
|`1 FETCH <ID> all`|Retrieves data associated with a message in the mailbox.|
|`1 CLOSE`|Removes all messages with the `Deleted` flag set.|
|`1 LOGOUT`|Closes the connection with the IMAP server.|

#### POP3 Commands

|**Command**|**Description**|
|---|---|
|`USER username`|Identifies the user.|
|`PASS password`|Authentication of the user using its password.|
|`STAT`|Requests the number of saved emails from the server.|
|`LIST`|Requests from the server the number and size of all emails.|
|`RETR id`|Requests the server to deliver the requested email by ID.|
|`DELE id`|Requests the server to delete the requested email by ID.|
|`CAPA`|Requests the server to display the server capabilities.|
|`RSET`|Requests the server to reset the transmitted information.|
|`QUIT`|Closes the connection with the POP3 server.|

### Default Ports
*110/995* are default ports for ***POP3***
*143/993* are default ports for ***IMAP***

we can curl with imaps:// protocol

OPENSSL to enumerate imap server

```
openssl s_client -connect 10.129.85.246:imaps
```

When connecting and interracting with imaps we need to use tagX commands these are just unique identifiers used to query the server we can do this with whatever number but if we want to chain commands we use the same one.

#### Enumerating a IMAP server
```
tag0 LIST "" "*"
tag1 SELECT "RESULT"
tag3 FETCH 1 (BODY[])
```

## SNMP (Simple Network Management Protocol)
Created to monitor network devices.
Configuration Tasks and settings can be set over this network protocol.

### MIB
Management Information Base independent format for storing device information to ensure compatability

### OIP
OIP represents a node in a hierarchical namespace, Sequence of numbers identifies each node, allowing nodes position in tree to be determined. 

### SNMP v1
used for network management and monitoring

#### SNMPWALK 
If SNMP is unauthenticated then we can query results with snmpwalk 
```shell-session
snmpwalk -v2c -c public 10.129.14.128
```

## MySQL
Using nmap --script mysql* we can enumerate mysql logins
```shell-session
nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

| **Command**                                          | **Description**                                                                                       |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `mysql -u <user> -p<password> -h <IP address>`       | Connect to the MySQL server. There should **not** be a space between the '-p' flag, and the password. |
| `show databases;`                                    | Show all databases.                                                                                   |
| `use <database>;`                                    | Select one of the existing databases.                                                                 |
| `show tables;`                                       | Show all available tables in the selected database.                                                   |
| `show columns from <table>;`                         | Show all columns in the selected database.                                                            |
| `select * from <table>;`                             | Show everything in the desired table.                                                                 |
| `select * from <table> where <column> = "<string>";` | Search for needed `string` in the desired table.                                                      |
## MSSQL
Microsofts SQL client/server
use Impacket's mssql client impacket-mssqlclient.
```
impacket-mssqlclient  backdoor@10.129.101.105 -windows-auth
```

```shell-session
nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```

Metasploit has enumeration techniques *mssql_ping*

select name from sys.databases

## Oracle TNS
Server that facilitates communication between oracle databases. port 1521

```shell-session
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

## IPMI
Intelligent Platform Management Interface (Port 623) Gives the ability to monitor systems even if they're off.

IPMI is typically used in three ways:

- Before the OS has booted to modify BIOS settings
- When the host is fully powered down
- Access to a host after a system failure

Metasploit version scan 
```shell-session
auxiliary/scanner/ipmi/ipmi_version 
```

Metasploit can dump hashes
```shell-session
auxiliary/scanner/ipmi/ipmi_dumphashes 
```

## Linux Remote Management Protocols
### SSH
Encrypted Direct Connection
OpenSSH has six different authentication methods:

1. Password authentication
2. Public-key authentication
3. Host-based authentication
4. Keyboard authentication
5. Challenge-response authentication
6. GSSAPI authentication

#### SSH-Audit
https://github.com/jtesta/ssh-audit.git

### Rsync
Effecient tool for remotely copying files, to copy files locally to and from a remote host.
PORT 873

we can probe for shares with netcat
```shell-session
nc -nv 127.0.0.1 873
```

Once we have an open share we can enumerate it further
```shell-session
rsync -av --list-only rsync://127.0.0.1/dev
```

|          |           |     |     |                                                                                                                                                                                                                                                                            |
| -------- | --------- | --- | --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rcp`    | `rshd`    | 514 | TCP | Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the `cp` command on Linux but provides `no warning to the user for overwriting existing files on a system`.        |
| `rsh`    | `rshd`    | 514 | TCP | Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files for validation.                                                                                                                 |
| `rexec`  | `rexecd`  | 512 | TCP | Enables a user to run shell commands on a remote machine. Requires authentication through the use of a `username` and `password` through an unencrypted network socket. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files. |
| `rlogin` | `rlogind` | 513 | TCP | Enables a user to log in to a remote host over the network. It works similarly to `telnet` but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the `/etc/hosts.equiv` and `.rhosts` files.                                     |

## Windows Remote Management Protocols
The main components used for remote management of Windows and Windows servers are the following:
- Remote Desktop Protocol (`RDP`)
- Windows Remote Management (`WinRM`)
- Windows Management Instrumentation (`WMI`)

### RDP
Remote Desktop Protocol 3389
```shell-session
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

Cisco have a script to check RDP security
```shell-session
https://github.com/CiscoCXSecurity/rdp-sec-check.git
```

### WinRM
Windows Remote Management 5985 5986
```shell-session
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

Evil-winrm

### WMI
Windows Management Instrumentation 
```shell-session
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```

