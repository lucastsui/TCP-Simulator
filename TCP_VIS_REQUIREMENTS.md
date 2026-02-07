# TCP Visualization Confirmed Requirements

This file records the requirements explicitly discussed and confirmed in the thread.

## 1. Platform and Packaging

- R1: The software runs locally in a web browser.
- R2: Single-file SPA (`index.html`) with no build step and no required local server.
- R3: 2D UI only (no 3D model).
- R4: Dark theme with a sidebar control panel.
- R5: SVG-based visualization is acceptable.

## 2. Timeline and Speed

- R6: Speed control range is `0.25x` to `4x`.
- R7: At `1x`, one-way sender-to-receiver delivery time is 5 seconds.
- R8: At `1x`, ACK return time is also 5 seconds (10-second RTT total).
- R9: Timing is illustrative and not tied to real TCP timer values.
- R10: Segment pacing should be derived from `cwnd/RTT`.

## 3. Core Flow and Data Queue

- R11: Sender has a bar showing data awaiting send (health-bar style).
- R12: If queued data is non-zero, keep sending according to TCP logic.
- R13: Sender data input uses a byte-size slider.
- R14: Byte-size slider defaults to 32 KB.
- R15: Byte-size slider range is 1 KB to 10 MB, step 1 KB.
- R16: MSS is explicit and user-adjustable.
- R17: Default MSS is 1460 bytes.
- R18: Segmentation uses full segments where possible, with final partial segment.
- R19: Effective MSS must auto-adjust to keep IP packet size within MTU when headers/options grow.

## 4. Protocol Coverage

- R20: Include handshake visualization (SYN, SYN-ACK, ACK).
- R21: Include retransmission timer (RTO) visualization.
- R22: Include fast retransmit / fast recovery visualization.
- R23: Include flow control visualization (`rwnd`), with sender clamped by `min(cwnd, rwnd)`.
- R24: Include congestion control (simplified teaching model: slow start + congestion avoidance).
- R25: Initial `cwnd` default is 2 MSS and user-adjustable.
- R26: Initial `ssthresh` default is infinity until first loss.
- R27: ACK policy default is ACK every segment.
- R28: Include Nagle toggle (coalesce sub-MSS writes when unacked data exists).
- R29: Include keep-alive toggle with accelerated visualization timing.
- R30: Include zero-window and persist probing with accelerated visualization timing.
- R31: Include close visualization with FIN/ACK exchange and TIME-WAIT duration.
- R32: Include delayed ACK control (available, default off).

## 5. Fault Injection and Recovery

- R33: Include freeform fault injection controls (not presets only):
  - drop segment N
  - reorder N with N+1
  - duplicate segment N
  - corrupt frame N
- R34: No fixed cap on number of concurrent fault injections.
- R35: When SACK is enabled, ACK visuals should include SACK blocks.
- R36: Sender UI should include a minimal SACK scoreboard.

## 6. Packet/PDU Visualization

- R37: In-flight PDU is represented as a box containing smaller boxes for protocol headers.
- R38: Header boxes must display field names and values.
- R39: Include nested/collapsible Ethernet + IP + TCP headers around payload.
- R40: Include distinct header region and payload region sized proportionally to byte size.

## 7. Header Fields and Layer Details

- R41: Show plain Ethernet II only (no VLAN tag support).
- R42: Include IPv4/IPv6 toggle.
- R43: For TCP header display, show:
  - source port
  - destination port
  - sequence number
  - acknowledgment number
  - data offset
  - flags
  - window
  - checksum
  - urgent pointer
  - options
- R44: IP display can stay minimal; no requirement to show extra IPv4 fields beyond clarity needs.

## 8. Checksums and Educational Math View

- R45: Computed checksums are required (not placeholders).
- R46: Compute TCP checksum with pseudo-header (version-dependent).
- R47: Compute IPv4 header checksum.
- R48: Compute Ethernet FCS (CRC-32).
- R49: Include a checksum "show math" panel with byte/running-sum style explanation.

## 9. TCP Options

- R50: TCP options must be toggleable.
- R51: Include toggles for:
  - MSS
  - SACK Permitted
  - Window Scale
  - Timestamps

## 10. Accessibility and Controls

- R52: Include keyboard controls:
  - space: play/pause
  - arrows: step
  - +/-: speed adjust
- R53: Include tooltips and colorblind-safe palette.

## 11. Explicit Out-of-Scope

- R54: No export (PNG/GIF) required.

