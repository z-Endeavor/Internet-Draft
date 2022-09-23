---
title: "Carrying Traffic Shaping Mechanism in IPv6 Extension Header"
abbrev: "Carrying Traffic Shaping Mechanism in IPv6 Extension Header"
category: info

docname: draft-ietf-traffic-shaping-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "z-Endeavor/Internet-Draft"
  latest: "https://z-Endeavor.github.io/Internet-Draft/draft-ietf-traffic-shaping.html"

author:
 -
    fullname: Your Name Here
    organization: Your Organization Here
    email: "46804994+z-Endeavor@users.noreply.github.com"

normative:
  RFC8200:

informative:
  RFC5409:

--- abstract

Starting from the traffic shaping mechanism, one of the core technologies of network deterministic assurance, we summarize the characteristics of different traffic scheduling and shaping methods and propose a solution design for IPv6 to carry these traffic scheduling and shaping methods, taking into account deterministic and security factors. At the same time, the network scope of practical applications is becoming larger and larger, and the demand for deterministic network services will no longer be restricted to LANs, but will require deterministic forwarding beyond LAN boundaries, extending the deterministic assurance capability previously provided in LANs to WANs through network layer technologies.


--- middle

# Introduction

Starting from the traffic shaping mechanism, one of the core technologies of network deterministic assurance, we summarize the characteristics of different traffic scheduling and shaping methods and propose a solution design for IPv6 to carry these traffic scheduling and shaping methods, taking into account deterministic and security factors. At the same time, the network scope of practical applications is becoming larger and larger, and the demand for deterministic network services will no longer be restricted to LANs, but will require deterministic forwarding beyond LAN boundaries, extending the deterministic assurance capability previously provided in LANs to WANs through network layer technologies.

This document gives a description of the design of the IPv6 bearer traffic shaping mechanism and specifies the technical requirements and security specifications of the IPv6 bearer traffic shaping mechanism.This document applies to deterministic data communication of IPv6 networks that have implemented synchronous scheduling shaping mechanism.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Definition of Carrying Traffic Shaping Mechanism

Carrying traffic shaping mechanism in IPv6 extension header is in the form of a field on the extended header that specifies the basic traffic scheduling shaping protocol interface options for resolving the semantics of the scheduling shaping mechanism in the packet, allowing the network determinism to be transmitted through the extended header as well as for the adaptation of the upper layer protocols and network functions for use. This field information can be examined and processed by each node of the packet transmission path.

The requirements for the use of scheduling shaping include two main aspects: the scheduling shaping technical solution options and the key information necessary for this solution. The protocol format consists of four fields: options, flag bits, fill bit length, and control information. The protocol format is shown in **Figure x**. The technical scheme here mainly specifies the synchronous scheduling and shaping mechanism option, and the asynchronous scheduling and shaping mechanism information is not transmitted through this design. 

~~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                                   +-+-+-+-+-+-+-+-+
                                                   |  OPT  |F|  FB |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~             Control Information (variable length)             ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~

where

*  Options(OPT): 4-bits. Indicating the synchronous traffic scheduling shaping technology scheme used.
*  Flag: 1-bit. Used to record whether the contents of this protocol have reached their maximum length.
*  Fill Bit length(FB): 3-bit. The number of bits used to record the padding at the end of the protocol is 0 to 7 bits to ensure that the total length of the definition content is an integer multiple of 8 bits, which is compatible with the subsequent adaptation in the IPv6 extension header. The actual length of the control information is obtained by parsing the length of the padding bits to facilitate the reading and processing of the network control device. The padding method is to set all the padding bits at the end to 0. 
*  Control Information: Variable length. Used to carry the network control information necessary for the use of a specific scheduling and shaping mechanism, in a format and content determined by the specific scheduling and shaping mechanism. The standard control frame format of each specific scheduling shaping mechanism is used to ensure the integrity of the control information to complete the standard adaptation to various network devices.


# Specification in Hop-by-Hop Options

The definition of carrying traffic shaping mechanism shall conform to the relevant specifications in IETF RFC8200 for extended headers.

The definition populates one or more sub-options of the TLV encoding format into the option field of the hop-by-hop option header, where the TLV encoding format is shown in **Figure x**.

~~~~~
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+- - - - - - - - -
|  Option Type  |  Opt Data Len |  Option Data
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+- - - - - - - - -
~~~~~

where

*  Option Type: 8-bit identifier of the type of option.
*  Opt Data Len: 8-bit unsigned integer. Length of the Option Data field of this option, in octets.
*  Option Data: Variable-length field. Option-Type-specific data.

In the definition above, some specific instructions are required:

The Option Type identifiers are internally encoded such that their highest-order 2 bits specify the action that must be taken if the processing IPv6 node does not recognize the Option Type. Actions are selected by the controller in the network, refer to IETF RFC8200 for specific action definitions.
The third-highest-order bit of the Option Type specifies whether or not the Option Data of that option can change en route to the packet’s final destination. The option data is changed during packet forwarding with traffice shaping information so that this bit needs to be set to 1.
The low-order 5 bits of the Option Type should not conflict with the Option Type field already defined by IPv6.

Option Data is used to carry the definition content of Chapter 3.


# Security Considerations

Security Considerations are composed of a number of different pieces. These can be used to provide confidentiality, integrity, and replay protection; though some of these can be configured manually, generally a key management component is used.  

## Confidentiality


## Integrity


## Replay Protection



# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
