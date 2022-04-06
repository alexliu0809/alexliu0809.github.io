References:

[1](https://classes.cs.uchicago.edu/archive/2021/winter/23200-1/10.pdf),
[2](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.348.4534&rep=rep1&type=pdf),
[3](https://computing.ece.vt.edu/~jkh/Understanding_SSL_TLS.pdf),
[4](https://cs249i.stanford.edu/lectures/CS249i-WebPKI.pdf),
[5](https://www.cs.columbia.edu/~suman/security_arch/ssl.pdf),
[6](https://hpbn.co/transport-layer-security-tls/),
[7](https://www.cse.scu.edu/~bdezfouli/publication/MSWIM2019_SCU_SIOTLAB.pdf)

# SSL/TLS certificates ecosystem
## What is SSL/TLS?
SSL stands for Secure Sockets Layer and TLS stands for Transport Layer Security. SSL protocol is TLS protocol's predecessor. They share the same protocol design but use different crypto algorithms. It is **De facto standard** for Internet security. 

## Threat Model
It is widely End-to-end secure communications in the presence of a network attacker, who may completely own the network: controls Wi-Fi, DNS, routers, his own websites, can listen to any packet, modify packets in transit, inject his own packets into the network. However, a compromised endpoint (e.g., your own laptop) is not considered in the threat model.

## History of the Protocol
* SSL 2.0 - 1994
* SSL 3.0 - 1996
* TLS 1.0 - 1999
	* Based on SSL 3.0, but not interoperable (uses different cryptographic algorithms)
* TLS 1.1 - 2006

## TLS in the Protocol Stack
TLS protocol is implemented at the application layer, directly on top of TCP, enabling protocols above it (HTTP, email, instant messaging, and many others) to operate unchanged while providing communication security when communicating across the network. 

## TLS Basics
TLS consists of two main layers: the handshake protocol layer and the record layer.
* Handshake protocol: Runs between a client and a server (e.g., client = Web browser, server = website). It uses public-key cryptography for identity authentication and allows the negotiation of a cipher suite, which consists of a set of cryptographic algorithms. This enables data confidentiality and integrity later in the record layer. 
* Record protocol: Uses the secret keys established in the handshake protocol to protect confidentiality, integrity, and authenticity of data exchange between the client and the server.

## TLS Ecosystem
Clients are usually browsers, and servers typically web servers such as Apache httpd and Microsoft IIS. Server authentication occurs via a certificate signed by a trusted certification authority.

Concretely, when Alice visits https://www.bob.com, her browser connects to www.bob.com over port 443 where the server responds with a certificate. Then, the browser validates that a trusted certification authority signed it, and checks that it pertains to www.bob.com and no other host. If these checks succeed, the browser uses the public key in the certificate to setup a secure channel with www.bob.com.

### Certification Authorities
**CA**: stands for certificate authority, which is an agency responsible for certifying public keys and verifying the identities of certificate requestors and domain ownership.

**Root CAs**: Browsers are pre-configured with 100+ of trusted CAs. Browsers ship with a set of "trusted" CAs called the **root CA** (aka root stores). A public key for any website in the world will be accepted by the browser if certified by one of these CAs.

**Intermediate/Lower Level CAs**: A CA in the trusted root store can also create and sign certificates for other intermediate CAs; A Root CA signs certificates for intermediate CAs, they sign certificates for lower-level CAs, etc. The trust relationship is transitive. For example, Verisign can sign a UCSD certificate, which in turns signs a certificate for me.

### TLS Certificate
TLS uses X.509 certificate schema (defines what fields in what order). It include:
* Serial number
* CA info (public key, name, etc)
* Common name of subject
* Public key of subject
* Expiration date
* Supported protocols
* Extensions (possibly many)

### Issuing a TLS Cert
Goals: 
* verify that a network identifier (i.e., IP address or DNS Name) controls some cryptographic public key.
* generate a certificate that attests to this linkage

#### Historical Issuance
* Confirming the Applicant as the Domain Name Registrant directly with the Domain Name Registrar
* Communicating directly with Registrant via address, email, or telephone number provided by the Registrar
* Communicating directly with the Registrant using the contact information listed in the WHOIS record's
"registrant", "technical", or "administrative" field

#### Modern Issuance
* Manual Contact (via phones, fax, SMS, Email, WHOIS record, etc.)
* Automatable (via a Random Token), which leverages the protocol defined in RFC 8555 (Automatic Certificate Management Environment).
	* Requestor submits public key and request to CA
	* CA gives a challenge to requestor
	* Requestor places challenge on web server or DNS server, proving ownership
	* CA then issues cert



