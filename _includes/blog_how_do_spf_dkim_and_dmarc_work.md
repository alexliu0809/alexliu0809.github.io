# How do SPF, DKIM and DMARC work?
References:
1. [Envelope vs Header FROM](https://www.xeams.com/difference-envelope-header.htm)
2. [How Many From: Addresses Are There?](https://dmarc.org/2016/07/how-many-from-addresses-are-there/)
3. [SPF Record: Protect your domain reputation and email delivery](https://postmarkapp.com/guides/spf)
4. [SPF Record Syntax](https://dmarcian.com/spf-syntax-table/#all)
5. [Forge Proof your Domain Reputation With DKIM](https://aritic.com/blog/aritic-mail/forge-proof-your-domain-reputation-with-dkim/)
6. [DomainKeys Identified Mail](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)
7. [DMARC: Monitor & secure your email delivery](https://postmarkapp.com/guides/dmarc)
8. [How to implement DMARC “reject” policy for your domain](https://iciber.nl/en/news/how-to-implement-dmarc-reject-policy-for-your-domain/)
9. [What Is A DMARC Record And How Do I Create It On DNS Server?](https://www.sonicwall.com/support/knowledge-base/what-is-a-dmarc-record-and-how-do-i-create-it-on-dns-server/170504796167071/)
10. [How to create a DMARC record](https://www.dmarcanalyzer.com/how-to-create-a-dmarc-record/)

# Anatomy of Email Headers
## How Is An Email Message Like A Postal Letter?
When your postman delivers a physical letter, it’s usually easy to locate the sender’s address. There are addresses on the envelope that handle the routing of the letter. There are also addresses on the letter itself, providing more formal contact information or in case the envelope is discarded after the letter is delivered. In fact the address on the letterhead inside might be a corporate headquarters while a different address is used for the return address on the envelope, so that undeliverable letters get handled specially when they’re returned.

Continuing with the letter itself the sender and recipient addresses, the date, and similar information can be found in the stationary letterhead or above the greeting (“Dear Mr. Watson”). The general content of the message is what the author has written following the greeting. These all have parallels in email messages, if you don’t stretch the analogy too far.

## Comparing The Standards
The Internet Engineering Task Force (IETF) creates the standards used on the Internet, called a Request For Comment (RFC) for historical reasons. These standards can cover the format of documents like email messages, or the details of how those messages are transmitted between one computer and another. And in fact RFC5321 covers the computer-to-computer transmission protocol for email (SMTP), while RFC5322 covers the format of email messages (IMF).

Returning to our comparison, the handling and formatting of the letter’s envelope are analogous to the computer-to-computer transmission protocol, or RFC5321. Likewise the layout and formatting of the business letter inside the envelope is analogous to the email message format, or RFC5322.

![email](/assets/img/posts/email.png)

An email message’s headers and envelope information can be identified very precisely by using the RFC that defines it and the name of the item under that RFC. This table identifies the most commonly discussed items.

![email_headers](/assets/img/posts/email_headers.png)

The following diagram may help make the difference between the transfer protocol (RFC5321) and the message format (RFC5322) clearer. The text above the line between the computers represents the protocol, or commands and information being exchanged – this is described by RFC5321. The page below that line represents the contents of the email message, which is governed by RFC5322.

![rfc_cont](/assets/img/posts/rfc_cont.png)

## Return Paths And Bounce Addresses
(Also see [this](https://en.wikipedia.org/wiki/Bounce_address))
There are two other names in common use that refer to the RFC5321.MailFrom address. 

In the 1980s and 1990s, email messages were transmitted between computers a bit differently. It was frequently the case that messages had to be transmitted from one network of computers to another through special gateways, and maybe a third or fourth time before they reached their destination. And you couldn’t count on Internet services like the Domain Name Service (DNS) to know about all the machines between you and your destination, so you couldn’t just address your message to user@example.com and rely on the computers to figure out how to get it there.

These paths between networks were not always publicized and since it was not always possible to rediscover them on-the-fly, the path a message took was recorded in a special message header called the Return-Path: that could be used when you wanted to reply. As the Internet evolved the practice developed of storing what we now call the RFC5321.MailFrom address in the Return-Path: header. And so today, some people will refer to the Return Path when they mean the RFC5321.MailFrom address – and this is perfectly valid since that’s the official name of the RFC5322 header that captures that address.

When a message is being sent and for some reason can’t be delivered – because the address is incorrect, or the addressee’s mailbox is full, to name just two possibilities – an error or “bounce” message will be sent to the RFC5321.MailFrom address, as a courtesy to let the message author know that their message couldn’t be delivered. Hence the term Bounce Address is sometimes used to refer to the RFC5321.MailFrom address.

**This use of the RFC5321.MailFrom as the Bounce Address is very important**. Companies will set the RFC5321.MailFrom to a special address to make sure they know that those recipients didn’t receive their message. In many cases this is critical. Take the example of a bank sending a customer time-sensitive information – **they may need to know that message hasn’t been delivered** so they can have a representative contact the customer by other means before the deadline passes. This is just like the example earlier where the return address on the postal letter’s envelope might be different from the sending address on the letter inside.


# SPF
## What is SPF
SPF is an open standard that enables the owner of a domain to provide a public list of approved senders. It enables receiving mail servers to check that an email is originated from a server that has the permission to send on your behalf.

An important aspect to understand about SPF is that it does **NOT** validate against the **From** domain. Instead, SPF looks at the **Return-Path** (aka MAIL FROM) header to validate the originating server. **Return-Path** is the email address that receiving servers use to notify the sending mail server of delivery problems, like bounces. So an email can pass SPF regardless of whether the **From** address is fake. The problem with this limitation is that the **From** address is what recipients see in their email clients. Furthermore, even if a message fails SPF, there's no guarantee it won't be delivered. That final decision about delivery is up to the receiving server.

## How do SPF records work?
As mentioned earlier, the key technical detail with SPF is that it works by looking at the domain of the **Return-Path** value included in the email’s headers. The receiving server extracts the domain’s SPF record and then checks if the source email server IP is approved to send emails for that domain.

Receiving servers verify SPF by checking a specific TXT DNS entry in your domain, which includes a list of approved IP addresses. This is one of the key aspects of SPF. By using DNS, it’s able to build on something that every website or application already has. That DNS entry includes several parts that each provide different information to the server.

![SPF](/assets/img/posts/spf.png)

## How does the SPF record syntax work?
Let’s look at a breakdown of the key elements (also called “mechanisms”) in an example SPF record entry of **v=spf1 a mx include:spf.mtasv.net include:_spf.createsend.com ~all**
* **v=spf1** This states which version of SPF is being used.
* **a** This states that if the domain includes an address record (A or AAAA) for the sender’s address, it will match. So, if the IP address of your A record is used to send email, it will pass.
* **mx** The short version is that as long as the email originates from an IP address of the domain’s incoming mail servers, then it’s a match. The recipient server will check the MX record with the highest priority first.
* **include:** The include statements essentially tell receiving servers to include the values for the SPF records at the specified domain. These records generally specify a set of IP addresses for the service. In this case spf.mtasv.net contains the SPF entry for Postmark and _spf.creatsend.com represents Campaign Monitor’s SPF entry. To double-check that everything works as it should, you can look at these using the dig command in your terminal. Just type dig txt spf.mtasv.net and you’ll see the Postmark SPF record and the specified IP addresses.
* **~all** This specifies that everything else should be a “Soft” fail. That means that the message should be accepted but tagged as a soft fail, and the receiving server can use that as an additional factor in scoring the message’s likeliness of being spam. You could replace the ~ with a **-** and that would indicate that the message should be rejected. **However, this is more aggressive and is known to create more issues than it solves**.

## Full SPF Record Syntax
### Mechanisms
Mechanisms can be used to describe the set of hosts which are designated outbound mailers for the domain and can be prefixed with one of four qualifiers:
* **+** (Pass)
* **-** (Fail)
* **~** (SoftFail)
* **?** (Neutral)

If a mechanism results in a hit, its qualifier value is used.  The default qualifier is “+“, i.e. “Pass”. Mechanisms are evaluated in order. If no mechanism or modifier matches, **the default result is “Neutral”**.

If a domain has no SPF record at all, the result is **“None”**. If a domain has a temporary error during DNS processing, you get the result **“TempError”** (called “error” in earlier drafts). If a syntax or evaluation error occurs (eg. the domain specifies an unrecognized mechanism) the result is **“PermError”** (formerly “unknown”).

Evaluation of an SPF record can return any of these results:

![spf_results](/assets/img/posts/spf_results.png)

#### The "all" mechanism
This mechanism always matches. It should always go at the end of the SPF record.

Examples:
```
“v=spf1 mx ~all”
Allow domain’s MXs to send mail for the domain, softfail all others.

“v=spf1 ~all”
The domain sends no mail at all.

“v=spf1 +all”
The domain allows all IP address on the internet to send mail.  Though ‘valid’, this is not recommended.
```

#### The "ip4" mechanism
Syntax:
```
ip4:<ip4-address>
ip4:<ip4-network>/<prefix-length>
```
The argument to the “ip4:” mechanism is an IPv4 network range. If no prefix-length is given, /32 is assumed.

Examples:
```
“v=spf1 ip4:192.168.0.1/16 ~all”
Allow any IP address between 192.168.0.1 and 192.168.255.255.
```

#### The "a" mechanism
Syntax:
```
a
a/<prefix-length>
a:<domain>
a:<domain>/<prefix-length>
```
All the A records for domain are tested. If the client IP is found among them, this mechanism matches. If the connection is made over IPv6, then an AAAA lookup is performed instead.

If domain is not specified, the current domain is used.

The A records have to match the client IP exactly, unless a prefix-length is provided, in which case each IP address returned by the A lookup will be expanded to its corresponding CIDR prefix, and the client IP will be sought within that subnet.

Examples:
```
Examples:

“v=spf1 a ~all”
The current domain is used.

“v=spf1 a:example.com ~all”
Equivalent if the current domain is example.com.

“v=spf1 a:mailers.example.com ~all”
Perhaps example.com has chosen to explicitly list all the outbound mailers in a special A record under mailers.example.com.

“v=spf1 a/24 a:offsite.example.com/24 ~all”
If example.com resolves to 192.0.2.1, the entire class C of 192.0.2.0/24 would be searched for the client IP. Similarly for offsite.example.com. If more than one A record were returned, each one would be expanded to a CIDR subnet.
```

#### The "mx" mechanism
Syntax:
```
mx
mx/<prefix-length>
mx:<domain>
mx:<domain>/<prefix-length>
```

All the A records for all the MX records for domain are tested in order of MX priority. If the client IP is found among them, this mechanism matches.

If domain is not specified, the current domain is used.

Examples:
```
“v=spf1 mx mx:deferrals.domain.com ~all”
Perhaps a domain sends mail through its MX servers plus another set of servers whose job is to retry mail for deferring domains.

“v=spf1 mx/24 mx:offsite.domain.com/24 ~all”
Perhaps a domain’s MX servers receive mail on one IP address, but send mail on a different but nearby IP address.
```

#### The "include" mechanism
Syntax:
```
include:<domain>
```
The specified domain is searched for a match. If the lookup does not return a match or an error, processing proceeds to the next directive.

Examples:
```
In the following example, the client IP is 1.2.3.4 and the current domain is example.com.

“v=spf1 include:example.com ~all”

If example.com has no SPF record, the result is PermError.
Suppose example.com’s SPF record were “v=spf1 a ~all”.
Look up the A record for example.com. If it matches 1.2.3.4, return Pass.
If there is no match, other than the included domain’s “~all”, the include as a whole fails to match; the eventual result is still Fail from the outer directive set in this example
```

#### Too many lookups?
You should not run in to the 10 lookup maximum.

# DKIM
## What is DKIM
DKIM (DomainKeys Identified Mail) is a security standard for emails, and it is designed to ensure that emails do not get altered during the transit phase from the sending server to the recipient server.

When it leaves the server that is sending it, it is signed with a private key using public key cryptography. The public key is published on the domain’s DNS, and it is used by the recipient servers to authenticate the message. It ensures that there is no alteration in the message’s body. The message passes DKIM. The recipient server verifies the hash made with the private key using the published public key. Only then it becomes authentic.

The specification allows signers to choose which header fields they sign, but the **From:** field must always be signed.

## How does DKIM work?
Typically, DKIM uses two actions to verify the message. Firstly it acts on the sending server that sends signed emails. Secondly, on the recipient server where it checks the signatures of the received emails. This process involves the pairing of private and public keys. The private key is never exposed and is kept safe on your server. The public key is included on your domains DNS records where it is revealed to the world for purposes of verifying messages. Now let us understand how this protocol works on the sending and receiving servers.

![dkim](/assets/img/posts/DKIM.png)

## Sending a DKIM signed message
Signing modules insert one or more **DKIM-Signature:** header fields, possibly on behalf of the author organization or the originating service provider. The specification allows signers to choose which header fields they sign, but the **From:** field must always be signed. The resulting header field consists of a list of **tag=value** parts as in the example below:
```
DKIM-Signature: v=1; a=rsa-sha256; d=example.net; s=brisbane;
     c=relaxed/simple; q=dns/txt; t=1117574938; x=1118006938;
     h=from:to:subject:date:keywords:keywords;
     bh=MTIzNDU2Nzg5MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTI=;
     b=dzdVyOfAKCdLXdJOc9G2q8LoXSlEniSbav+yuU4zGeeruD00lszZVoG4ZHRNiYzR
```
Where the tags used are:
* **v**, version
* **a**, signing algorithm
* **d**, domain
* **s**, selector
* **c**, canonicalization algorithm(s) for header and body
* **q**, default query method
* **t**, signature timestamp
* **x**, expire time
* **h**, header fields - list of those that have been signed
* **bh**, body hash
* **b**, signature of headers and body

The most relevant ones are **b** for the actual digital signature of the contents (headers and body) of the mail message, **bh** for the body hash, **d** for the signing domain, and **s** for the selector.

Both header and body contribute to the signature. First, the message body is hashed, always from the beginning, possibly truncated at a given length (which may be zero). Second, selected header fields are hashed, in the order given by h. Repeated field names are matched from the bottom of the header upward, which is the order in which Received: fields are inserted in the header. A non-existing field matches the empty string, so that adding a field with that name will break the signature.

## Verifying a Message
A receiving SMTP server wanting to verify uses the domain name and the selector to perform a DNS lookup. For example, given the example signature above: the d tag gives the author domain to be verified against, **example.net** ; the s tag the selector, **brisbane**. The string **_domainkey** is a fixed part of the specification. This gives the TXT resource record to be looked up as:
```
brisbane._domainkey.example.net
```
Note that the selector and the domain name can be UTF-8 in internationalized emails. In that case the label must be encoded according to IDNA before lookup. The data returned from the query of this record is also a list of tag-value pairs. It includes the domain's public key, along with other key usage tokens and flags; as in this example:
```
"k=rsa; t=s; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDDmzRmJRQxLEuyYiyMg4suA2Sy
MwR5MGHpP9diNT1hRiwUd/mZp1ro7kIDTKS8ttkI6z6eTRW9e9dDOxzSxNuXmume60Cjbu08gOyhPG3
GfWdg7QkdN6kR4V75MFlw624VY35DaXBvnlTJTgRg/EW72O1DiYVThkyCgpSYS8nmEQIDAQAB"
```

The receiver can use the public key (value of the **p** tag) to then validate the signature on the hash value in the header field, and check it against the hash value for the mail message (headers and body) that was received. If the two values match, this cryptographically proves that the mail was signed by the indicated domain and has not been tampered with in transit.

Signature verification failure does **NOT** force rejection of the message. Instead, the precise reasons why the authenticity of the message could not be proven should be made available to downstream and upstream processes. Methods for doing so may include sending back an [FBL message](https://en.wikipedia.org/wiki/Feedback_loop_(email)), or adding an **Authentication-Results header** field to the message as described in [RFC 7001](https://tools.ietf.org/html/rfc7001).

# DMARC
## What is DMARC
DMARC (Domain-based Message Authentication, Reporting & Conformance) is a standard that prevents spammers from using your domain to send email without your permission — also known as spoofing. Spammers can forge the “From” address on messages so the spam appears to come from a user in your domain. A good example of this is PayPal spoofing, where a spammer sends a fraudulent email to you pretending to be PayPal in an effort to obtain your account information. DMARC ensures these fraudulent emails get blocked before you even see them in your inbox. In addition, DMARC gives you great visibility and reports into who is sending email on behalf of your domain, ensuring only legitimate email is received.

## How does DMARC work?
DMARC is built on top of DKIM and SPF. With only SPF and DKIM, it is up to the receiving server to decide what to do with the results. DMARC takes it a step further and gives you full control to set a policy to reject or quarantine emails from sources you do not know or trust, all based on the results of DKIM and SPF. For instance, since PayPal is a huge target for email fraud, they publish a DMARC record that says if DKIM or SPF fails, reject the message. Participating ISPs will look at this policy and discard the emails that fail.

![DMARC](/assets/img/posts/DMARC.png)

### DMARC alignment with DKIM
In the above diagrammatic flow, the left side shows how DMARC uses DKIM i.e. how DMARC treats a mail compliant or fraudulent based on the results of DKIM and if it passes DKIM, it will further validate the alignment. When an email is sent by a partner on behalf of a domain that has a DMARC policy enabled, the receiving mail server that has DMARC capability does the following:

1. Extracts DKIM signature from mail headers.
2. Checks if DKIM pass. If DKIM result is “pass”, only then it will further validate DMARC alignment. If the DKIM result is “fail” or is other than “pass”, DMARC alignment with DKIM will fail for that mail.
3. If the DKIM result is “pass”, DMARC further performs an alignment using the selector domain (d=com) and matches with the from domain (from:mydomain.com) in the mail header. Since both match, DMARC result is pass for this mail as it is aligned with DKIM.
4. If DMARC alignment with DKIM fail, it will check for alignment with SPF. SPF alignment is explained next.

**Note**: It doesn’t matter if DKIM does not pass if DMARC alignment is achieved with SPF.

### DMARC alignment with SPF
When an email is sent by a partner on behalf of a domain that has a DMARC policy enabled, the receiving mail server that has DMARC capability does the following:

1. Extract SPF information from mail headers.
2. Checks if SPF pass. If SPF result is “pass”, only then it will further validate DMARC alignment. If the SPF result is “fail” or is other than “pass”, DMARC alignment with SPF will fail for that mail.
3. If SPF result is “pass”, DMARC further performs an alignment using return-path domain (mailfrom:test.user@com) and matches with the from domain (from:mydomain.com) in the mail header. Since both match, DMARC result is pass for this mail as it is aligned with SPF.
4. If DMARC alignment with SPF fail, it will check for alignment with DKIM. DKIM alignment process explained above.

**Note**: It doesn’t matter if SPF does not pass, if DMARC alignment is achieved with DKIM.



## Setup DMARC Record
Similar to SPF and DKIM, this policy resides in DNS. A typical DMARC record in DNS will look like this:
```
"v=DMARC1;p=reject;pct=100;rua=mailto:postmaster@dmarcdomain.com"
```
In this scenario, the sender defines the policy as such that the receiver outright rejects all non-aligned messages and sends a report about the rejections to a specific email address. If the sender were to use the “quarantine" setting in the policy, it would look like:
```
"v=DMARC1;p=quarantine;pct=100;rua=mailto:postmaster@dmarcdomain.com"
```
and would request the action to quarantine on the receiving end of the message. In the next example, if a message claims to be from your domain.com and fails DMARC, no action is taken. Instead, an aggregate report will be sent to **postmaster@your_domain.com**.
```
"v=DMARC1; p=none; rua=mailto:postmaster@your_domain.com"
```