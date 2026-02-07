# TCP Simulator

This project is a browser-based, single-file TCP visualization and experiment sandbox (`index.html`).

## Purpose

The original purpose of this simulator is to experiment with whether TCP protocol behavior can be modified to improve throughput, evaluated by goodrate. vGoodrate (in this project) means:
- receiver-consumed, in-order payload data rate over time
- excluding out-of-order data that is buffered but not yet consumable
- excluding duplicate arrivals that do not advance receiver consumption

## What It Mimics From TCP
The simulation includes these TCP-like behaviors:
- Three-way handshake (`SYN`, `SYN-ACK`, `ACK`)
- Segmentation of sender data by MSS/effective MSS (MTU-aware)
- Sequence/ACK progression with cumulative ACK behavior
- Basic flow control using receiver window (`rwnd`)
- Congestion-window-driven sending (`cwnd`)
- Slow start and congestion avoidance (teaching model)
- Fast retransmit / fast recovery (simplified Reno-style)
- Retransmission timeout (RTO) handling
- Zero-window persist probing (toggle)
- Keep-alive probing (toggle)
- Connection close path including FIN exchange and TIME-WAIT visualization
- In-flight Ethernet/IP/TCP header rendering with checksum/FCS calculations

## What Is Simplified
This is an educational visual simulator, not a production TCP stack:
- Event timing is illustrative and animation-driven, not kernel/network-clock accurate
- Congestion control is simplified (teaching-oriented), not Linux/BSD implementation parity
- ACK/loss/recovery logic is simplified relative to full RFC behavior
- Network path is abstracted to a line with configurable delay variation and random loss
- Sender/receiver buffers are simplified UI-driven models
- Checksum/FCS views are educational and integrated into the visual model

## What Is Left Out Compared To Real TCP
Compared with modern real-world TCP stacks, this simulation omits or does not fully model:
- Advanced loss recovery (for example PRR, RACK/TLP behavior)
- Full SACK-driven retransmission selection logic
- Modern congestion algorithms (for example CUBIC/BBR details)
- ECN, pacing internals, and implementation-specific heuristics
- Path MTU discovery dynamics, fragmentation edge cases, NIC offloads
- OS socket API behavior, scheduler effects, and real NIC/driver queueing
- Multi-path and middlebox interactions

## Running
No build step is required.
1. Open `index.html` in a modern browser.
2. Adjust controls in the left sidebar.
3. Use Start/Pause/Reset and inspect Event Log, packet visuals, and Receiver Goodput chart.

