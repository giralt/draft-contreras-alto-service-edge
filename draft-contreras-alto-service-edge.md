---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Use of ALTO for Determining Service Edge"
category: info

docname: draft-contreras-alto-service-edge-latest
ipr: trust200902
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
area: AREA
workgroup: ALTO
keyword:
 - ALTO
 - compute
 - edge computing
venue:
  group: WG
  type: Working Group
  mail: alto@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/alto/
  github: giralt/draft-contreras-alto-service-edge
  latest: https://giralt.github.io/draft-contreras-alto-service-edge/#go.draft-contreras-alto-service-edge.html

author:
 -
    fullname: Luis M. Contreras
    organization: Telefonica
    email: luismiguel.conterasmurillo@telefonica.com

 -
    fullname: Sabine Randriamasy
    organization: Nokia Bell Labs
    email: sabine.randriamasy@nokia-bell-labs.com

 -
    fullname: Jordi Ros-Giralt
    organization: Qualcomm Europe, Inc.
    email: jros@qti.qualcomm.com

 -
    fullname: Danny Lachos
    organization: Benocs
    email: dlachos@benocs.com

 -
    fullname: Christian Rothenberg
    organization: Unicamp
    email: chesteve@dca.fee.unicamp.br



normative:
  RFC7285:
  RFC7752:
  RFC9275:
  RFC9240:
  I-D.ietf-teas-sf-aware-topo-model:


informative:

  CNTT:
    title : Cloud Infrastructure Telco Taskforce Reference Model
    seriesinfo : https://github.com/cntt-n/CNTT/wiki
    date : June 2022

  GSMA:
    title : Cloud Infrastructure Reference Model, Version 2.0
    seriesinfo : https://www.gsma.com/newsroom/wp-content/uploads//NG.126-v2.0-1.pdf
    date : October 2021

  Anuket:
    title : Anuket Project
    seriesinfo : https://wiki.anuket.io/
    date : October 2022
  LF-EDGE:
    title : Linux Foundation Edge
    seriesinfo : https://www.lfedge.org/
    date : Accessed March 2023

--- abstract

Service providers are starting to deploy computing capabilities
across the network for hosting applications such as AR/VR, vehicle
networks, IoT, and AI training, among others.
In these distributed computing
environments, knowledge about computing and communication resources
is necessary to determine the proper deployment location of
each application. This document proposes an initial approach
towards the use of ALTO to expose such information to the
applications and assist the selection of their deployment
locations.


--- middle

# Introduction

With the advent of network virtualization, operators
can make use of dynamic instantiation of network functions and
applications by using different techniques on top of
commoditized computation infrastructures, permitting
a flexible and on-demand deployment of services, aligned
with the actual needs as demanded by the users.

Operators are starting to deploy distributed computing
environments in different parts of the network with the
objective of addressing different service needs including
latency, bandwidth, processing capabilities, storage, etc.
This is translated in the emergence of a number of data
centers (both in the cloud and at the edge)
of different sizes (e.g., large, medium, small)
characterized by distinct dimension of CPUs, memory, and
storage capabilities, as well as bandwidth capacity for
forwarding the traffic generated in and out of the
corresponding data center.

The proliferation of the edge computing paradigm
further increases the potential footprint
and heterogeneity of the environments where a function
or application can be deployed, resulting in different
unitary cost per CPU, memory,
and storage. This increases the complexity of deciding
the location where a given function or application should
be best deployed, as this decision should be
influenced not only by the available resources
in a given computing
environment, but also by the network capacity of the
path connecting the traffic source with the destination.

It is then essential for a network operator to have
mechanisms assisting this decision by considering
a number of constraints related to the function or
application to be deployed, understanding how a
given decision on the computing environment
for the service edge affects the transport network
substrate. This would enable the integration of network
capabilities in the function placement decision
and further optimize performance of the deployed
application.

This document proposes the use of ALTO {{RFC7285}}
for assisting such a decision.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Computing Needs

A given network function or application typically
shows certain requirements in terms of processing
capabilities (i.e., CPU), as well as
volatile memory (i.e., RAM) and storage capacity.
Cloud computing providers such as Amazon Web Services
or Microsoft Azure, typically structure their offerings
of computing capabilities by bundling CPU, RAM and
storage units as quotas, instances, and flavors that
can be consumed in an ephemeral fashion,
during the actual lifetime of the required function
or application.

This same approach is being taken for
characterizing bundles of resources on the
so-called Network Function Virtualization
Infrastructure Points of Presence (NFVI-PoPs)
being deployed by the telco operators.
For instance, the Common Network Function
Virtualization Infrastructure Telecom Taskforce
(CNTT) {{CNTT}}, jointly hosted by GSMA
{{GSMA}} and the Linux Foundation, intends to harmonize
the definition of
instances and flavors for abstracting capabilities
of the underlying NFVI, facilitating a
more efficient utilization of the infrastructure
and simplifying the integration and certification
of functions. (Here certification means the assessment
of the expected behavior for a given function
according to the level of resources determined by
a given flavor.) An evolution of this initiative is
Anuket {{Anuket}}, which works to consolidate
different architectures for well-known tools
such as OpenStack and Kubernetes.

