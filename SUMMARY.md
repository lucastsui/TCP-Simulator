# Project Document Summaries

This file summarizes each document in the project. For tech specs (RFCs/standards), the summary describes what the spec is about. For research or historical articles, it lists the three most important arguments.

- 2-TCP-rfc9293.pdf — Transmission Control Protocol (TCP) Specification (RFC 9293, August 2022)
  - Tech spec summary: Consolidates and modernizes the TCP standard, superseding RFC 793 and related updates. Defines the TCP header and options, state machine, connection setup/teardown, segmentation (MSS/PMTUD), retransmission and congestion control, window and urgent data handling, interfaces, and security/privacy considerations.

- 3-IPv4-rfc791.txt.pdf — Internet Protocol version 4 (IPv4) Specification (RFC 791, September 1981)
  - Tech spec summary: Defines IPv4 datagrams, addressing, fragmentation/reassembly, routing model, and the Internet header format, plus interactions with upper and lower layers and example scenarios.

- 4-IPv6-rfc8200.txt.pdf — Internet Protocol version 6 (IPv6) Specification (RFC 8200, July 2017)
  - Tech spec summary: Specifies the IPv6 base header and extension headers, option processing, packet sizing, flow label and traffic class semantics, and guidance for upper-layer protocols; supersedes RFC 2460.

- 5-rfc2960.txt.pdf — Stream Control Transmission Protocol (SCTP) (RFC 2960, October 2000)
  - Tech spec summary: Defines SCTP as a reliable, message-oriented transport with multistreaming, multihoming, path and congestion management, handshake/association procedures, and chunk formats for signaling and data.

- 6-SCTP-1135777.1135867.pdf — “SCTP: An innovative transport layer protocol for the web”
  - Three key arguments:
    - Multistreaming avoids head-of-line blocking for independent HTTP objects while preserving fairness by sharing one congestion controller across streams.
    - Multihoming enables transparent failover and robustness; SCTP’s four-way handshake mitigates SYN-flood attacks compared to TCP’s three-way handshake.
    - Operationally superior for web servers/clients: fewer TCBs and lower memory/processing overhead than many parallel TCPs; feasibility shown by Apache/Firefox prototypes.

- 4-QUIC-3098822.3098842.pdf — “The QUIC Transport Protocol: Design and Internet-Scale Deployment” (SIGCOMM ’17)
  - Three key arguments:
    - A user-space, UDP-based, fully encrypted transport enables rapid iteration and wide deployment while resisting middlebox ossification.
    - QUIC reduces latency and head-of-line blocking via 0-RTT/1-RTT cryptographic handshakes and multiplexed streams with accurate loss/RTT signaling.
    - Robustness and evolvability through Connection IDs for connection migration, modular congestion control, and proven Internet-scale performance at Google.

- cerf74(1).pdf — “A Protocol for Packet Network Intercommunication” (Cerf & Kahn, 1974)
  - Three key arguments:
    - Heterogeneous packet networks require an internetwork protocol and gateways to interconnect systems with differing packet sizes and services.
    - End-to-end principles: hosts handle sequencing, flow/error control, and checksums; gateways forward and may fragment but should not reassemble.
    - Establishes logical process-to-process connections and uniform addressing across networks, forming the conceptual basis for TCP/IP.

- McKenzie05723076(1).pdf — “INWG and the Conception of the Internet: An Eyewitness Account” (A. McKenzie)
  - Three key arguments:
    - INWG formed (1972) to harmonize interconnection/host-host requirements across emerging packet networks and set concrete objectives for a common protocol.
    - Competing designs (e.g., Cerf–Kahn’s datagram/TCP approach vs. INWG 61’s fixed-length fragment scheme) reflected fundamental choices about host vs. gateway responsibilities and flow/error control.
    - Iterative international collaboration and gateway-centric thinking shaped the transition from early notes to the IEEE 1974 paper and the Internet’s direction.

- 3-anhc-38-03-Day.pdf — “The Clamor Outside as INWG Debated: Economic War Comes to Networking” (John Day)
  - Three key arguments:
    - INWG’s technical debates (1974–1976) played out amid industry power dynamics (IBM, national PTT monopolies), influencing standards and adoption.
    - The new paradigm emphasized layered, peer networks, datagrams, and nondeterministic, distributed computing—departing from telecom-style hierarchies.
    - Choosing a transport/network model (e.g., INWG 96 context) had deep business and architectural consequences beyond pure technical merit.

- 3-DynamicLayer-nsdip13-paper11.pdf — “Dynamic Layer Instantiation as a Service” (Esposito, Wang, Matta, Day)
  - Three key arguments:
    - RINA models networking as IPC and organizes it into DIF layers that scope policy (auth, routing, addressing, resource allocation) for predictable services.
    - Dynamic instantiation/discovery of DIFs enables virtual slices (e.g., a VPC DIF) across administrative domains with location-independent naming and policy adaptation.
    - A GENI-based demo shows on-the-fly creation of a higher-level VPC DIF to bridge enterprise and cloud DIFs for application flows.

- 3-Timer-Based Mechanisms copy.pdf — “Timer-Based Mechanisms in Reliable Transport Protocol Connection Management” (R.W. Watson)
  - Three key arguments (manually summarized from page images):
    - Robust connection management needs more than data retransmission timers; protocols must time-bound control exchanges (open/close/abort) so lost, duplicated, or misordered control messages don’t corrupt endpoint state.
    - Bounding maximum packet/segment lifetime (MSL) is essential: timers such as 2×MSL prevent old duplicates from affecting new connections and underpin mechanisms like TCP’s three-way handshake and TIME-WAIT.
    - Practical timer set (handshake, keep-alive, persist/zero-window probes, abort/idle) defines implementable service guarantees and resource recovery; explicit timer design yields higher reliability than relying solely on message semantics.

- Timer-Based Mechanisms in Reliable Transport.md — “Timer-Based Mechanisms in Reliable Transport Protocol Connection Management” (typed transcript)
  - Three key arguments:
    - Reliable connection management requires explicit time-bounded control, not just data retransmission: handshakes, open/close/abort, and state initialization must be governed by timers so that lost/duplicate/misordered control messages cannot desynchronize endpoints.
    - Bounding three lifetimes is fundamental to correctness: MPL (maximum packet lifetime), R (max sender retry time), and A (max receiver ack delay). These bounds justify the three-way handshake, TIME-WAIT, sequence‑number space sizing, and duplicate suppression.
    - A timer‑centric design (as in Delta‑t) can simplify acceptance tests and improve throughput versus pure message-exchange schemes (early TCP), while enabling practical services such as keep‑alive, persist/zero‑window probing, and robust crash/restart recovery.
