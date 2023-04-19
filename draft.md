---
title: The WebRTC URI Scheme
abbrev:
docname: draft-jiang7369-webrtc-uri-scheme-02
date: {DATE}
category: std

ipr: trust200902
area: General
workgroup:
keyword:
 - WebRTC
 - URI Scheme

stand_alone: yes
smart_quotes: no
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
 -
    ins: Jiang,Jianxing
    name: Jiang,Jianxing
    country: China
    email: jiang.7369@163.com

normative:
  RFC2119:

informative:


--- abstract

This document registers the "wr://" and "wrs://" URI schemes to aid in the connect of WebRTC.

--- note_Note_to_Readers

*RFC EDITOR: please remove this section before publication*

The issues list for this draft can be found at <https://github.com/jiang7369/I-D/issues/>.

The most recent (unpublished) draft and demos is at <https://jiang7369.github.io/I-D/>.

Recent changes are listed at <https://github.com/jiang7369/I-D/commits/gh-pages/>.

See also the draft's current status in the IETF datatracker, at
<https://datatracker.ietf.org/doc/draft-jiang7369-webrtc-uri-scheme/>.

--- middle

# Introduction

URI is short and compact, convenient for transmission. You can even send it offline to others without using any server, for example, you can generate a QR Code for use.

WebRTC is a widely used real-time connection protocol, but unlike WebSocket, it has no URI scheme.

This document registers the "wr://" and "wrs://" URI schemes to supplement such gaps.  When use the URI quickly open the connection, you can immediately to realize communication between two clients.

Use this URI Scheme to easily open a connection. You can open a data channel, then the connection can be used as a file transfer or signalling server, etc. You can also directly open a stable and complex connection by passing more parameters.

WebRTC URI Scheme defines one endpoint of the connection, rather than one port of the device. It defines the connection mode of WebRTC in the Internet/LAN that conforms to human intuition. One device can open multiple connections, corresponding to multiple endpoints, without paying special attention to which port is currently used for connection.

WebRTC URI Scheme may look like a compressed version of the SDP file. It avoids the trouble of inconvenient transmission of SDP newline characters, and does not expose more information in the first connection.

In addition, the WebRTC URI Scheme facilitates communication between two computers through browser application under the premise of light server or no server. Changing connections on the network connect pairs of endpoints. WebRTC endpoints are characterized by great variability and fast variability. We can dynamically connect these points to form a dynamic network.

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT",
"RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as
described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as
shown here.

This document uses ABNF {{!RFC5234}}, Base64 {{!RFC4648}}. It also uses the pchar rule from {{!RFC3986}}.


# WebRTC URIs

This specification defines two URI schemes, using the ABNF syntax defined in RFC 5234 {{!RFC5234}}, and terminology and ABNF productions defined by the URI specification RFC 3986 {{!RFC3986}}.

~~~ abnf
       wr-URI = "wr" ":" [ host-part ] "/" endpoint [ "?" query ]
       wrs-URI = "wrs" ":" [ host-part ] "/" endpoint [ "?" query ]
       host-part = "//" [ hostport ]

       hostport = host [ ":" port ]
       host = <host, defined in [RFC3986], Section 3.2.2>
       port = <port, defined in [RFC3986], Section 3.2.3>
       endpoint = basehash pwd ufrag
       query = <query, defined in [RFC3986], Section 3.4>

       basehash = 44<pchar>
       pwd = *255<pchar>
       ufrag = *255<pchar>
~~~

See {{RFC3986, Section 3.3}} for a definition of pchar. Disallowed characters -- including non-ASCII characters -- MUST be encoded into UTF-8 {{!RFC3629}} and then percent-encoded ({{RFC3986, Section 2.1}}).

The hostport component is OPTIONAL; By default, WebRTC connection without STUN or TURN. 

The port component is OPTIONAL; By default, "wr" runs on the same ports as STUN and TURN: 3478 for "wr" over UDP and TCP, and 5349 for "wrs" over TLS.

The ufrag component, pwd component and fingerprint used in basehash component below can be find in attributes ({{!RFC8859}}, Section 5.12) of SDP ({{!RFC8864}}, Section 5.12).

The basehash component MUST be calculated by the following steps:

1.  get the length of pwd component

2.  convert length of pwd component to hexadecimal (1 Byte)

3.  get the fingerprint hexadecimal (32 Byte)

4.  connect the length hexadecimal after the fingerprint hexadecimal

