**Timer-Based Mechanisms in Reliable Transport
Protocol Connection Management**

Richard W. Watson
University of California, Lawrence Livermore Laboratory,
Livermore, California 94550, USA

There is a need for timer-based mechanisms (in addition to retransmission timers) to achieve reliable connection management in transport protocols designed to operate in a general network (datagram and internetwork) environment where packets can get lost, duplicated, or missequenced. This need is illustrated by discussing timer mechanisms and assumptions (1) in the Department of Defense Transmission Control Protocol, initially designed using only a message exchange mechanism; and (2) in the Lawrence Livermore Laboratory Delta-t protocol, designed explicitly to be timer-based. Some of the implementation and service implications of the approaches are discussed. The bounding of maximum packet lifetime and related parameters is important for achieving transport protocol reliability and a mechanism is outlined for enforcing such a bound.

**Keywords:** Computer network, transport protocol, interprocess communication, host-to-host protocol, end-to-end protocol, timer protocol, connections, connection management, reliable communication, transaction communication, maximum packet lifetime, 3 way handshake, packet switching, network operating system.

Richard W. Watson received his B.S. from Princeton University in 1959, and his M.S. and Ph.D. from the University of California, Berkeley in 1962 and 1965. All degree were in Electrical Engineering and Computer Science. During the years 1964–1966 he was a Stanford University Computer Science Department. He has been affiliated with the University of California (Berkeley, Davis) as a Lecturer part time from 1966–1971. He was associated with Shell Development Co. as Supervisor of Computer Science Research from 1971–1976 with SRI International’s Augmentation Research Center as Assistant Director of Development. He is currently with the Computation Department at Lawrence Livermore Laboratory, his area of research and development includes hybrid graphics and man-machine interface, operating systems, and computer networks. He is author of the book *Timesharing System Design Concepts*.

*To wish to acknowledge the valuable conversations on these issues with John Fletcher and Lansing Sloan at LLNL and with Jon Postel and others in the ARPA TCP community. The work performed here was under the auspices of the U.S. Department of Energy at the Lawrence Livermore National Laboratory under contract No. W-7405-ENG-48.*

1. **Introduction**

Protocols at every level of a network architecture deal with two types of information: control and data. A protocol provides services with respect to the data. Control information is sent between and interpreted by the cooperating protocol modules to support the protocol’s service. Mechanisms at any protocol level designed to deal with error control or assurance issues such as misdirected, lost, damaged, duplicated, or missequenced information are based on (1) identifying the data or control units being error controlled; (2) reliably transmitting control or redundant information; (3) reliably initializing, maintaining, and terminating state information at each end; (4) assumptions made about the environment provided by the supporting lower level protocols and communication technology.

These mechanisms are often based on explicit or implicit (often unconscious) assumptions about bounds on certain intervals. While the discussion in this paper focuses on transport level protocols, many of the issues discussed are also applicable to protocols at other levels.

Transport protocols designed for reliable transmission provide one or more error controlled channels between transport address pairs (associations). While state information at each end of an association is maintained for any purpose, a connection is said to exist. The state information is maintained in connection records. An important aspect of assurance is proper synchronization and evolution of state information in connection records in the face of arbitrary transmission delays, errors, and end-node crashes and restarts. This paper is called connection management. In this paper connection management mechanisms used in two protocols, the Department of Defense Transmission Control Protocol (TCP) [5], and the Lawrence Livermore Laboratory (LLL) Delta-t protocol [3,8], are compared to illustrate the role timer-based mechanisms play in achieving a reliable transport protocol, to demonstrate the requirement for bounding facts affecting the lifetime of assurance identifiers, and highlight some of the implementation and usage implications of different connection management mechanisms. Both the TCP and Delta-t protocols were designed explicitly to operate in a general network (internetwork and datagram) environment where packets can be duplicated, lost, damaged, or missequenced. In the following discussion, although some background is provided, we assume that the reader is familiar with the basic issues of connection management and reliable transport protocol design and the fundamentals of the TCP and Delta-t assurance mechanisms [3,4,7].

2. **Background**

