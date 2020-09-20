# DNS Nameserver and BERKELEY INTERNET NAME DOMAIN (BIND)
Reference:

[The Domain Name System](https://www.pks.mpg.de/~mueller/docs/suse10.1/suselinux-manual_en/manual/cha.dns.html)

[Domain name](https://en.wikipedia.org/wiki/Domain_name)

[WHAT IS A DNS ZONE? DNS ZONES EXPLAINED](https://ns1.com/resources/dns-zones-explained)

[DNS zone](https://en.wikipedia.org/wiki/DNS_zone)

[BIND_Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/ch-bind)


## DNS Terminology
### Domain name
A domain name consists of one or more parts, technically called labels, that are conventionally concatenated, and delimited by dots, such as example.com. Any name registered in the DNS is a domain name. Domain names are organized in subordinate levels (subdomains) of the DNS root domain, which is nameless. The first-level set of domain names are the top-level domains (TLDs), such as com and org. Below these top-level domains in the DNS hierarchy are the second-level and third-level domain names that are typically open for reservation by end-users who wish to connect local area networks to the Internet.

### Fully qualified domain name (FQDN)
A fully qualified domain name (FQDN) is a domain name that is completely specified with all labels in the hierarchy of the DNS, having no parts omitted. Labels in the Domain Name System are case-insensitive.

### Hostname
```
bob.sales.example.com
```
When looking at how an FQDN is resolved to find the IP address that relates to a particular system, read the name from right to left, with each level of the hierarchy divided by periods (.). In this example, **com** defines the **top level** domain for this FQDN. The name **example** is a **sub-domain** under **com**, while **sales** is a **sub-domain** under **example**. The name furthest to the left, **bob**, **identifies a specific machine hostname**.

### Zones
Except for the hostname, each section is called a zone, which defines a specific namespace. A namespace controls the naming of the sub-domains to its left. While this example only contains two sub-domains, an FQDN **must contain at least one sub-domain** but may include many more, depending upon how the namespace is organized.

### DNS Nameserver
The DNS nameserver is a server that maintains the name and IP information for a domain. You can have a primary DNS server for master zone, a secondary server for slave zone, or a slave server without any zones for caching.

### Zone Files
Zones are defined on authoritative nameservers through the use of **zone files** (which describe the namespace of that zone), the mail servers to be used for a particular domain or sub-domain, and more. 

#### Master/Slave nameservers
Zone files are stored on primary nameservers (also called master nameservers), which are truly authoritative and where changes are made to the files and secondary nameservers (also called slave nameservers), which receive their zone files from the primary nameservers. Any nameserver can be a primary and secondary nameserver for different zones at the same time, and they may also be considered authoritative for multiple zones. It all depends on how the nameserver is configured.

### DNS Nameserver Types
A nameserver may be one or more of these types. For example, a nameserver can be a master for some zones, a slave for others, and only offer forwarding resolutions for others.
#### Master
Stores original and authoritative zone records for a namespace, and answers queries about the namespace from other nameservers.
#### Slave
Answers queries from other nameservers concerning namespaces for which it is considered an authority. However, slave nameservers get their namespace information from master nameservers.
#### Caching-only
Offers name-to-IP resolution services, but is not authoritative for any zones. Answers for all resolutions are cached in memory for a fixed period of time, which is specified by the retrieved zone record.
#### Forwarding
Forwards requests to a specific list of nameservers for name resolution. If none of the specified nameservers can perform the resolution, the resolution fails.

## BIND as a Nameserver
BIND performs **name resolution services** through the **/usr/sbin/named** daemon (a daemon is a program that runs continuously and exists for the purpose of handling periodic service requests). BIND also includes an administration utility called /usr/sbin/rndc.

### BIND Configurations 
BIND stores its configuration files in the following locations:
* **/etc/named.conf**: The configuration file for the **named** daemon
* **/var/named/**: The **named** working directory which stores zone, statistic, and cache files

### /ETC/NAMED.CONF
The **named.conf** file is a collection of statements using nested options surrounded by opening and closing ellipse characters, **{ }**. Administrators must be careful when editing **named.conf** to avoid syntax errors as many seemingly minor errors prevent the **named** service from starting.

A typical named.conf file is organized similar to the following example:
```
<statement-1> ["<statement-1-name>"] [<statement-1-class>] {
	<option-1>;
	<option-2>;
	<option-N>;
};
<statement-2> ["<statement-2-name>"] [<statement-2-class>] {
	<option-1>;
	<option-2>;
	<option-N>;
};
<statement-N> ["<statement-N-name>"] [<statement-N-class>] {
	<option-1>;
	<option-2>;
	<option-N>;
};
```
#### Common Statement Types
The following types of statements are commonly used in **/etc/named.conf**:

##### acl Statement
The **acl** statement (or access control statement) defines groups of hosts which can then be permitted or denied access to the nameserver.

An **acl** statement takes the following form:
```
acl <acl-name> {
	<match-element>;
	[<match-element>; ...]
};
```
In this statement, replace **acl-name** with the name of the access control list and replace **match-element** with a semi-colon separated list of IP addresses. Most of the time, an individual IP address or IP network notation (such as **10.0.1.0/24**) is used to identify the IP addresses within the acl statement.

The following access control lists are already defined as keywords to simplify configuration:
* **any** — Matches every IP address
* localhost — Matches any IP address in use by the local system
* localnets — Matches any IP address on any network to which the local system is connected
* none - Matches no IP addresses

When used in conjunction with other statements (such as the **options** statement), **acl** statements can be very useful in preventing the misuse of a BIND nameserver.

The following example defines two access control lists and uses an options statement to define how they are treated by the nameserver:
```
acl black-hats {
	10.0.2.0/24;
	192.168.0.0/24;
};
acl red-hats {
	10.0.1.0/24;
};
options {
	blackhole { black-hats; };
	allow-query { red-hats; };
	allow-recursion { red-hats; };
};
```

This example contains two access control lists, **black-hats** and **red-hats**. Hosts in the **black-hats** list are **denied** access to the nameserver, while hosts in the **red-hats** list are **given normal access**.

##### include Statement
The **include** statement allows files to be included in a **named.conf** file. In this way, sensitive configuration data (such as **keys**) can be placed in a separate file with restrictive permissions.

An **include** statement takes the following form:
```
include "<file-name>"
```
In this statement, **file-name** is replaced with an absolute path to a file.

##### options Statement
The **options** statement defines global server configuration options and sets defaults for other statements. It can be used to specify the location of the **named** working directory, the types of queries allowed, and much more.
The **options** statement takes the following form:
```
options {
	<option>;
	[<option>; ...]
};
```
In this statement, the **option** directives are replaced with a valid option.
The following are commonly used options:
* allow-query: Specifies which hosts are allowed to query this nameserver. By default, all hosts are allowed to query. An access control list, or collection of IP addresses or networks, may be used here to allow only particular hosts to query the nameserver.
* allow-recursion: Similar to allow-query, this option applies to recursive queries. By default, all hosts are allowed to perform recursive queries on the nameserver.
* blackhole: Specifies which hosts are not allowed to query the server.
* directory: Specifies the named working directory if different from the default value, /var/named/.
* forwarders: Specifies a list of valid IP addresses for nameservers where requests should be forwarded for resolution.
* forward: Specifies the forwarding behavior of a forwarders directive. The following options are accepted:
	* first — Specifies that the nameservers listed in the forwarders directive be queried before named attempts to resolve the name itself.
	* only — Specifies that named does not attempt name resolution itself in the event that queries to nameservers specified in the forwarders directive fail.
* listen-on: Specifies the network interface on which named listens for queries. By default, all interfaces are used. Using this directive on a DNS server which also acts a gateway, BIND can be configured to only answer queries that originate from one of the networks.
	```
	options {
		listen-on { 10.0.1.1; };
	};
	```
	In this example, only requests that arrive from the network interface serving the private network (10.0.1.1) are accepted.
* statistics-file: Specifies an alternate location for statistics files. By default, named statistics are saved to the /var/named/named.stats file

#### zone Statement
A **zone** statement defines the characteristics of a zone, such as the location of its configuration file and zone-specific options. This statement can be used to override the global options statements.
A **zone** statement takes the following form:
```
zone <zone-name> <zone-class> {
	<zone-options>;
	[<zone-options>; ...]
};
```
In this statement, **zone-name** is the name of the zone, **zone-class** is the optional class of the zone, and **zone-options** is a list of options characterizing the zone.

The **zone-name** attribute for the zone statement is particularly important. It is the default value assigned for the **$ORIGIN** directive used within the corresponding zone file located in the **/var/named/** directory. The named daemon appends the name of the zone to any non-fully qualified domain name listed in the zone file.

For example, if a **zone** statement defines the namespace for **example.com**, use **example.com** as the **zone-name** so it is placed at the end of hostnames within the **example.com** zone file.

The most common zone statement options include the following:
* allow-query: Specifies the clients that are allowed to request information about this zone. The default is to allow all query requests.
* allow-transfer: Specifies the slave servers that are allowed to request a transfer of the zone's information. The default is to allow all transfer requests.
* allow-update: Specifies the hosts that are allowed to dynamically update information in their zone. The default is to deny all dynamic update requests. 

	Be careful when allowing hosts to update information about their zone. Do not enable this option unless the host specified is completely trusted. 

	In general, it is better to have an administrator manually update the records for a zone and reload the named service.
* file: Specifies the name of the file in the named working directory that contains the zone's configuration data.
* masters: Specifies the IP addresses from which to request authoritative zone information and is used only if the zone is defined as type slave.
* notify: Specifies whether or not named notifies the slave servers when a zone is updated. This directive accepts the following options:
	* yes — Notifies slave servers.
	* no — Does not notify slave servers.
	* explicit — Only notifies slave servers specified in an also-notify list within a zone statement.
* type: Defines the type of zone. Below is a list of valid options:
	* delegation-only — Enforces the delegation status of infrastructure zones such as COM, NET, or ORG. Any answer that is received without an explicit or implicit delegation is treated as NXDOMAIN. This option is only applicable in TLDs or root zone files used in recursive or caching implementations.
	* forward — Forwards all requests for information about this zone to other nameservers.
	* hint — A special type of zone used to point to the root nameservers which resolve queries when a zone is not otherwise known. No configuration beyond the default is necessary with a hint zone.
	* master — Designates the nameserver as authoritative for this zone. A zone should be set as the master if the zone's configuration files reside on the system.
	* slave — Designates the nameserver as a slave server for this zone. Also specifies the IP address of the master nameserver for the zone.

#### Sample zone Statements
Most changes to the **/etc/named.conf** file of a master or slave nameserver involves adding, modifying, or deleting zone statements. While these zone statements can contain many options, most nameservers require only a small subset to function efficiently. The following zone statements are very basic examples illustrating a master-slave nameserver relationship.

The following is an example of a zone statement for the primary nameserver hosting example.com (192.168.0.1):
```
zone "example.com" IN {
	type master;
	file "example.com.zone";
	allow-update { none; };
};
```

In the statement, the zone is identified as example.com, the type is set to master, and the named service is instructed to read the /var/named/example.com.zone file. It also tells named not to allow any other hosts to update.

A slave server's zone statement for example.com is slightly different from the previous example. For a slave server, the type is set to slave and in place of the allow-update line is a directive telling named the IP address of the master server.

The following is an example slave server zone statement for example.com zone:
```
zone "example.com" {
	type slave;
	file "example.com.zone";
	masters { 192.168.0.1; };
};
```
This zone statement configures named on the slave server to query the master server at the 192.168.0.1 IP address for information about the example.com zone. The information that the slave server receives from the master server is saved to the /var/named/example.com.zone file.

#### Other Statement Types
* controls: Configures various security requirements necessary to use the rndc command to administer the named service.
* **logging**: Allows for the use of multiple types of logs, called channels. By using the channel option within the logging statement, a customized type of log can be constructed — with its own file name (file), size limit (size), versioning (version), and level of importance (severity). Once a customized channel is defined, a category option is used to categorize the channel and begin logging when named is restarted.

	By default, named logs standard messages to the syslog daemon, which places them in /var/log/messages. This occurs because several standard channels are built into BIND with various severity levels, such as default_syslog (which handles informational logging messages) and default_debug (which specifically handles debugging messages). A default category, called default, uses the built-in channels to do normal logging without any special configuration.

	Customizing the logging process can be a very detailed process and is beyond the scope of this chapter. For information on creating custom BIND logs, refer to the BIND 9 Administrator Reference Manual referenced [“Installed Documentation”](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/s1-bind-additional-resources#s2-bind-installed-docs).

#### Comment Tags
The following is a list of valid comment tags used within named.conf:
* // — When placed at the beginning of a line, that line is ignored by named.
* \# — When placed at the beginning of a line, that line is ignored by named.

### Zone Files
Zone files contain information about a namespace and are stored in the named working directory (**/var/named/**) by default. Each zone file is named according to the file option data in the zone statement, usually in a way that relates to the domain in question and identifies the file as containing zone data, such as **example.com.zone**.

Each zone file may contain **directives** and **resource** records. Directives tell the nameserver to perform tasks or apply special settings to the zone. Resource records define the parameters of the zone and assign identities to individual hosts. Directives are optional, but resource records are required to provide name service to a zone.

All directives and resource records should be entered on individual lines.

Comments can be placed after semicolon characters (;) in zone files.

#### Zone File Directives
Directives begin with the dollar sign character ($) followed by the name of the directive. They usually appear at the top of the zone file.
The following are commonly used directives:
* $INCLUDE: Configures named to include another zone file in this zone file at the place where the directive appears. This allows additional zone settings to be stored apart from the main zone file.
* $ORIGIN: Appends the domain name to unqualified records, such as those with the hostname and nothing more.

	For example, a zone file may contain the following line:
	```
	$ORIGIN example.com.
	```
	Any names used in resource records that do not end in a trailing period (.) are appended with example.com.
* **$TTL**: Sets the default Time to Live (TTL) value for the zone. This is the length of time, in seconds, that a zone resource record is valid. Each resource record can contain its own TTL value, which overrides this directive. 
	
	Increasing this value allows remote nameservers to cache the zone information for a longer period of time, reducing the number of queries for the zone and lengthening the amount of time required to proliferate resource record changes.

#### Zone File Resource Records
The primary component of a zone file is its resource records.

There are many types of zone file resource records. The following are used most frequently:

* A: This refers to the Address record, which specifies an IP address to assign to a name, as in this example:
	```
	<host> IN A <IP-address> 
	```
	If the **host** value is omitted, then an A record points to a default IP address for the top of the namespace. This system is the target for all non-FQDN requests.

	Consider the following A record examples for the example.com zone file:
	```
	server1	IN	A	10.0.1.3
					IN	A	10.0.1.5
	```
	Requests for domain.com are pointed to 10.0.1.3, while requests for server1.domain.com are pointed to 10.0.1.5.
* CNAME: This refers to the Canonical Name record, which maps one name to another. This type of record can also be referred to as an alias record.
	
	The next example tells named that any requests sent to the **alias-name** should point to the host, **real-name**. CNAME records are most commonly used to point to services that use a common naming scheme, such as www for Web servers.
	```
	<alias-name> IN CNAME <real-name> 
	```
	In the following example, an **A** record binds a hostname to an IP address, while a **CNAME** record points the commonly used www hostname to it.
	```
	server1		IN	A	10.0.1.5
	www		IN	CNAME	server1
	```
* NS: This refers to the NameServer record, which announces the authoritative nameservers for a particular zone.
	
	The following illustrates the layout of an **NS** record:
	```
	IN NS <nameserver-name> 
	```

	Here, **nameserver-name** should be an FQDN.
	
	Next, two nameservers are listed as authoritative for the domain. It is not important whether these nameservers are slaves or if one is a master; they are both still considered authoritative.
	```
	IN     NS     dns1.example.com.
	IN     NS     dns2.example.com.
	```
* SOA: This refers to the Start Of Authority resource record, which proclaims important authoritative information about a namespace to the nameserver.
	
	Located after the directives, an SOA resource record is the first resource record in a zone file.
	
	The following shows the basic structure of an SOA resource record:
	```
	@  IN	SOA  <primary-name-server>  <hostmaster-email> (
	<serial-number>
	<time-to-refresh>
	<time-to-retry>
	<time-to-expire>
	<minimum-TTL> )
	```
	The **@** symbol places the $ORIGIN directive (or the zone's name, if the $ORIGIN directive is not set) as the namespace being defined by this SOA resource record. The hostname of the primary nameserver that is authoritative for this domain is the **primary-name-server** directive, and the email of the person to contact about this namespace is the **hostmaster-email** directive.
	
	The **serial-number** directive is a numerical value incremented every time the zone file is altered to indicate it is time for named to reload the zone. The <time-to-refresh> directive is the numerical value slave servers use to determine how long to wait before asking the master nameserver if any changes have been made to the zone. The <serial-number> directive is a numerical value used by the slave servers to determine if it is using outdated zone data and should therefore refresh it.
	
	The **time-to-retry** directive is a numerical value used by slave servers to determine the length of time to wait before issuing a refresh request in the event that the master nameserver is not answering. If the master has not replied to a refresh request before the amount of time specified in the **time-to-expire** directive elapses, the slave servers stop responding as an authority for requests concerning that namespace.
	
	In BIND 4 and 8, the **minimum-TTL** directive is the amount of time other nameservers cache the zone's information. However, in BIND 9, the **minimum-TT** directive defines how long negative answers are cached for. Caching of negative answers can be set to a maximum of 3 hours (3H).
	
	When configuring BIND, all times are specified in seconds.

	The following example illustrates the form an SOA resource record might take when it is populated with real values.
	```
	@	IN	SOA	dns1.example.com.	hostmaster.example.com. (
		2001062501 ; serial
		21600      ; refresh after 6 hours
		3600       ; retry after 1 hour
		604800     ; expire after 1 week
		86400 )    ; minimum TTL of 1 day
	```

#### Example Zone File
Seen individually, directives and resource records can be difficult to grasp. However, when placed together in a single file, they become easier to understand.

The following example shows a very basic zone file.
```
$ORIGIN example.com.
$TTL 86400
@		IN	SOA	dns1.example.com.	hostmaster.example.com. (
			2001062501 ; serial
			21600      ; refresh after 6 hours
			3600       ; retry after 1 hour
			604800     ; expire after 1 week
			86400 )    ; minimum TTL of 1 day
;
;
		IN	NS	dns1.example.com.
		IN	NS	dns2.example.com.
dns1		IN	A	10.0.1.1
		IN	AAAA	aaaa:bbbb::1
dns2		IN	A	10.0.1.2
		IN	AAAA	aaaa:bbbb::2
;
;
@		IN	MX	10	mail.example.com.
		IN	MX	20	mail2.example.com.
mail		IN	A	10.0.1.5
		IN	AAAA	aaaa:bbbb::5
mail2		IN	A	10.0.1.6
		IN	AAAA	aaaa:bbbb::6
;
;
; This sample zone file illustrates sharing the same IP addresses
; for multiple services:
;
services	IN	A	10.0.1.10
		IN	AAAA	aaaa:bbbb::10
		IN	A	10.0.1.11
		IN	AAAA	aaaa:bbbb::11
ftp		IN	CNAME	services.example.com.
www		IN	CNAME	services.example.com.
;
;
```
In this example, standard directives and SOA values are used. The authoritative nameservers are set as dns1.example.com and dns2.example.com, which have A records that tie them to 10.0.1.1 and 10.0.1.2, respectively.

The email servers configured with the MX records point to mail and mail2 via A records. Since the mail and mail2 names do not end in a trailing period (.), the $ORIGIN domain is placed after them, expanding them to mail.example.com and mail2.example.com. Through the related A resource records, their IP addresses can be determined.

Services available at the standard names, such as www.example.com (WWW), are pointed at the appropriate servers using a CNAME record.

This zone file would be called into service with a zone statement in the named.conf similar to the following:
```
zone "example.com" IN {
	type master;
	file "example.com.zone";
	allow-update { none; };
};
```

#### Reverse Name Resolution Zone Files
See [Reverse Name Resolution Zone Files](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/s1-bind-zone#s2-bind-configuration-zone-reverse)

## Configuring the Hosts and Hostname
### Hosts
The **Hosts** tab allows you to add, edit, or remove hosts from the **/etc/hosts** file. This file contains IP addresses and their corresponding hostnames.

When your system tries to resolve a hostname to an IP address or tries to determine the hostname for an IP address, it refers to the **/etc/hosts** file before using the name servers (if you are using the default Red Hat Enterprise Linux configuration). If the IP address is listed in the /etc/hosts file, the name servers are not used. If your network contains computers whose IP addresses are not listed in DNS, it is recommended that you add them to the /etc/hosts file.

To change lookup order, edit the **/etc/host.conf** file. The line order hosts, bind specifies that /etc/hosts takes precedence over the name servers.

### Host Names
#### Understanding Host Names
There are three classes of hostname: static, pretty, and transient.

The “static” host name is the traditional hostname, which can be chosen by the user, and is stored in the /etc/hostname file. See the definition above (A hostname is a name which is given to a computer whne attached to the network. Its main purpose is to uniquely identify over a network.). It should look like this:

![hostname](/assets/img/hostname.jpg)

## Other Useful Reading
### Another Definition of Hostname
A hostname is a domain name that **has at least one associated IP address**. For example, the domain names www.example.com and example.com are also hostnames, whereas the com domain is not. However, other top-level domains, particularly country code top-level domains, may indeed have an IP address, and if so, they are also hostnames.

### Host
A network host is a computer or other device connected to a computer network. Specifically, computers participating in the Internet are called Internet hosts, sometimes Internet nodes. Internet hosts and other IP hosts **have one or more IP addresses** assigned to their network interfaces.

### Domain
A network domain is an administrative grouping of multiple private computer networks or hosts within the same infrastructure. Domains can be identified using a domain name;

### Domain Name Space
Today, the Internet Corporation for Assigned Names and Numbers (ICANN) manages the top-level development and architecture of the Internet domain name space. It authorizes domain name registrars, through which domain names may be registered and reassigned. 

The domain name space consists of a tree of domain names. Each node in the tree holds information associated with the domain name. The tree sub-divides into zones beginning at the DNS root zone.

![domain_name_system](/assets/img/domain_name_system.png)

### Domain name syntax
A domain name consists of one or more parts, technically called labels, that are conventionally concatenated, and delimited by dots, such as example.com.
* The right-most label conveys the top-level domain; for example, the domain name www.example.com belongs to the top-level domain com.
* The hierarchy of domains descends from the right to the left label in the name; each label to the left specifies a subdivision, or subdomain of the domain to the right. For example: the label example specifies a node example.com as a subdomain of the com domain, and www is a label to create www.example.com, a subdomain of example.com. The empty label is reserved for the root node and when fully qualified is expressed as the empty label terminated by a dot.
* Hostnames impose restrictions on the characters allowed in the corresponding domain name. A valid hostname is also a valid domain name, but a valid domain name may not necessarily be valid as a hostname.

### Another Definition of DNS Zone
A DNS zone is any distinct, contiguous portion of the domain name space in the Domain Name System (DNS) for which administrative responsibility has been delegated to a single manager. 

The domain name space of the Internet is organized into a hierarchical layout of subdomains below the DNS root domain. The individual domains of this tree may serve as delegation points for administrative authority and management. However, usually it is furthermore desirable to implement fine-grained boundaries of delegation, so that multiple sub-levels of a domain may be managed independently. Therefore, the domain name space is partitioned into areas (zones) for this purpose. A zone starts at a domain and extends downward in the tree to the leaf nodes or to the top-level of subdomains where other zones start.

### DNS Record
The record is information about name and IP address. Some special records are:
* NS record: An NS record tells name servers which machines are in charge of a given domain zone.
* MX record: The MX (mail exchange) records describe the machines to contact for directing mail across the Internet.
* SOA record: SOA (Start of Authority) record is the first record in a zone file. The SOA record is used when using DNS to synchronize data between multiple computers.
* A record: IPv4 Address Mapping records - a hostname and its IPv4 address.
* AAAA record: IPv6 Address records — a hostname and its IPv6 address.