5.  Calculate the Base64({{!RFC3986}}, Section 2.1) of the result<br>(33 Byte)

The advantage of this is to take endpoint component as a whole while taking readability into consideration.

Fragment identifiers are meaningless in the context of WebRTC URIs and MUST NOT be used on these URIs.  As with any URI scheme, the character "#", when not indicating the start of a fragment, MUST be escaped as %23.

Example URIs are listed in Appendix A.


# IANA Considerations

## Registration of New URI Schemes

### Registration of "wr" Scheme

A &#124;wr&#124; URI identifies a WebRTC offer and answer name.

{: vspace="0"}
Scheme name:
: wr

Status:
: Permanent

Applications/protocols that use this scheme:
: none yet

Security considerations:
: See "Security Considerations" section.

Contact:
: IETF &lt;iesg@ietf.org&gt;

Change controller:
: IETF &lt;iesg@ietf.org&gt;

References:
: (this document)

### Registration of "wrs" Scheme

A &#124;wrs&#124; URI identifies a WebRTC offer and answer name and indicates that traffic over that connection is to be protected via TLS (including standard benefits of TLS such as data confidentiality and integrity and endpoint authentication).

{: vspace="0"}
Scheme name:
: wrs

Status:
: Permanent

Applications/protocols that use this scheme:
: none yet

Security considerations:
: See "Security Considerations" section.

Contact:
: IETF &lt;iesg@ietf.org&gt;

Change controller:
: IETF &lt;iesg@ietf.org&gt;

References:
: (this document)


# Security Considerations

The token ABNF rule allows tokens as small as 0 character.  This is not recommended practice; applications should evaluate their requirements for entropy and issue tokens correspondingly.  See {{?RFC8445}} for more information.

This document not solve the problem that attackers can flood stun/turn servers. Too many open ports may cause network layer rejection.

Although the WebRTC URI Scheme makes connections easy to open, you may connect untrusted nodes in static pages, just like click the link of a phishing website. Before connecting, you must confirm whether the URI provider is trusted.

If the stun/turn server is not trusted, man-in-the-middle attack may occur on the "ws://" connection. "wrs://" is intended to reduce the incidence of man-in-the-middle attack; it cannot prevent man-in-the-middle attack on client to client connections.

And The server side of "wrs://" protocol SHALL ensure the security of each link and the handling of blacklist. 

--- back

# Example URIs

The content of sdp is as follows:

~~~ example
a=ice-ufrag:Yb1d
a=ice-pwd:uCiLWVRLVIKkrl14SzyO4TMF
a=fingerprint:sha-256 B6:9E:F3:DD:8B:83:8D:F6:95:4E:76:40:AF:F2:78
:0B:CA:78:DA:0B:73:21:1E:28:93:4F:70:DA:47:B4:41:7E
~~~

The ufrag component is "Yb1d".  
The pwd component is "uCiLWVRLVIKkrl14SzyO4TMF"  
The basehash component before Base64 is  
0xB69EF3DD8B838DF6954E7640AFF2780BCA78DA0B73211E28934F70DA47B4417E18,  
The basehash component is  
"tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w2ke0QX4Y"

So, the endpoint component is  
"tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w2ke0QX4YuCiLWVRLVIKk  
rl14SzyO4TMFYb1d"

Without STUN or TURN:

o&nbsp; If no STUN or TURN, expressed in "wr:///".  For example:

       *  "wr:///tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w2ke0QX4YuCiLWVRLVI
          Kkrl14SzyO4TMFYb1d?query=required&for&connect"

o&nbsp; Or omit "//", expressed in "wr:/".  For example:

       *  "wr:/tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w2ke0QX4YuCiLWVRLVIKk  
          rl14SzyO4TMFYb1d?query"

With STUN or TURN:

o&nbsp; Use STUN or TURN server, For example:

       *  "wr://host.example.com/tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w2  
          ke0QX4YuCiLWVRLVIKkrl14SzyO4TMFYb1d?query"

o&nbsp; Use STUNs or TURNs server,  For example:

       *  "wrs://host.example.com/tp7z3YuDjfaVTnZAr/J4C8p42gtzIR4ok09w  
          2ke0QX4YuCiLWVRLVIKkrl14SzyO4TMFYb1d?query"


# Acknowledgements
{:numbered="false"}

Frankly speaking, the document format refers to {{?RFC8959}} when writing.<br>Thank you~