There are three logical phases in connection management (explicit phase separation is not required): (1) initializing (opening) the connection records at each end; (2) evolving the state during ongoing data transfer; and (3) resetting or terminating (closing) state information when no further data requires transferring. During the reliable opening of a transport protocol assurance connection, the main problem is establishing initial error control identifiers, usually Sequence Numbers (SNs) or SNs qualified by connection identifiers, meeting the following conditions:

O1: If no connection exists and the receiver is willing to receive, then no packets from a previously closed connection should cause a connection to be initialized and duplicate data to be accepted.

O2: If a connection exists, then no packets from a previously closed connection should be acceptable within the current connection.

In a graceful close of a connection, each side must know that the other side has received any data sent. This implies two conditions:

C1: A receiving side must not close until it has received all of a sender’s possible retransmissions and can unambiguously respond to them, and

C2: A sending side must not close until it has received an Acknowledgment (Ack) for all its transmitted data or allowed time for an Ack of its final transmission to return before reporting a failure.

We believe a graceful close is important in an assurance protocol to limit those situations where ambiguity can exist as to whether data sent reached the other side. In particular we would like to limit ambiguity to end-node crash or network partition events. Ambiguity exists if a sender does not receive an Ack of some data sent. It will not know whether the data or Ack got lost. Depending on how it responds, lost or duplicate data may result. Ambiguity may thus require more expensive higher level error recovery mechanisms.

Most of the design decisions for a complete transport protocol are independent of the choice of a connection management mechanism, e.g., addressing, flow control, options, choice of basic transport data unit(s), etc. Section 7 discusses one area where the connection management mechanism may influence another protocol design decision. Many design decisions associated with assurance issues are also independent of the connection management mechanism. For example:

• What is the meaning of an Ack? Does it mean that the data reached the destination protocol module or that the data were placed safely in the destination user’s buffer?

• What is the unit and identification scheme used for error control?

• Should header, packet, or end-end checksums be used to protect against damaged packets?

Most importantly, we believe, all reliable transport protocols require explicit or clearly understood implicit bounds on three lifetime factors:

• *MPL*, the maximum time an identified data or control unit can exist within the routing network, which includes time in origin and destination protocol modules. At the transport level this is often called maximum packet lifetime (MPL). At other levels, it would be maximum frame, message, or other unit lifetime.

• *R*, the maximum time a sender requiring a positive acknowledgment will keep trying to retransmit a unit before ceasing retransmission.

• *A*, the maximum time a receiver will hold a unit before sending an Ack.

One can provide a mechanism to bound these factors explicitly or make “reasonable” assumptions about their bounds. These quantities can be bounded either individually [3] or as a sum [8]. The value(s) assumed in each data flow direction may be the same [3] or different [8]. In this paper we deal with these quantities as if bound separately.

In its first versions, TCP initiated a connection by having the first packet of a connection contain a synchronization flag (syn) indicating to the receiver that connection SNs would start with the one in that packet. This mechanism did not satisfy condition O1 and quickly led to difficulties with duplicates. An extended message exchange mechanism was introduced to satisfy O1. This mechanism requires an end to receive an Ack of its connection opening SN before it will accept data. This exchange, illustrated in Section 4, is called a three-way handshake [4,7]. As TCP designers came to more fully understand connection management hazards, time-based mechanisms were added. The mechanisms implicitly assume bounds on MPL, R, and A.

Delta-t, using the TCP experience, was designed from the beginning with connection management based solely on timer mechanisms. This mechanism explicitly recognizes the necessity to bound the quantities MPL, R, and A. Delta-t maintains connection records while any data unit or Ack (including duplicates) with a given identifier can be generated or exist on a given association.

In comparing protocol assurance mechanisms, one needs to ask two questions: (1) whether for an equivalent level of assurance one is more complicated or uses more resources than the other, or (2) for a given set of design decisions whether one is more complicated, uses more resources, or gives more assurance. One also needs to examine other service implications of each approach such as delay or throughput.

