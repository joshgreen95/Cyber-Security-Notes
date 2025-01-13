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