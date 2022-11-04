---
title: "Carrying Traffic Shaping Mechanism in IPv6 Extension Header"
abbrev: "IPv6 Carries Traffic Shaping Mechanism"
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
  RFC2119:
  RFC8174:

informative:
  RFC5409:
  RFC6398:
  RFC6192:
  RFC9098:

--- abstract

Starting from the traffic shaping mechanism, one of the core technologies of network deterministic assurance, we summarize the characteristics of different traffic scheduling and shaping methods and propose a solution design for IPv6 to carry these traffic scheduling and shaping methods, taking into account deterministic and security factors. At the same time, the network scope of practical applications is becoming larger and larger, and the demand for deterministic network services will no longer be restricted to LANs, but will require deterministic forwarding beyond LAN boundaries, extending the deterministic assurance capability previously provided in LANs to WANs through network layer technologies.


--- middle

# Introduction

Time Sensitive Network (TSN) is a network that guarantees the quality of service for delay-sensitive flows, achieving low latency, low jitter and zero packet loss. Time-sensitive streams can be divided into periodic time-sensitive streams (PTS), such as cyclic control instructions in the plant, synchronization information, and non-periodic/sporadic time-sensitive streams (STS), such as event alarm information.

For periodic time-sensitive flows, synchronous scheduling shaping mechanisms are generally used, i.e., requiring precise nanosecond clock synchronization of network-wide devices, whose earliest ideas come from Time-Triggered Ethernet (TTE), and current mechanisms studied include Time-Aware Shaping (TAS), Cyclic Queuing and Forwarding (CQF), Credit-Based Shaping (CBS).

Scheduling and shaping mechanisms are two quality of service assurance mechanisms in the switch. Scheduling refers to queue scheduling, which is generally implemented at the outgoing port of the switch and consists of three parts: entering the queue, selecting the sending queue according to the scheduling algorithm, and exiting the transmission; shaping refers to traffic shaping, which prevents congestion within the switch or at the next hop by limiting the forwarding rate of the port.

Since the asynchronous scheduling and shaping mechanism cannot guarantee that the worst delay of the packet meets a certain threshold, it can only guarantee that the average delay of the packet is comparable to the synchronous method, and the delay jitter is relatively large, and the delay-sensitive stream is prone to packet loss in the case of network congestion, the current asynchronous mechanism is not mature, and in order to better elucidate the nature of the delay-sensitive network, this document of using the synchronous mechanism to transmit periodic delay-sensitive stream (PTS) is mainly discussed.

"IPv6 Hop-by-Hop Options Processing Procedures" {{!HbH-UPDT=I-D.hinden-6man-hbh-processing}} further specifies the procedures for how IPv6 Hop-by-Hop options are processed to make their processing even more practical and increase their use in the Internet. In that context, it makes sense to consider Hop-by-Hop Options to transport the information that is relevant to carry traffic shaping mechanism.

Starting from the traffic shaping mechanism, one of the core technologies of network deterministic assurance, we summarize the characteristics of different traffic scheduling and shaping methods and propose a solution design for IPv6 to carry these traffic scheduling and shaping methods, taking into account deterministic and security factors. At the same time, the network scope of practical applications is becoming larger and larger, and the demand for deterministic network services will no longer be restricted to LANs, but will require deterministic forwarding beyond LAN boundaries, extending the deterministic assurance capability previously provided in LANs to WANs through network layer technologies.

This document gives a description of the design of the IPv6 bearer traffic shaping mechanism and specifies the technical requirements and security specifications of the IPv6 bearer traffic shaping mechanism.This document applies to deterministic data communication of IPv6 networks that have implemented synchronous scheduling shaping mechanism.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Definition of Carrying Traffic Shaping Mechanism

Carrying traffic shaping mechanism in IPv6 extension header is in the form of a field on the extended header that specifies the basic traffic scheduling shaping protocol interface options for resolving the semantics of the scheduling shaping mechanism in the packet, allowing the network determinism to be transmitted through the extended header as well as for the adaptation of the upper layer protocols and network functions for use. This field information can be examined and processed by each node of the packet transmission path.

The requirements for the use of scheduling shaping include the scheduling shaping technical solution options and the control information necessary of specific solution. The definition format consists of four fields, including options, flag bits, fill bit length, and control information. The definition format is shown in Figure 1. The technical scheme here mainly specifies the synchronous scheduling and shaping mechanism option, and the asynchronous scheduling and shaping mechanism information is not transmitted through this design.


~~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |Options|F| FBL |                                               |
   +-+-+-+-+-+-+-+-+                                               +
   |                                                               |
   ~             Control Information (variable length)             ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
       Figure 1: Definition of Carrying Traffic Shaping Mechanism
~~~~~

where