Our contention, illustrated with the two protocols discussed, is that because there is something fundamental at the transport level about the requirement to bound MPL, R, and A to achieve assurance, a transport protocol explicitly based around this notion should be simpler for a given set of equivalent assurance assumptions. The potential simplification in packet acceptance testing could result in higher throughput [2]. It also allows a reliable process-to-process, transaction-oriented (low delay) service to be efficiently supported. Delta-t is currently being implemented. Although as this is written we cannot refer to operational experience in comparing Delta-t with TCP, we feel it is important to present the following connection management issues because of the current level of national and international activity in the transport protocol design area.

### **3. Achieving Reliable Connection Management**

Perfect error control is only possible if three assurance conditions are met:

A1: An identifier of an information unit used for assurance is never reused while one or more copies (duplicates) of that unit or its Ack exist.

A2: The assurance state or redundant information maintained at each end is never lost or damaged.

A3: The assurance control information transmitted between each end is itself perfectly error controlled.

Let us now consider how each protocol deals with these conditions and relate them to the opening and closing conditions of the previous section. Condition A1 implies opening condition O2 and that within a connection the size of the SN field, *n*, must satisfy an SN field-size inequality. Also implied, if A2 cannot be met, is that there must be care in restarting after a crash.

An example SN field size inequality is:

2ⁿ > (2MPL + R + A)T,

where *R* belongs to the origin desiring an Ack, *A* belongs to the destination, and *T* is the maximum rate at which SNs can be generated at the origin. This inequality assumes that SNs are generated at the origin at the maximum transmission rate, but if SNs can be skipped, such as by TCP’s “rubber EOL’s” [5] or Delta-t’s window overrun mechanism [8], these must be considered. The assumption on which this inequality is based is that while possibly retransmitting one unit, units with new SNs are transmitted at the maximum rate. If a different retransmission/transmission strategy were used, a different function would result. This particular SN field-size inequality results because in the worst case an SN of a unit or its Ack can live for the following periods. A unit with a given SN could continue to be emitted from the origin for *R* (because all but the last retransmitted instance were lost, for example). The last instance could take MPL to reach the destination. The destination could delay before sending an Ack, and the Ack could take MPL to reach the origin. Thus in this interval new SNs are assumed to be generated at the rate *T*.

This result illustrates that maximum transmission rate, reliable transmission, the transmission strategy, and the lifetime factors are intimately interrelated and must be carefully considered and be made explicit in choosing *n*. Too small an *n* could limit reliable throughput, particularly serious if used on a high bandwidth local area network.

We now examine requirements for the two protocols to meet conditions O2. Because TCP does not use connection identifiers and can close a connection while duplicate packets can still exist within the network, it requires a special mechanism to choose initial sequence numbers (ISNs) larger (modulo 2ⁿ) than any used in recent connections. The accepted mechanism to do this, requiring explicit pre association state to be safely maintained across a crash [4], maps a monotonically increasing clock value into the ISN value. The clock must continue to operate across a crash. Because both the clock and SN fields can wrap around, there are critical relationships between the quantities MPL, R, and A, the rate at which SNs are generated, the clock rate, and the SN and clock field sizes that introduce hazards resulting from crashes due to SN reuse while units or Acks with that SN still exist. The TCP designers extensively analyzed these hazards [4,7]. After considering various dynamic timer oriented mechanisms, the TCP designers chose to omit them and instead chose the following simpler scheme. An origin waits an interval after a crash before opening a connection to allow time for all packets from any previous connection to die. Therefore there will be no risk of a packet of a new connection having an SN used on a packet or Ack of a previous connection. For perfect assurance this interval must satisfy the inequality:

Sending Recovery Interval > 2MPL + A.

This inequality satisfies the worst case that results when the origin sent a packet just before it crashed with an SN that might be used in a new connection to be established after the crash. The last packet and then recovered instantly. The last packet could take MPL to reach the destination, the destination waits A to emit an Ack, and the Ack takes MPL to reach the origin. In the networks in which TCP operates, the interval MPL is only approximately bounded and can be very large (several hours), although the probability is quite low that packets will live beyond 30 to 60 sec. Therefore, the engineering decision was made to wait about 2 min after a crash. No Receiving Recovery Interval is needed because TCP’s three-way handshake and reset mechanisms handle recovery from half-open connections [7].