Taking CNTT as an example, the flavors or instances
can be characterized according to:

*  *Type of instance (T):* Used to specify the type of instances,
which are characterized as B (Basic), or N (Network Intensive).
The latter includes network acceleration extensions
for offloading network intensive operations to hardware.

* *Interface Option (I):* Used to specify the associated
bandwidth of the network interface.

* *Compute flavor (F):* Used to specify a given predefined
combination of resources in terms of virtual CPU, RAM,
disk, and bandwidth for the management interface.

* *Optional storage extension (S):* Used to specify additional storage capacity.

* *Optional hardware acceleration characteristics (A):*
Used to specify acceleration capabilities for
improving the performance of the application.

The naming convention of an instance is thus encoded as TIFSA.

# Usage of ALTO for Service Placement

ALTO can assist the deployment of a service on a
specific flavor or instance of
the computing substrate by taking into consideration
network cost metrics.
A generic and primary approach is to take into
account metrics related to the computing environment,
such as availability of resources, unitary cost
of those resources, etc.
Nevertheless, the function or application to be
deployed on top of a given flavor must also be
interconnected outside the computing
environment where it is deployed,
therefore requiring the necessary network
resources to satisfy application performance
requirements such as bandwidth or latency.

The objective then is to leverage ALTO to
provide information about the more convenient
execution environments to deploy virtualized
network functions or applications, allowing
the operator to get a coordinated service edge
and transport network recommendation.

## Integrating Compute Information in ALTO

CNTT proposes the existence of a catalogue
of compute infrastructure profiles collecting
the computing capability instances available
to be consumed. Such a catalogue could be
communicated to ALTO or even incorporated to it.

ALTO server queries could support
TIFSA encoding in order to retrieve proper
maps from ALTO. Additionally, filtered queries
for particular characteristics of a flavor
could also be supported.

## Association of Compute Capabilities to Network Topology

It is required to associate the location of the
available instances with topological information
to allow ALTO construct the overall map. The
expectation is that the management of the network and
cloud capabilities will be performed by the same entity,
producing an integrated map to handle both network and
compute abstractions jointly. While this can be
straightforward when an ISP owns both the network
and the cloud infrastructure, it can in general require
collaboration between multiple administrative domains.
Details on potential scenarios will be provided
in future versions of this document.

At this stage, four potential solutions could be
considered:

* To leverage (and possibly extend)
{{I-D.ietf-teas-sf-aware-topo-model}} for
disseminating topology information together
with the notion of function location (that would
require to be adapted to the existence of available
compute capabilities). A recent effort in this
direction can be found in
{{I-D.ietf-teas-sf-aware-topo-model}}.

* To extend BGP-LS {{RFC7752}},
which is already considered as a mechanism for
feeding topology information in ALTO, in order
to also advertise computing capabilities
as well.

* To combine information from the infrastructure
profiles catalogue with topological information
by leveraging the IP prefixes allocated to
the gateway providing connectivity to the NFVI PoP.

* To integrate with Cloud Infrastructure
Managers that could expose cloud infrastructure
capabilities as in {{CNTT}} and {{GSMA}}.

The viability of these options will be explored
in future versions of this document.

## ALTO Architecture for Determining Serve Edge

