# TCP Visualization Verification

Verification target: `TCP_VIS_REQUIREMENTS.md`  
Implementation under test: `index.html`  
Validation method:

- Static implementation review against requirement IDs.
- Syntax check: extracted script validated with `node --check`.
- In-file evidence references included per requirement (feature IDs, controls, or logic blocks).

## Results

| ID | Status | Verification Notes |
|---|---|---|
| R1 | PASS | Browser local app: single HTML with inline CSS/JS. |
| R2 | PASS | Single-file SPA at `index.html`. |
| R3 | PASS | SVG 2D scene only, no 3D libraries. |
| R4 | PASS | Dark theme + sidebar controls implemented. |
| R5 | PASS | SVG-based scene and packet rendering implemented. |
| R6 | PASS | Speed slider min `0.25`, max `4`, step `0.25`. |
| R7 | PASS | One-way base `sim.oneWayMs = 5000`. |
| R8 | PASS | ACK path uses same one-way duration (total RTT 10s at 1x). |
| R9 | PASS | Timers are visual/simulated, not OS/stack-real. |
| R10 | PASS | Send budget based on `min(cwnd,rwnd)-flight`. |
| R11 | PASS | Sender backlog health bar (`senderQueueBar`) present. |
| R12 | PASS | Send loop continues while queue non-zero and constraints allow. |
| R13 | PASS | Payload byte slider (`payload`) controls queue source. |
| R14 | PASS | Default payload 32KB (`32768`). |
| R15 | PASS | Slider range `1024..10485760`, step `1024`. |
| R16 | PASS | MSS input explicit (`mss`). |
| R17 | PASS | MSS default set to `1460`. |
| R18 | PASS | Segment sizing uses effective MSS and final partial packet. |
| R19 | PASS | `effectiveMss()` enforces MTU-aware payload reduction. |
| R20 | PASS | Handshake animation path: SYN -> SYN-ACK -> ACK. |
| R21 | PASS | RTO timer bar (`rtoBar`) + timeout retransmit logic. |
| R22 | PASS | 3 dupACK triggers fast retransmit / recovery updates. |
| R23 | PASS | rwnd bar and sender flight clamp by `min(cwnd,rwnd)`. |
| R24 | PASS | Slow-start + congestion-avoidance teaching model. |
| R25 | PASS | Initial cwnd default 2 MSS, user adjustable. |
| R26 | PASS | ssthresh default `inf` until loss unless user set otherwise. |
| R27 | PASS | ACK-every-segment default enabled. |
| R28 | PASS | Nagle toggle blocks small writes with unacked data in flight. |
| R29 | PASS | Keep-alive toggle with accelerated periodic probes. |
| R30 | PASS | Zero-window persist probes with exponential backoff. |
| R31 | PASS | FIN/ACK close flow + TIME-WAIT countdown bar. |
| R32 | PASS | Delayed ACK control exists and defaults off. |
| R33 | PASS | Freeform comma-list inputs for drop/reorder/dup/corrupt. |
| R34 | PASS | No hard cap in parser or storage for fault indexes. |
| R35 | PASS | ACK packet visuals include SACK block text when present. |
| R36 | PASS | Sidebar SACK scoreboard panel implemented. |
| R37 | PASS | In-flight PDU rendered as composite box of smaller header boxes. |
| R38 | PASS | Header mini-boxes include field labels and values. |
| R39 | PASS | Nested Ethernet/IP/TCP shown; collapsible via show toggles. |
| R40 | PASS | Header and payload widths proportionally derived from byte sizes. |
| R41 | PASS | Ethernet II only (no VLAN logic/UI). |
| R42 | PASS | IPv4/IPv6 toggle included and used in packet construction. |
| R43 | PASS | TCP fields shown: src/dst, seq/ack, offset, flags, window, checksum, urgent ptr, options. |
| R44 | PASS | IP field set kept minimal per clarified requirement. |
| R45 | PASS | Checksums are computed, not placeholders. |
| R46 | PASS | TCP checksum includes pseudo-header (v4/v6 aware). |
| R47 | PASS | IPv4 header checksum computed and displayed. |
| R48 | PASS | Ethernet FCS (CRC32) computed and displayed. |
| R49 | PASS | Math panel includes running checksum steps and byte-oriented notes. |
| R50 | PASS | TCP options are toggleable. |
| R51 | PASS | MSS, SACK Permitted, Window Scale, Timestamps toggles present. |
| R52 | PASS | Keyboard controls: space, arrows, plus/minus implemented. |
| R53 | PASS | Tooltips present on major controls and high-contrast palette used. |
| R54 | PASS | No PNG/GIF export feature included. |

## Additional Checks

- JavaScript syntax check:
  - Command used: `awk '/<script>/{flag=1;next}/<\\/script>/{flag=0}flag' index.html > /tmp/tcpviz.js && node --check /tmp/tcpviz.js`
  - Result: pass (no syntax errors).