Delta-t maintains state until it can meet condition O2. In Delta-t any ISN can be used because if no active (timers > 0) connection record exists for an association, it is guaranteed by the Delta-t timer intervals and rules (assuming MPL, R, and A are bounded) that no data or Ack packets from a previous connection exist [3]. Delta-t senders and receivers must wait an interval after a crash before initiating or accepting a conversation. For senders it is:

Sending Recovery Interval > 3MPL + R + A.

For receivers, the Receiving Recovery Interval is:

Receiving Recovery Interval > 2MPL + R + A.

This interval guarantees that no unit with a given SN will exist. We include motivation for the TCP timer interval because we have not seen an explicit analysis in the TCP literature.

In summary, to meet A1 and thus O2, both protocols require satisfaction of an inequality on SN field size and must wait an interval after a crash. TCP requires an additional clock based mechanism for ISN selection.

If A2 is met, the state information in connection records would never be lost or damaged. This condition is difficult or impossible to satisfy. TCP and Delta-t have the same issues and the same hazards here because of hardware errors, software bugs, or crashes with loss of memory. The implication of the latter was discussed above.

A3 requires that control information (e.g. that used for connection opening and closing) must itself be error controlled. TCP uses special packet header flags to indicate connection opening and closing packets, called syn and fin respectively. TCP protects its syn and fin flags against loss and duplication by placing them in the data SN space. Delta-t there are no open or close flags corresponding to TCP’s syn or fin flags. There is instead a data-run flag (DRF) used to signal the receiver that all previous SNs have been Acked. DRF does not require an Ack and is used to aid handling packet missequencing when a connection does not yet exist [3].

We now discuss how each protocol meets condition C1 (Section 2). TCP achieves a reliable close by requiring the sender of the last fin-Acked data to wait until all possible retransmissions of its partner’s final packet have been received or until its partner has timed out and discarded its connection state. The assumption is that the fin-Ack may also Ack data, otherwise no hazard exists. This holding of state is required to distinguish a normal close from a crash or network failure with lost data. This requirement could cause unnecessary confusion possibly requiring entry to expensive higher level error recovery procedures. Therefore, the sender of the final fin-Ack must wait an interval:

Final Close Interval > MPL + R,

in order to assure it can respond to all its correspondent’s retransmitted fins, where *R* is that used by the sender of the last packet with a fin. This inequality follows because, in the worst case, the final fin packet arrives at its destination instantaneously and its Ack is lost. The sender then retransmits it after *R* and the retransmission takes MPL to arrive. This TCP close-time is similar to Delta-t receive-time.

We have not seen condition C2 discussed in the TCP literature, i.e., how long should the TCP module wait after emitting its last retransmission of a packet without receiving an Ack before reporting failure to its user program. The answer is the timer interval:

Giveup Interval > MPL + A.

This condition results because the last emitted packet can take MPL to reach the destination, and the Ack can take MPL to reach the origin of the data packet. If a problem is reported before this Giveup Interval, the user process may create an unnecessary duplication or the user process may unnecessarily enter an involved error recovery procedure to deal with what appears as a possible partial crash or network failure.

This interval was considered as one condition on Delta-t’s send-timer value.

In summary, TCP uses three separate connection management mechanisms to achieve assurance: (1) exchange of connection management (syn, fin) messages and associated state transitions, (2) careful choice of an ISN, and (3) assurance timers with intervals containing assumptions about the bounds on MPL, R, and A. Delta-t uses a single timer mechanism with assumptions about bounds on MPL, R, and A.

In comparing TCP with Delta-t, we see the following. TCP requires additional complexity to deal with special states to synchronize syn and fin and carefully choose an ISN. TCP also requires a reset mechanism, not required in Delta-t. These mechanisms require additional packet acceptance overhead during normal data transfer as well [2]. If senders are allowed to overrun a receiver’s flow-control window, Delta-t requires an extra message, not needed in TCP, to resynchronize SNs [8].