*  Options: 4-bits. Indicating the synchronous traffic scheduling shaping technology scheme used.
*  F: 1-bit. Used as a flag bit to record whether the protocol content has reached its maximum length.
*  FBL: 3-bit. The number of bits used to record the padding at the end of the protocol is 0 to 7 bits to ensure that the total length of the definition content is an integer multiple of 8 bits.
*  Control Information: Variable length. Used to carry the network control information necessary for the use of a specific scheduling and shaping mechanism, in a format and content determined by the specific scheduling and shaping mechanism.

The F identifiers is a flag bit. The value of this field specifies:

~~~~~~
   0 - the length of the protocol content has not exceeded the maximum value and the information has been read completely.
   1 - the length of the protocol content exceeds the maximum value and needs to be read further.
~~~~~~

FBL indicates Fill Bit Length which is for compatibility with subsequent adaptations in the IPv6 extension header. The actual length of the control information is obtained by parsing the length of the padding bits to facilitate the reading and processing of the network control device. The padding method is to set all the padding bits at the end to 0.

The Control Information contains standard control frame format of each specific scheduling shaping mechanism, which is used to ensure the integrity of the control information to complete the standard adaptation to various network devices.


# Specification in Hop-by-Hop Options

The definition of carrying traffic shaping mechanism shall conform to the relevant specifications in {{!RFC8200}} for extended headers. The content in Section 3 should be placed in a Hop-by-Hop option header in the extended header to carry information that will not be inserted or removed and that can be examined or processed by each node in the packet transmission path until the packet reaches the node identified in the destination address field of the IPv6 header(or in the case of multicast, each of a group of nodes).

The definition populates one or more sub-options of the TLV encoding format into the option field of the hop-by-hop option header, where the TLV encoding format is shown in Figure 2.

~~~~~
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+- - - - - - - - -
|  Option Type  |  Opt Data Len |  Option Data
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+- - - - - - - - -
          Figure 2: TLV Encoding Format
~~~~~

where

*  Option Type: 8-bit identifier of the type of option.
*  Opt Data Len: 8-bit unsigned integer. Length of the Option Data field of this option, in octets.
*  Option Data: Variable-length field. Option-Type-specific data.


In the definition above, some specific instructions are required:

The Option Type identifiers are internally encoded such that their highest-order 2 bits specify the action that must be taken if the processing IPv6 node does not recognize the Option Type. Actions are selected by the controller in the network, refer to {{!RFC8200}} for specific action definitions.

The third-highest-order bit of the Option Type specifies whether or not the Option Data of that option can change en route to the packet’s final destination. The option data is changed during packet forwarding with traffice shaping information so that this bit needs to be set to 1.

The low-order 5 bits of the Option Type should not conflict with the Option Type field already defined by IPv6.

Option Data is used to carry the definition content of Section 3.

In addition, the length of the protocol defined in Section 3 exceeds the maximum length of an option, the F identifiers should be set to 1 and the protocol will continue to be stored in the next option. The protocol content in Section 3 is split into at most two options.


# Security Considerations

Security issues with IPv6 Hop-by-Hop options are well known and have been documented in several places, including [RFC6398], [RFC6192], and [RFC9098]. Security Considerations in IPv6 are composed of a number of different pieces. These are mainly required to provide the three characteristics of replay protection, integrity and confidentiality.

## Replay Protection

Replay Protection requires ensuring the uniqueness of each IP packet to ensure that the information cannot be reused in the event that it is intercepted and copied.

*  It should be ensured that access cannot be regained using the same packets to prevent attackers from intercepting deciphered information and then impersonating illegal access.
*  A secret key based on algorithm independent exchange should be set at the host side by the customer and the service provider, and when each packet is transmitted, a checksum is generated based on the secret key and the packet, and the checksum is recalculated and compared at the data receiving side.
*  Authentication data should be included in the transmission to protect the fields that cannot be changed during IP packet transmission.
*  The cache data should be cleared and guaranteed to be unrecoverable.

## Integrity

Integrity of data is to prevent data from being tampered with during transmission and to ensure that the outgoing and incoming data are identical.

*  Two-way authentication mechanism for shared data information components should be provided.
*  Encrypted transmission channels should be used to prevent data from being eavesdropped during network transmission.
*  Should have the ability to test the integrity of the data and provide the corresponding recovery control measures.

## Confidentiality

Confidentiality is used to prevent attackers from accessing packet headers or content, ensuring that information cannot be read during transmission, even if IP packets are intercepted.

*  Encryption policy of terminal data should be established to ensure the confidentiality of sensitive data output and shared at the terminal.
*  A cryptographic checksum should be generated for each packet, and the receiver should calculate the checksum before opening the packet; if the packet is tampered with and the checksum does not match, the packet is discarded.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
