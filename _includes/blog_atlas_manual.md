# RIPE Atlas
Reference:
[Atlas About](https://atlas.ripe.net/about/)
[User-Defined Measurements](https://atlas.ripe.net/docs/udm/)
[API Manual v2](https://atlas.ripe.net/docs/api/v2/manual/)

## What is RIPE Atlas?
RIPE Atlas is a global network of **probes that measure Internet connectivity and reachability**.


There are thousands of active probes in the RIPE Atlas network, and it is continually growing. The RIPE NCC collects data from this network and provides Internet maps, data tools and visualisations based on the aggregated results. RIPE Atlas **users who host a probe can also use the entire RIPE Atlas network to conduct customised measurements** that provide valuable data about their own network.


## What Can I Do With RIPE Atlas?
* monitor network reachability from thousands of vantage points around the globe
* **Check the responsiveness of DNS infrastructure**, such as root name servers.

## How RIPE Collect Data
RIPE Atlas **probes** are small, USB-powered hardware devices that hosts attach to an Ethernet port on their router via a network (UTP) cable.

## Definition in RIPE Atlas Community
The RIPE Atlas community is made up of **users**, **hosts**, **sponsors** and **ambassadors**.

Anyone who accesses RIPE Atlas maps and statistics, which are open and available to the public, is considered a RIPE Atlas **user**.

A **host** is anyone who connects a probe or anchor to their own network. Hosts can conduct their own customised measurements in order to gain valuable information about their network using other RIPE Atlas probes.

A **sponsor** is an individual or organisation that financially supports RIPE Atlas.

An **ambassador** is someone who helps us distribute RIPE Atlas probes.

RIPE NCC members can also take advantage of special RIPE Atlas features, even if they do not host a probe.

## Vision
With RIPE Atlas, the RIPE NCC hopes to create the world's largest Internet measurement network.

## Creating Your Own Measurements
The creation process consists of three steps: **Definitions**, **Probe selection** and **Timing**. In the definitions section, you can select the types of measurements you would like to schedule, with the UI showing some configurable parameters, such as the target.


An **extra panel** at the bottom of the page shows you the real-time, API-compatible specification, which can be used to **learn how to create measurements directly through the API**.


### Measurement Types
The available user-defined measurement (UDM) types are "Ping", "Traceroute", "DNS" and "SSL". "HTTP" measurements are also technically possible but are restricted to researchers and other interested users on a case-by-case basis. Following is a list of measurement types and related parameters.

#### Ping
* Address Family - Select whether you want to use IPv4 or IPv6.
* Packets - The number of packets that should be sent in each ping.
* Size - The size of the packets that are sent.
* Description - Allows you to give the measurement a description to help you identify it.
* Interval - The number of seconds each probe participating in the measurement will wait before attempting to perform the measurement again. A drop-down list will suggest some common options.
* Spread – This distributes the start time of the probes’ measurements throughout the interval so that they are more evenly spaced. If spread is not specified, it will default to half of the interval, with a maximum of 400 seconds.

#### DNS
* Address Family - Select whether you want to use IPv4 or IPv6.
* Query Class - Specify if the query will be performed in the IN or CHAOS domain space.
* Query Type - The type of DNS query to be performed.
* Query Argument - The domain name (or IP address, in the case of reverse DNS) to look up.
* Description - Allows you to give the measurement a description to help you identify it.
* Interval - The number of seconds each probe participating in the measurement will wait before attempting to perform the measurement again.
* Spread – This distributes the start time of the probes’ measurements throughout the interval so that they are more evenly spaced. If spread is not specified, it will default to half of the interval, with a maximum of 400 seconds.
* Set DNSSEC OK Flag - Enable DNSSEC DO bit (RFC-3225). Default is off
* Recursion desired - Enable recursion. This is the RD flag described in RFC1035.
* Use Probe's Resolver - Ese the probe's list of local resolvers instead of specifying a target to use as the resolver.
* Protocol - The protocol to use.
* Retry Times - Number of attempts.
* UDP Payload Size - The maximum reply size accepted. This field is described in RFC2671.
* Include the Qbuf - Include a base64 encoded version of the queries made by the probe.
* Exclude the Abuf - Exclude the full base64 encoded answer.
* Prepend the Probe's ID - Each probe prepends its probe number and a timestamp to the DNS query argument to make it unique
* Use NSID - Include DNS nameserver identifier option (RFC5001)

#### Spread
Spread can be set under “Advanced Options” in Step 1 when creating a new measurement using the web interface, and is also available via the API. Spread creates a distribution of individual measurements throughout the measurement interval, rather than clustering the measurements as closely together as possible. In this way, spread helps to avoid overloading targets with bursts of measurements and creates a more steady flow of packets. 


For example, if you use 60 probes for a measurement that is scheduled to take place every 240 seconds without any spread, all 60 probes would perform their measurement at nearly the same time, every 240 seconds. With a spread of 240 seconds, the probes will be assigned random start times throughout each interval. In this example, this would average out to one measurement every four seconds. The fewer the number of probes, the less uniform the distribution will be throughout the interval, since the probes are distributed randomly. However, the probes will stick to the specified frequency, so their distribution will remain the same from one interval to the next.


### Probe Selection
The second step of the wizard helps select the probes that will participate in the UDM. By default, 50 probes worldwide are randomly selected, allowing you to skip this step if you like.

You can specify a set of probes already used in a previously scheduled measurement or a new set of probes, either manually or through the probe selection wizard. The selections are displayed in an editable list.

### Timing
The third screen of the UDM wizard allows you to select the measurement's start and stop times. The fields are Start and End time. You can also specify whether it is a One-off measurement.

#### One-off
One-off measurements, as the name suggests, execute only once. What makes them really attractive is the fact that they are near real time; results are delivered a few seconds (generally 8-10) after specifying the measurement. This makes one-offs an ideal tool for looking into network problems that are perceived "now". If this option is selected, you can specify only the Start time.

#### Start
For the measurement start time, you can choose between 'as soon as possible' or a specific date and time (in UTC).

#### End
The measurement's end time can be 'never' (which means it won't stop until you manually stop it) or a specific date and time (in UTC).

Once your measurement is submitted and accepted, you can immediately see it in your measurement list, and can check its results.

### Measurement Statuses
Each measurement has a status, defined as one of these:
* Specified: The measurement has been defined and sent to our infrastructure to be relayed to the probes.
* Scheduled: The measurement has been relayed to the probes. If the start time is immediate, this status doesn't last very long.
* Ongoing: The measurement is running on available probes.
* Stopped: The measurement was stopped either on schedule, by the user requesting an early stop.
* Forced to Stop: The measurement was killed prematurely due to a lack of available credits.
* No suitable probes: The measurement cannot currently be executed as defined due to a lack of available probes. This may be because you asked to use probes that don't exist (for example, probes in an AS in which there are no probes) or because all of the probes you requested were too busy to take on new jobs. This latter scenario is very rare however.
* Failed: If a probe never actually runs a single measurement over the duration of the specified start/stop time (typically due to a lack of available probes), it will be marked as Failed once the stop time has been reached.


In other words, Specified, Scheduled, Ongoing, and No suitable probes are statuses potentially assigned to running measurements (or at least those who may return results at some point), while Stopped, Forced to Stop, and Failed are assigned to measurements that will not be returning any more results.

### Rate Limits
The following rate limits apply for users/measurements:
* No more than 100 simultaneous measurements
* No more than 1000 probes may be used per measurement
* No more than 100,000 results can be generated per day
* No more than 50 measurement results per second per measurement. This is calculated as the spread divided by the number of probes.
* No more than 1,000,000 credits may be used each day
* No more than 25 ongoing and 25 one-off measurements of the same type running against the same target at any time

### [Cost](https://atlas.ripe.net/docs/credits/)
The cost for the individual result depends on what type of measurement you conduct, and what options and flags were specified. The following sections describe the current settings. One-off measurement result is twice as expensive.

#### Ping and ping6
Unit cost = N * (int(S/1500) + 1)

#### DNS and DNS6
Unit cost for UDP: 10 credits/result
Unit cost for TCP: 20 credits/result

### Querying Results For DNS
An example result of a DNS Lookup: 
```json
{
	"af":6,
	"dst_addr":"2001:7fd::1",
	"from":"2001:67c:2e8:ffe2:220:4aff:fec6:cc9d",
	"fw":4790,
	"lts":38,
	"msm_id":11001,
	"msm_name":"Tdig",
	"prb_id":9,
	"proto":"UDP",
	"result":
	{
		"ANCOUNT":1,
		"ARCOUNT":8,
		"ID":20790,
		"NSCOUNT":13,
		"QDCOUNT":1,
		"abuf":"UTaEAAABAAEADQAIAAAGAAEAAAYAAQABUYAAQAFhDHJvb3Qtc2VydmVycwNuZXQABW5zdGxkDHZlcmlzaWduLWdycwNjb20AeDo7WQAABwgAAAOEAAk6gAABUYAAAAIAAQAH6QAAAsAcAAACAAEAB+kAAAQBYsAeAAACAAEAB+kAAAQBY8AeAAACAAEAB+kAAAQBZMAeAAACAAEAB+kAAAQBZcAeAAACAAEAB+kAAAQBZsAeAAACAAEAB+kAAAQBZ8AeAAACAAEAB+kAAAQBaMAeAAACAAEAB+kAAAQBacAeAAACAAEAB+kAAAQBasAeAAACAAEAB+kAAAQBa8AeAAACAAEAB+kAAAQBbMAeAAACAAEAB+kAAAQBbcAewBwAHAABAAfpAAAQIAEFA7o+AAAAAAAAAAIAMMB0ABwAAQAH6QAAECABBQACAAAAAAAAAAAAAAvAgwAcAAEAB+kAABAgAQUAAAIAAAAAAAAAAAAMwJIAHAABAAfpAAAQIAEFAAAtAAAAAAAAAAAADcChABwAAQAH6QAAECABBQAAqAAAAAAAAAAAAA7AsAAcAAEAB+kAABAgAQUAAC8AAAAAAAAAAAAPwL8AHAABAAfpAAAQIAEFAAASAAAAAAAAAAANDcDOABwAAQAH6QAAECABBQAAAQAAAAAAAAAAAFM=",
		"answers":
		[ { 
			"MNAME":"a.root-servers.net.",
			"NAME":".",
			"RNAME":"nstld.verisign-grs.com.",
			"SERIAL":2017082201,
			"TTL":86400,
			"TYPE":"SOA"
		  }
		],
		"rt":6.715,
		"size":509
	},
	"src_addr":"2001:67c:2e8:ffe2:220:4aff:fec6:cc9d",
	"timestamp":1503447622,
	"type":"dns"
}
```

#### [Data Structure Documentation](https://atlas.ripe.net/docs/data_struct/#v4750_dns)
* "af" -- [optional] IP version: "4" or "6" (int)
* "bundle" -- [optional] instance ID for a collection of related measurement results (int)
* "dst_addr" -- [optional] IP address of the destination (string)
* "dst_name" -- [optional] hostname of the destination (string)
* "error" -- [optional] error message (object with the following fields:)
	* "timeout" -- query timeout (int)
	* "getaddrinfo" -- error message (string)
* "from" -- [optional] IP address of the source (string)
* "group_id" -- [optional] If the measurement belongs to a group of measurements, the identifier of the group (int)
* "lts" -- last time synchronised. How long ago (in seconds) the clock of the probe was found to be in sync with that of a controller. The value -1 is used to indicate that the probe does not know whether it is in sync (int)
* "msm_id" -- measurement identifier (int)
* "msm_name" -- measurement type "Tdig" (string)
* "prb_id" -- source probe ID (int)
* "proto" -- "TCP" or "UDP" (string)
* "qbuf" -- [optional] query payload buffer which was sent to the server, base64 encoded (string)
* "result" -- [optional] response from the DNS server (object with the following fields:)
	* "ANCOUNT" -- answer count, RFC 1035 4.1.1 (int)
	* "ARCOUNT" -- additional record count, RFC 1035, 4.1.1 (int)
	* "ID" -- query ID, RFC 1035 4.1.1 (int)
	* "NSCOUNT" -- name server count (int)
	* "QDCOUNT" -- number of queries (int)
	* "abuf" -- answer payload buffer from the server, base64 encoded (string)
	* "answers" -- first two records from the response decoded by the probe, if they are TXT or SOA; other 	RR can * be decoded from "abuf" (array of objects)
		objects have the following fields:
		* "MNAME" -- domain name, RFC 1035, 3.1.13 (string)
		* "NAME" -- domain name. (string)
		* "RDATA" -- [type TXT] txt value (list of strings)
		* "RNAME" -- [if type SOA] mailbox, RFC 1035 3.3.13 (string)
		* "SERIAL" -- [type SOA] zone serial number, RFC 1035 3.3.13 (int)
		* "TTL" -- [type SOA] time to live, RFC 1035 4.1.3 (int)
		* "TYPE" -- RR "SOA" or "TXT" (string), RFC 1035
		* "rt" -- [optional] response time in milli seconds (float)
		* "size" -- [optional] response size (int)
* resultset - [optional] an array of objects containing all the fields of a DNS result object, except for the fields: fw, from, msm_id, prb_id, and type. Available for queries sent to each local resolver.
* "retry" -- [optional] retry count (int)
* "src_addr" -- [optional] the source IP address added by the probe (string).
* "subid" -- [optional] sequence number of this result within a group of results, available if the resolution is done by the probe's local resolver (int)
* "submax" -- [optional] total number of results within a group (int)
* "timestamp" -- start time, in Unix timestamp (int)
* "type" -- "dns" (string)

##### Notes
* If a hostname was provided, both the hostname (dst_name) and the IP address (dst_addr) will be in the response.
* If an IP address was provided, the name field will not be filled, unless the address which was given differs from the probe's resolution of the address. For instance, if "2001:0DB8:0:0::1" was provided, and the probe resolves it to "2001:0DB8::1", the first value will be returned as the name and the second as the address.
* The dst_addr field will always be present, except when there is an error.

#### [Parsing "qbuf"/"abuf"](https://atlas.ripe.net/docs/code/#decoding_dns_abuf)
```python
>>> import base64
>>> import dns.message
>>> dnsmsg = dns.message.from_wire(base64.b64decode('f2+AgAABAAEAAAAAA3d3dwRyaXBlA25ldAAAAQABwAwAAQABAAA3+gAEwQAGiw=='))
>>> print dnsmsg
id 32623
opcode QUERY
rcode NOERROR
flags QR RA
;QUESTION
www.ripe.net. IN A
;ANSWER
www.ripe.net. 14330 IN A 193.0.6.139
;AUTHORITY
;ADDITIONAL
```

## [Introduction To API](https://atlas.ripe.net/docs/api/v2/manual/overview/basic.html)
Before doing anything, [signing up for an API key](https://atlas.ripe.net/keys/#).

### A Basic Request
#### The Base URL
The base URL of all calls to the RIPE Atlas APIs for this particular version is *https://atlas.ripe.net/api/v2/*. You can append the name of the object type you want information from to form a URL. For example: *https://atlas.ripe.net/api/v2/measurements/*

A URL that contains information about one object or a series of objects is called a **Resource**.


Note that the API is only served over TLS.


The object part of the URL should be plural and lower case. For example: *measurements* and *probes*.

#### Example
```
https://atlas.ripe.net/api/v2/measurements/?page_size=3
```

### Two Types of Resources
A RIPE Atlas API can either return a list of objects or a single object in a response.

We will refer to the first type as an Object List Resource. A URL that points to an Object List Resource generally has the form: *https://atlas.ripe.net/api/v2/< OBJECTNAME_PLURAL >/*

An API resource that returns a single object will be called an Object Detail Resource. An Object Detail Resource URL usually has the form: *https://atlas.ripe.net/api/v2/< OBJECTNAME_PLURAL >/< OBJECT_ID >*

#### Object List Resources
An Object List Resource will return one JSON object. This JSON object holds the fields count, next, previous and *results*. This latter field is an array of JSON objects. Its URL will end with the name of the object type. For example: *https://atlas.ripe.net/api/v2/measurements/*

The *results* field will hold an array of objects. Each object contains a field *id *and a field *type* that, together, are unique identifiers across all the RIPE Atlas APIs.

You can add query parameters to the URI to filter object properties. You can also use query parameters to add additional fields to the objects, or remove fields from the objects. An example of a filter query parameter is *is_oneoff=true*, while an example of a query parameter that would reduce the fields is *fields=id,country_code=gr*.

A Resource that holds a request list will always be paginated. This means that the response to a single request will be broken down into separate responses if the number of objects requested exceeds 500. The details of this pagination are all in the *count*, *next* and *previous* fields of the response. The response has a field count that lists the total number of objects found in RIPE Atlas. This *count* field will never have a value higher than **20,000**.

#### Example
```
https://atlas.ripe.net/api/v2/probes/?page=10&sort=id
```

### Object details
An Object Details Resource returns exactly one JSON object.


Like the List Resource, the object returned from the metadata API has a type and a id field that, together, are unique identifiers across RIPE Atlas.
#### Example
```
https://atlas.ripe.net/api/v2/probes/143/
```

### Generic Query Parameters
Every request to the RIPE Atlas API can be accompanied by one or more query parameters. This was already shown in the basic request featured. Some of the available query parameters are specific to a request, most notably query parameters that are used as filters on Object List Resources. For example:
```
https://atlas.ripe.net/api/v2/measurements/?status_name=Connected
```

#### Unknown Query Parameters
The RIPE Atlas APIs will silently ignore any unknown query parameters. This means that using an invalid query parameter name will return a "regular" response.

### The *fields* and *optional_fields* Query Parameters
Every request to the RIPE Atlas APIs will always return the fields that constitute the unique identifier for that particular object. Generally that will be *id* and *type*. Some optional fields can also be added to the request. Every other field can be explicitly removed.

The query parameters that allow this are the *fields* and the *optional_fields* parameters.

#### fields
Let's start with an example: *https://atlas.ripe.net/api/v2/probes/143/?fields=first_connected,status_since* will return only the fields first_connected and status_since of the probe with ID 143.

#### optional_fields
Using the *optional_fields* query parameter with a comma-separated list of field names means these fields will be appended to every requested object, next to the fields that are already there by default.

For example, the request *https://atlas.ripe.net/api/v2/measurements/2000000/* will not, by default, return the fields *participation_requests* and *probes*, mainly because these fields require extra calls to the back-end database and contain information that is not obviously needed. If you add *optional_fields=participation_requests*,probes as a query parameter, however, you will see these two extra fields:
```
https://atlas.ripe.net/api/v2/measurements/2000000/?optional_fields=participation_requests,probes
```


### The *page* and *page_size* Query Parameters
The *page* and *page_size* query parameters are used in conjunction most of the time on requests that return object lists. The query parameter *page_size* allows you to set the number of objects returned in one page, while page allows you to specify the page number you want to view, based on a specified number or the default *page_size* of 100.


The APIs use these query parameters themselves to construct links to the next and previous page of any list of objects. For example, if you request the URL:
```
https://atlas.ripe.net/api/v2/measurements/
```

You'll see that the API returns a next field that contains a link with the query parameters ?page=2.


There are some important things to note when using these query parameters:
* Values over 500 on the query parameter page_size will be automatically limited to 500.
* The product of the values page_size and page cannot be more than 20,000. A request higher than this limit will result in an HTTP error. See this page for information on how to overcome this limit.
* Using the page and page_size parameters is meant to limit the size of the response as a convenience for users. Using many requests with small limits will not speed up (or slow down), the RIPE Atlas APIs. Using many small requests will, however, require more overhead on you network connection in comparison to one big request to RIPE Atlas.


### More Query Parameters
#### mine=true
If you are logged in to RIPE Atlas, you can see objects that you own by passing the query parameter *mine=true* for measurement objects.

#### sort
This query parameter will order the results by the field name that is given as the value, e.g. *sort=-id*.

#### key
The key parameter allows you to authenticate and authorise yourself to the RIPE Atlas APIs. The actual key can be generated by you if you have a RIPE Atlas account, or if somebody gave you one.


### Authentication
As mentioned in the overview, there are two methods to authenticate to RIPE Atlas and you can use most of our APIs unauthenticated (i.e. as an anonymous user).


The two authentication methods are:
* Use an API key. This is the preferred way of authentication to the APIs.
* Use a session-based cookie. This is primarily used for client-side JavaScript in web browsers.

### API Keys
The preferred and most convenient method to authenticate to RIPE Atlas is to use API keys. 

The user who created the API key is referred to by RIPE Atlas as the creator of the key. Key permissions can never be greater than those of the creator. This also means that key permissions can be lowered when the creator's permissions are lowered.

API keys can be used used in the RIPE Atlas APIs in one of two ways:

by passing them in as query parameters in a web request:
```
https://atlas.ripe.net/api/v2/measurements/?key=1233-3434-4556-565
```

by including the key in the Authorization header, e.g. with curl:
```bash
curl -H "Authorization: Key 1233-3434-4556-565" "https://atlas.ripe.net/api/v2/measurements/"
```

#### Notes Regarding 403
A 403 Forbidden error will response will be returned if:
* the API key does not provide the required permission;
* the API key does not exist;
* the API key is either not enabled or is outside of the defined valid from to valid to time range;
multiple API keys are presented using either multiple "key=" parameters, or a combination of "key=" parameters and the "Authorization" header.

### RIPE Atlas Result Streams
RIPE Atlas has been providing downloadable results since the very beginning of the project. This works well if you know what time frame you're interested in, and want to get the data collected during that period.


The streaming data service allows you to tap into the real-time data flow of all the collected public results. Every time our system receives a data point or a probe connectivity event occurs, it's also delivered to the clients who are "tuned in" to that result stream. This feature is implemented using [WebSockets](https://en.wikipedia.org/wiki/WebSocket).


#### Highlights
* This service is in prototype status. We're observing how our system reacts to the streams provided to users in order to evaluate the feasibility and usefulness of a production service.
* Streaming uses the Socket.IO protocol over WebSockets for real-time event-based communication.
* You can only subscribe ("tune in") to results delivered by public measurements.
* You can subscribe to the connectivity events of any probe.
* We're inviting the community to check out the gallery of visualisations provided by the RIPE Atlas team and our users as soon as it becomes available, and to come up with new visualisations and/or to enhance existing ones

### [More Info On Streaming](https://atlas.ripe.net/docs/api/v2/manual/measurements/result-streaming.html)
More information on streaming can be found here

## [Using APIs For Measurement](https://atlas.ripe.net/docs/api/v2/manual/object_documentation.html)
A *Measurement* in the context of the RIPE Atlas APIs is an object holding information about one measurement (called a "one-off" measurement) or a series of measurements performed by one or more probes on the RIPE Atlas network. The response of a single measurement performed by a single probe is called a *measurement result*.


If we look at the information returned in more detail we can discern:
* Measurement metadata: the ID and the measurement type and information on the selected probes, including whether this is a public measurement.
* Attributes of the measurement specification, i.e. the attributes that a probe needs to start performing measurements.
* Status information of the measurement. E.g. Is it running? When was it started?, etc.

### Type of Information
#### Metadata
A *Measurement* object holds information about the measurement itself. First it holds the fields *id* and the *type* that, together, provide a unique identifier for the measurement object. Examples of measurement types are *HTTP* and Traceroute.

The *participation requests* array holds a list of *participation request* objects, which describe the probe selection criteria that were made for this measurement. You can read more about the particapation request object [here](https://atlas.ripe.net/docs/api/v2/manual/participation_requests.html).

Please note that this is not the same as the actual probes that are appointed to a measurement by RIPE Atlas. The latter would be part of the status information of the measurement in a object called *probes*.

Finally, there are some fields that describe some of the measurement's general properties. They are *is_public* and *result*, a URL that points to the result of the measurements as performed by the selected probes.

#### The Measurement Specification
The *Measurement* object holds the measurement's settings, called the "measurement specification" in RIPE Atlas terminology. This specification is a mixture of base attributes, available for all types of measurements and type-specific attributes. Examples of such attributes are *packets*, *method* or *interval*.


The measurement specification is the object that is sent to a specific probe to perform its measurement.


You can see an exhaustive list of all measurement specification attributes for all kinds of measurements [here](https://atlas.ripe.net/docs/api/v2/reference/#!/measurements/Measurement_List_GET).

#### Status Information
A *Measurement* object also holds the information about the time series of measurements to be performed by the probes, like the start and stop time of the measurements and the link to the streaming API resource that holds the result of the performed measurements.


A *Measurement* object also holds information about the status of the measurement, e.g. if it is currently running, stopped, etc.


Finally, the *Measurement* object holds information about the actual probes used in the measurement. This information is in an object called *probes*. **This is an optional field, which can be enabled by using the optional_fields=probes query parameter.**


### Measurement Query Parameters
Most of the generic query parameters apply to requests for measurement objects. You can use *page* and *page_size* to manipulate the number of objects and the pagination; you can use *fields* and *optional_fields* to set the fields you want in the response, and so on.


See [here](https://atlas.ripe.net/docs/api/v2/manual/overview/generic_query_parameters.html) for more information on these.


#### Field Filters
You can filter on most responses that are included in a typical response for measurement objects. Fields like id, target_name, etc. can be used directly as filters:
```
https://atlas.ripe.net/api/v2/measurements/?id=2000000
```

For some fields, it is also possible to enter a range filter, i.e. a filter that selects a range specified by a list, or by a lower and an upper limit.


These range filters have special syntax for their query parameters.


The **list range filter** uses the syntax *?< FIELDNAME >__in=item1,item2*. An example is:
```
https://atlas.ripe.net/api/v2/measurements/?id__in=2000000,2000001,2000002
```


The limit range filter uses the syntax appended with either *__gte*, *__gt*, *__lte* or *__lt*. As mentioned earlier on gt and gte refer to greater than and greater than or equal. You can combine multiple query parameters to set upper and lower limits:
```
https://atlas.ripe.net/api/v2/measurements/?id__gte=2000000&id__lte=2000010
```

#### API References
A full list of all available fields and query parameters is available [here](https://atlas.ripe.net/docs/api/v2/manual/measurements/queryparameters.html).

### Creating Measurements
Measurement creation is managed based on the premise of having to submit as little information as possible while still obtaining something useful. With that said, even the simplest measurement requires a rather large amount of information to initiate things. You will need to compose a measurement definition that has at least the *type*, *af*, *description* and *target* fields, as well as a *probes* object telling us which probes you want to use for the measurement.


If you omit the *start_time*, we assume you want it started right away; if you don't set the *is_oneoff* flag, we assume it must be a regular measurement, and so on.


You will also have to use a valid *key* with the right permissions.


If you do want to create measurements, you must use **a tool that is able to send POST requests** with a payload to RIPE Atlas. Three such tools are postman (a plug-in for Chrome), HttpRequester (a plugin for Firefox) and cURL (a command-line tool).


#### Structure
Our measurement REST API uses a JSON payload with a POST verb to create new measurements.


You can fill in one or more measurement *definitions*, one or more *probes* objects, and some fields that are applicable to all measurements you want to create.

#### The Response
The response to any successful POST request will return an object that has one field called *measurements*. This field holds an array that contains the IDs of the created measurements. The array reflects the order in which they were specified in the *definitions* list of you request.


### Simple Example
Let's begin with an example. Assume we want to create a simple ping measurement from *1* probes anywhere in the world to *ripe.net*. We also want it to be *one_off*.


Here's how to do this in python:
```python
# Reference: https://stackoverflow.com/questions/9746303/how-do-i-send-a-post-request-as-a-json/26876308#26876308

# importing the requests library 
import requests
import json 

# api-endpoint 
URL = "https://atlas.ripe.net/api/v2/measurements/?key=<your key>"

# defining a params dict for the parameters to be sent to the API 
body_in_json = {
    "definitions":[
        {
            "target": "ripe.net",
            "description": "My Third Measurement",
            "type": "ping",
            "af": 4,
            "is_oneoff": True
        }]
    ,
    "probes":
        [{
            "requested": 1,
            "type": "area",
            "value": "WW"
        }],
    "bill_to": "e7liu@eng.ucsd.edu",
}


# Define headers
headers = {'Content-type': 'application/json', 'Accept': 'application/json'}

# sending get request and saving the response as response object 
r = requests.post(URL, headers=headers, json=body_in_json)

# extracting data in json format 
data = r.json()

print(data)
```

If you have filled out everything correctly, you will get a response like this:
```json
{"measurements": [23021613]}
```

If you didn't use the right key, or no key at all, you will get an error:
```json
{"error":{"status":403,"code":104,"detail":"Authentication credentials were not provided.","title":"Forbidden"}}
```

If you made a mistake in the entry of the payload, you will get the following error:
```json
{"error":{"status":400,"code":104,"detail":"Invalid input. Please check the documentation.","title":"Bad Request"}}
```

### The *definition* Array
The *definitions* array holds single objects, which we will refer to as measurement definitions.


A measurement definition has a similar structure to a *measurement specification*. The *measurement specification*, as you might recall, is the collection of measurement settings that a probe needs to perform a measurement. There are some base attributes and some type-specific attributes in the specification.


A measurement definition is a sub-set of the measurement specification. There are some required fields, but most are optional. RIPE Atlas will fill out the missing fields with defaults for all non-required fields. Which fields are required differs per measurement type.


These are the required fields for all measurement types:
* description: An arbitrary string you will use to refer to this measurement.
* type: One of ping,traceroute,dns, sslcert or ntp
* af: The address family. It must be either 4 or 6.

The *target* field denotes the target of a measurement and is special because it is required for all measurement types except DNS measurements.

#### Note
You can use GUI to generate JSON files which could be used directly in replace of the *body_in_json* part.

### Base Attributes
A measurement definition contains a few base properties, and a myriad of type-specific options. For example, while all definitions have an *af* property, only traceroute measurements have a *paris* property.

![Base Properties](/assets/img/base_property.png)

### Type Specific Attributes
#### DNS
Aside from the base attributes that must be present in any request to create a measurement, there are specific attributes required for each measurement type.


We are mentioning only the required fields for every type. An exhaustive reference of all attributes that can be used are listed [here](https://atlas.ripe.net/docs/api/v2/reference).

![Base Properties](/assets/img/DNS_Attributes.png)

### A Full Example
```json
{
        "definitions": [
            {
                "target": "www.ripe.net",
                "description": "My Complex Measurement",
                "type": "traceroute",
                "af": 6,
                "resolve_on_probe": true,
                "is_public": true,
                "packets": 16,
                "protocol": "ICMP",
                "paris": 99,
                "firsthop": 30,
                "interval": 1800,
                "is_oneoff": false
            },
            {
                "target": "www.ripe.net",
                "description": "My Complex Measurement",
                "type": "ping",
                "af": 4,
                "resolve_on_probe": false,
                "is_public": false,
            }
        ],
        "probes": [
            {
                "requested": 10,
                "type": "area",
                "value": "WW"
            },
            {
                "requested": 5,
                "type": "country",
                "value": "GR"
            },
            {
                "requested": 5,
                "type": "country",
                "value": "CA"
            },
            {
                "requested": 3,
                "type": "probes",
                "value": [55,19,252]
            },
            {
                "requested": 1,
                "type": "udm",
                "value": 1000002
            }
        ],
        "start_time": 1461807395,
        "stop_time": "2018-01-01T12:00:00Z",
        "is_oneoff": true
}
```

### Stop Measurement
Measurements can be stopped by sending a *DELETE* verb to the URL of the measurement you want to stop. For example, if you want to stop the measurement with *msm_id*2034345, you will have to send a DELETE verb to: 
```
https://atlas.ripe.net/api/v2/measurements/2034305
```

You can only stop measurements that you've created yourself or that you have an API key for (and which has permissions to stop measurements). In the first case, you will have to be logged in with a session-based cookie.

### Latest Measurement Results API
The latest measurement results API can be found here:
```
https://atlas.ripe.net/api/v2/measurements/2000000/latest/?probe_ids=10008
```

An example code:
```python
# importing the requests library 
import requests
import json 

# api-endpoint 
URL = "https://atlas.ripe.net/api/v2/measurements/23020975/latest/?key=<YOUR KEY>"

# Define headers
headers = {'Content-type': 'application/json', 'Accept': 'application/json'}

# sending get request and saving the response as response object 
r = requests.get(URL, headers=headers)

# extracting data in json format 
data = r.json()

print(data)
```

#### Output Format
The output format is only slightly different from what you'd expect from the standard measurement result API call. Rather than simply dumping every result as a list, it returns a series of key/value pairs in the format:
```
{
  probe_id: [<result>],
  probe_id: [<result>],
  ...
}
```
This allows you to easily fetch the latest result from probe *123*, for example, by using:
```
my_data["123"][0]
```

#### Filtering
You can also do basic filtration by probe id simply by using *probe_ids=probe_id,probe_id* as query parameters.

Note, however, that if you specify a probe ID that is not part of the measurement, you'll simply get back an empty set.

### Status Check
If you just want to get started using status checks, you simply need to do the following:

1. Create a RIPE Atlas ping measurement using either the website or the API.
* You may use up to 1,024 probes.
	* Note the newly-created measurement ID
2. Go to: *https://atlas.ripe.net/api/v2/measurements/< measurement-id >* where *< measurement-id >* is the ID from your newly created measurement. If the measurement in question is not public, you'll need to include an API *?key=argument*.
	* A new version is available using the link: *https://atlas.ripe.net/api/v2/measurements/< measurement-id >/status-check*

3. Go to the URL again later to check whether anything has changed.

4. Define your alerts accordingly.

An exmaple code:
```python
import requests
import json 

# api-endpoint 
URL = "https://atlas.ripe.net/api/v2/measurements/23020975/"

# Define headers
headers = {'Content-type': 'application/json', 'Accept': 'application/json'}

# sending get request and saving the response as response object 
r = requests.get(URL, headers=headers)

# extracting data in json format 
data = r.json()

print(data)
```