The final question is, if explicit bounds on MPL, R, and A are not guaranteed and we rely on an engineering decision (mentioned earlier), is one or the other protocol safer? Both rely on these being bounded for reliable operation. Because Delta-t relies on the bound to meet condition O1 and TCP does not, some extra hazard exists. The level of hazards depends on the percentage of packets that can live beyond the assumed bound. We believe that this would be best if all protocols bound MPL, R, and A. These quantities can be bound with support from the lower environment as described in Section 6.

### **4. Transaction Applications**

We believe a useful design goal is to minimize the total number of messages and the delay needed to reliably send a single data message. This results in minimizing network traffic and increasing system performance. There are many important transactional applications, such as distributed operating systems, where a request may be sent, a reply may be received, and no further conversation is required [9]. It is important to minimize messages or packets at the transport level, because each packet at this level may require that many frames or blocks be transmitted by lower-level link or network technologies. There is increased delay plus considerable work and host overhead in handling each packet, frame, or block. We want to minimize the number of data units that all parties must handle. A datagram service can meet this need; however datagrams are not reliable, and many transaction-oriented applications could benefit from a reliable transaction-oriented transport mechanism.

To reliably send a single data packet, Delta-t only requires two packets to be exchanged, the data packet and its Ack. TCP requires a minimum three-message exchange procedure to send one message, but there is a hazard inherent in this procedure not present in Delta-t. This hazard does not exist in TCP if its use of sequence numbers does not exist.

The three-message procedure also requires a special case in the TCP rules and implementation, making writing the specification more complex.

The three TCP messages are, assuming node A sending to node B:

M₁(A to B) ISN, syn, data, fin
M₂(B to A) ISN, syn, fin, Ack (syn, data, fin)
M₃(A to B) Ack (syn, fin)

The elements in parentheses are the elements being Acked. The TCP module at B cannot deliver the data in M₁ until it receives M₃, because it is not until that point that it knows M₁ was not an old duplicate from a previous connection. Holding the M₁ data until the arrival of M₃ is a special case not needed for succeeding data in a longer connection.

A hazard results because if M₃ or retransmissions of M₃ does not get through, the data in M₁ cannot be delivered. Yet M₁ was Acked, and therefore was misdelivered. Basically, this hazard arises because no protocol can support positive acknowledgment with retransmission of the last-message. This hazard can be removed with a wait between connections or a five-message procedure [1]:

M₁(A to B) ISN, syn
M₂(B to A) ISN, syn, Ack (syn)
M₃(A to B) data, fin, Ack (syn)
M₄(B to A) fin, Ack (data, fin)
M₅(A to B) Ack (fin)

There is another hazard in the three-message procedure if B crashes after it has sent M₂ and before it delivers the data. This hazard exists for any protocol if the design decision is that an Ack means delivery to the protocol module rather than to the user buffer in main memory at the time of a crash; it could be lost before it was acted on. Perfect error control is impossible at any level, but we believe our designs should reduce the probability of a problem. In either the three- or five-message exchange cases above, TCP requires a transaction delay three times that of Delta-t, because data in TCP can only be delivered after the exchange of three messages. Independent of the total number of messages necessary to reliably send a single data message, the delay penalty required in a message-exchange-based connection management mechanism could place serious performance limitations on a transaction-oriented application such as a distributed operating system.

### **5. Required Connection Record Resources**

It was suggested that Delta-t might use more connection-record resources than TCP because it must hold inactive connection records while they time out [7]. But TCP also needs to hold connection records, as discussed earlier, after a fin-Ack and before after the last retransmission of a packet. Delta-t requires both ends of an association to hold connection records under timer control.

The connection records required by both protocols should be about the same size. For reasonable assumptions about numbers of connections per host and values for MPL, R, and A (comparable for each protocol with the magnitude of A dependent on the assumption about the meaning of an Ack), the number of connection records held under timer control waiting to close is quite small and about the same in each case and not significant.

