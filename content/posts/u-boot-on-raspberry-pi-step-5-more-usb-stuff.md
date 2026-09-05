---
title: 'U-Boot on Raspberry Pi, step 5: More USB stuff'
date: '2012-06-15T00:54:09'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

OK. No visual progress, but short SitRep: I got control transfers working more stable and got bulk transfer somewhat working: U-Boot sends one packet and receives response, then all bulk transfers end up in STALL state. Investigating. 

```
BOOTP broadcast 1
** smsc95xx_send(), len 342, buf 0x7e8db5e
BULK -> <3,2>
dev = 3 pipe = 2 buf = 07e8db5e size = 350 dir_out = 1
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 350, num_packets = 6, max = 64, buffer = 07e8db5e(07e8f490)
Tx: len = 350, actual = 350, err = 0
** smsc95xx_recv()
BULK -> <3,1>
dev = 3 pipe = 1 buf = 07fec698 size = 2048 dir_out = 0
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 2048, num_packets = 32, max = 64, buffer = 07fec698(07e8f490)
Rx: len = 2048, actual = 368, err = 0
packet received
packet received
Receive from protocol 0x800
Got IP
len=346, v=45
DHCPHandler: got packet: (src=67, dst=68, len=318) state: 3
Filtering pkt = 0
DHCPHandler: got DHCP packet: (src=67, dst=68, len=318) state: 3
DHCP: state=SELECTING bp_file: "kernel.RPI"
TRANSITIONING TO REQUESTING STATE
*** Unhandled DHCP Option in OFFER/ACK: 130
*** Unhandled DHCP Option in OFFER/ACK: 28
DhcpSendRequestPkt: Sending DHCPREQUEST
Transmitting DHCPREQUEST packet: len = 342
** smsc95xx_send(), len 342, buf 0x7e8dace
BULK -> <3,2>
dev = 3 pipe = 2 buf = 07e8dace size = 350 dir_out = 1
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 350, num_packets = 6, max = 64, buffer = 07e8dace(07e8f490)
Channel halted
Tx: len = 350, actual = 0, err = 0
BOOTP broadcast 2
** smsc95xx_send(), len 342, buf 0x7e8db5e
BULK -> <3,2>

dev = 3 pipe = 2 buf = 07e8dace size = 350 dir_out = 1
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 350, num_packets = 6, max = 64, buffer = 07e8dace(07e8f490)
Channel halted
Tx: len = 350, actual = 0, err = 0
BOOTP broadcast 2
** smsc95xx_send(), len 342, buf 0x7e8db5e
BULK -> <3,2>
dev = 3 pipe = 2 buf = 07e8db5e size = 350 dir_out = 1
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 350, num_packets = 6, max = 64, buffer = 07e8db5e(07e8f490)
Channel halted
Tx: len = 350, actual = 0, err = 0
** smsc95xx_recv()
BULK -> <3,1>
dev = 3 pipe = 1 buf = 07fec698 size = 2048 dir_out = 0
max_hc_xfer_size = 65535, max_hc_pkt_count = 511
xfer_len = 2048, num_packets = 32, max = 64, buffer = 07fec698(07e8f490)
HANG at line 533: 00000423
```