The following logical architecture defines the
usage of ALTO for determining service edges.

                             +--------+   Topological   +---------+
                             |        |   Information   |         |
                             |        |<--------------->| e.g.BGP |
                    ALTO     |        |                 |         |
      +--------+  protocol   |        |                 +---------+
      | Client |<----------->|  ALTO  |
      +--------+             | Server |
                             |        |    Computing    +---------+
                             |        |   Information   |  e.g.,  |
                             |        |<--------------->|  Infra. |
                             |        |                 |Catalogue|
                             +--------+                 +---------+
{: #EdgeArchitecture title="Service Edge Information Exchange." }

In order to select the optimal edge server
from both the network (e.g.,
the path with lower latency and/or higher bandwidth)
and the cloud perspectives (e.g., number of CPUs/GPUs,
available RAM and storage capacity),
there is a need to see the edge server as
both an IP entity (as in {{RFC7285}})
and an Abstract Network Element (ANE)
entity (as in {{RFC9275}}).

Currently there is no mechanism (neither in {{RFC9275}} nor
{{RFC9240}}) to see the same edge server as an entity
in both domains. The design of ALTO, however,
allows extensions that could be used to identify
that an entity can be defined in several domains.
These different domains and their related properties
can be specified in extended ALTO property maps,
as proposed in the next sections.

# ALTO Design Considerations for Determining Service Edge

This section is in progress and gathers the
ALTO features that are needed to support the
exposure of both networking capabilities and
compute capabilities in ALTO Maps.

In particular, ALTO Entity Property Maps defined in
{{RFC9240}} can be extended. {{RFC9240}} generalizes
the concept of endpoint properties to domains of other
entities through property maps. Entities can be
defined in a wider set of entity domains such as IPv4,
IPv6, PID, AS, ISO3166-1 country codes or ANE.
In addition, RFC 9240 specifies how properties can be
defined on entities of each of these domains.

## Example of Entity Definition in Different Domains

As there can be applications that do not
necessarily need both compute and networking information,
it is fine to keep the entity domains separate, each with
their own native properties.
However, some applications need information
on both topics, and a scalable and flexible
solution consists in defining an ALTO property
type, that:

* Indicates that an entity can be defined in
several domains;

* Specifies, for an entity, the other domains
where this entity is defined.

For instance, one possible approach is to
introduce entity properties that
list other entity domains where an
edge server is identified. This property
type should be usable in all entity
domains types. The following provides an
example where the property "entity domain mapping"
lists the other domains in which an entity is defined.

Suppose an edge server is identified as follows:

* In the IPV4 domain, with an IP address, e.g., ipv4:1.2.3.4;

* In the ANE domain, with an identifier, e.g., ane:DC10-HOST1.

To get information on this edge server as an
entity in the "ipv4" entity domain, an ALTO
client can query the properties "pid" and
"entity-domain-mappings" and obtain a response as follows:

    POST /propmap/lookup/dc-ip HTTP/1.1
    Host: alto.example.com
    Accept: application/alto-propmap+json,application/alto-error+json
    Content-Length: TBC
    Content-Type: application/alto-propmapparams+json
    {
    "entities" : ["ipv4:1.2.3.4"],
    "properties" : [ "pid", "entity-domain-mappings"]
    }


    HTTP/1.1 200 OK
    Content-Length: TBC
    Content-Type: application/alto-propmap+json
    {
    "meta" : {},

    "property-map":  {
        "ipv4:1.2.3.4" :
          {"pid" : DC10,
            "entity-domain-mappings" : ["ane"]}
        }
    }

  To get information on this edge server as an
  entity in the "ane" entity domain, an ALTO
  client can query the properties "entity-domain-mappings"
  and "network-address" and obtain a response as follows:

    POST /propmap/lookup/dc-ane HTTP/1.1
    Host: alto.example.com
    Accept: application/alto-propmap+json,application/alto-error+json
    Content-Length: TBC
    Content-Type: application/alto-propmapparams+json
    {
    "entities" : ["ane:DC10-HOST1"],
    "properties" : [ "entity-domain-mappings", "network-address"]
    }

    HTTP/1.1 200 OK
    Content-Length: TBC
    Content-Type: application/alto-propmap+json
    {
    "meta" : {},

    "property-map":  {
      "ane:DC10-HOST1"
          {"entity domain mappings :  ["ipv4"]",
            "network-address" :  ipv4:1.2.3.4}
          }
    }

Thus, if the ALTO Client sees the edge server
as an entity with a network address, it knows
that it can see the server as an ANE on which
it can query relevant properties.

Further elaboration will be provided in future
versions of this document.

## Definition of Flavors in ALTO Property Map

The ALTO Entity Property Maps {{RFC9240}}
generalize the concept of endpoint properties
to domains of other entities through
property maps. The term "flavor" or "instance"
refers to an abstracted set of computing
resources, with well-specified properties such as
CPU, RAM and Storage. Thus, a flavor can be
seen as an ANE with properties defined in terms
of TIFSA. A flavor or instance
is a group of 1 or more elements that can be
reached via one or more network addresses. So
an instance can also be seen as a PID that
groups one or more IP addresses. In a context
such as the one defined in CNTT, an ALTO
property map could be used to expose TIFSA
information of potential candidate flavors, i.e.,
potential NFVI-PoPs where an application or
service can be deployed.

{{table_PropertyMap}} below shows an example
of an ALTO property map with property values
grouped by flavor name.

      +--------+------------+-------+-----+------+------+-----+---+---+
      | flavor |  type (T)  | inter | f-c | f-ra | f-di | f-b | S | A |
      | -name  |            |  face |  pu |  m   |  sk  |  w  |   |   |
      |        |            |  (I)  | (F) | (F)  | (F)  | (F) |   |   |
      +--------+------------+-------+-----+------+------+-----+---+---+
      | small- |   basic    |   1   |  1  | 512  | 1 GB | 1 G |   |   |
      |   1    |            |  Gbps |     |  MB  |      | bps |   |   |
      .................................................................
      | small- |  network-  |   9   |  1  | 512  | 1 GB | 1 G |   |   |
      |   2    | intensive  |  Gbps |     |  MB  |      | bps |   |   |
      .................................................................
      | medium |  network-  |   25  |  2  | 4 GB |  40  | 1 G |   |   |
      |   -1   | intensive  |  Gbps |     |      |  GB  | bps |   |   |
      .................................................................
      | large- |  compute-  |   50  |  4  | 8 GB |  80  | 1 G |   |   |
      |   1    | intensive  |  Gbps |     |      |  GB  | bps |   |   |
      .................................................................
      | large- |  compute-  |  100  |  8  |  16  | 160  | 1 G |   |   |
      |   2    | intensive  |  Gbps |     |  GB  |  GB  | bps |   |   |
      +--------+------------+-------+-----+------+------+-----+---+---+
{: #table_PropertyMap title="ALTO Property Map." }

The following example uses ALTO's filtered property map
to request properties "type",
"cpu", "ram", and "disk" on five ANE flavors named
"small-1", "small-2", "medium-1", "large-1", "large-2"
defined in the example before.

      POST /propmap/lookup/ane-flavor-name HTTP/1.1
      Host: alto.example.com
      Accept: application/alto-propmap+json,application/alto-error+json
      Content-Length: 155
      Content-Type: application/alto-propmapparams+json
      {
        "entities" : ["small-1",
                      "small-2",
                      "medium-1",
                      "large-1"],
                      "large-2"],
        "properties" : [ "type", "cpu", "ram", "disk"]
      }

      HTTP/1.1 200 OK
      Content-Length: 295
      Content-Type: application/alto-propmap+json
      {
        "meta" : {
        },
        "property-map": {
          "small-1":
            {"type" : "basic", "cpu" : 1,
              "ram" : "512MB", "disk" : 1GB},
          "small-2":
            {"type" : "network-intensive", "cpu" : 1,
              "ram" : "512MB", "disk" : 1GB},
          "medium-1":
            {"type" : "compute-intensive", "cpu" : 2,
              "ram" : "4GB", "disk" : 40GB},
          "large-1":
            {"type" : "compute-intensive", "cpu" : 4,
              "ram" : "8GB", "disk" : 80GB},
          "large-2":
            {"type" : "compute-intensive", "cpu" : 8,
              "ram" : "16GB", "disk" : 160GB},
        }
      }
{: #ane-flavor-name title="Filtered Property Map query example." }

# Use Cases

## Open Abstraction for Edge Computing

As shown in this document, modern applications such as AR/VR,
V2X, or IoT, require bringing compute
closer to the edge in order to meet
strict bandwidth, latency, and jitter requirements.  While this
virtualization process can resemble the path taken
by the main cloud providers
(notably, AWS, Facebook, Google and Microsoft) to deploy
their large-scale datacenters, the edge presents a
key difference: datacenter clouds (both in terms of their infrastructure
and the applications run by them) are owned and managed by a
single organization,
whereas edge clouds involve a complex ecosystem of operators,
vendors, and application providers, all striving to provide
a quality end-to-end solution to the user. This implies that,
while the traditional cloud has been implemented for the most part
by using vertically optimized and closed architectures, the edge will necessarily need to rely on a complete ecosystem of carefully
designed open standards to enable horizontal interoperability
across all the involved parties.
This document envisions ALTO playing a role as part of the
ecosystem of open standards that are necessary to deploy and
operate the edge cloud.

As an example, consider a user of an XR
application who arrives at his/her home by car. The application
runs by leveraging compute capabilities from both the
car and the public 5G edge cloud. As the user parks the
car, 5G coverage may diminish (due to building interference)
making the home local Wi-Fi connectivity a better choice.
Further, instead of relying on computational resources from
the car and the 5G edge cloud, latency can be reduced by leveraging
computing devices (PCs, laptops, tablets) available from the home
edge cloud.
The application's decision to switch from using resources in one
domain to another, however,
demands knowledge about the compute
and communication resources available both in the 5G and the Wi-Fi
domains, therefore requiring interoperability across multiple
industry standards (for instance, IETF and 3GPP on the public side,
and IETF and LF Edge {{LF-EDGE}} on the private home side). ALTO
can be positioned to act as an abstraction layer supporting
the exposure of communication and compute information independently
of the type of domain the application is currently residing in.

Future versions of this document will elaborate further on this
use case.


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.

# Conclusions

Telco networks will increasingly contain a number
of interconnected data centers and edge
clouds of different sizes
and characteristics, allowing flexibility in the
dynamic deployment of functions and applications
for advanced services. The overall objective of
this document is to begin a discussion in the
ALTO WG regarding the suitability of the ALTO
protocol for determining where to deploy a
function or application in these distributed computing
environments. The result of these discussions
will be reflected in future versions of this draft.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