The sum of the lifetime factors can be bound to an arbitrarily small value even if the underlying network has a large MPL (Section 6). The value chosen for this sum must reflect realistic times for R, A, and transmission time across the network to avoid unnecessarily destroying packets because their lifetime expires too early. For example, for a large internetwork, and assuming that units are Acked on reaching the destination protocol module, binding the sum to be 60 sec might be reasonable. Then assuming a given host might have 600 connections per hour, there will be on average about 10 to 30 inactive connection records waiting to time out for each active connection.

In a local network transaction environment there will be considerably more connections per hour, but now the lifetime sum above will be much smaller. Therefore the connection record time product should be about the same.

One of Delta-t’s design goals was to remove connection management as a user interface issue. That is, connection opening and closing primitives are not required at the interface as, we believe, they provide no essential user service. Their main function is to aid the underlying implementation manage connection record, buffer, or identifier resources. Delta-t’s connection management mechanism allows implementation to simply reclaim inactive connection record or buffer space when timeouts occur during lags in the conversation. If there are lags in the conversation, TCP could also reclaim space, but the required fin and syn exchange overheads to close and start up again dissuade this, particularly if the connection opening and closing is visible at the interface. Therefore, TCP may actually use more connection record resources than Delta-t.

### **6. Enforcing a Bound on MPL**

As discussed earlier, reliable transport protocol assurance mechanisms require bounds on MPL, R, and A. In many cases these bounds are only approximate and depend on engineering decisions. It would be desirable to provide a mechanism to explicitly enforce a bound on MPL. This would reduce the hazard associated with packets living longer than assumed, and would allow transport protocols to operate with smaller timer values, thereby reducing delays and resource requirements.

One possible mechanism for enforcing an MPL bound is to include a hop count or time-to-live field in packets, as is done in some network protocols. However, hop counts are not always a reliable indicator of elapsed time, particularly in internetwork environments where delays may be highly variable.

An alternative approach is to include a timestamp in each packet and require that the packet be discarded if its age exceeds a specified limit. This requires that clocks at different nodes be synchronized within a known bound, or that the timestamp be interpreted relative to a local clock with conservative assumptions. The timestamp mechanism can be implemented at various levels in the protocol hierarchy.

Another approach is to use per-hop timers that decrement a lifetime field based on elapsed time rather than hop count. This requires each node to maintain timers and update packet state as it is forwarded. While this introduces some overhead, it provides a more direct enforcement of an MPL bound.

The choice of mechanism for bounding MPL depends on the characteristics of the underlying network and the performance requirements of the transport service. In environments where packets may be queued for long periods or routed dynamically, explicit enforcement becomes more important.

By providing a mechanism to bound MPL, the assumptions required by timer-based transport protocols become explicit and enforceable. This reduces the reliance on engineering judgment and improves the robustness of reliable communication in general network environments.


### **7. Conclusions**

Timer-based mechanisms are required, in addition to retransmission timers, to achieve reliable connection management in transport protocols designed to operate in general network environments where packets may be lost, duplicated, or missequenced. Message-exchange-based connection management mechanisms, such as those used in TCP, implicitly rely on timer assumptions and therefore do not eliminate the need to bound lifetime factors such as MPL, R, and A.

The comparison between TCP and Delta-t illustrates that when the requirement to bound these lifetime factors is made explicit and forms the basis of the protocol design, connection management mechanisms can be simplified. Delta-t eliminates the need for special connection opening and closing message exchanges, reduces the number of protocol states, and avoids the complexity associated with selecting initial sequence numbers that do not conflict with those from previous connections.

Explicit timer-based connection management can also improve performance, particularly for transaction-oriented applications, by reducing the number of messages exchanged and the delay before data can be delivered. In addition, making lifetime assumptions explicit allows for clearer analysis of protocol correctness and hazards, and facilitates the design of mechanisms to enforce these bounds in the underlying network.

We believe that future transport protocol designs should explicitly recognize and incorporate bounds on MPL, R, and A, and should rely on timer-based mechanisms as the primary means of achieving reliable connection management. Doing so can lead to simpler, more efficient, and more robust protocols for reliable communication in complex network environments.