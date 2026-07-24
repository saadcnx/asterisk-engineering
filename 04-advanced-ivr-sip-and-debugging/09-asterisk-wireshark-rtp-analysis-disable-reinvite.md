# Wireshark Part 3: Disabling Re-INVITE & Analyzing RTP

## The Goal

Previously, Asterisk's default **re-INVITE** behavior caused RTP (audio) to bypass the server, preventing complete RTP analysis.

The goal is to disable re-INVITE so all RTP flows through Asterisk.

---

# Step 1: Disable Re-INVITE

Edit `sip.conf`.

## Global

```ini
[general]
canreinvite=no
```

## Per Peer

```ini
[james]
canreinvite=no
```

### Recommendation

Set `canreinvite=no` globally and enable it only for peers that require direct media.

### Allow Re-INVITE When

- Phones are on the same LAN.
- Lower latency is desired.
- Saving server bandwidth is important.

### Disable Re-INVITE When

- Call recording is required.
- IVR requires DTMF detection.
- RTP debugging is needed.
- Contact-center calls must pass through Asterisk.

Reload SIP:

```bash
sip reload
```

---

# Step 2: Capture Traffic

```bash
tcpdump -i eth0 -s 0 -w test.pcap
```

Workflow:

1. Start capture.
2. Make a call.
3. Talk.
4. Hang up.
5. Stop capture.

With re-INVITE disabled, the capture contains all RTP packets.

Example:

```text
~710 packets
```

---

# Step 3: Analyze in Wireshark

Open the PCAP.

Go to:

```text
Telephony → VoIP Calls
```

The SIP call legs are displayed.

> The packet count shown there is for SIP signaling, not RTP.

## Flow Diagram

The signaling remains:

```text
INVITE
 ↓
401 Unauthorized
 ↓
INVITE (Authentication)
 ↓
100 Trying
 ↓
180 Ringing
 ↓
200 OK
 ↓
ACK
```

After `ACK`, a continuous RTP stream appears.

The call ends with:

```text
BYE
 ↓
200 OK
```

---

# Step 4: Play RTP Audio

Open:

```text
Telephony → VoIP Calls
```

Then:

1. Select a call.
2. Click **Play Streams**.
3. Select an RTP stream.
4. Click **Play**.

Wireshark decodes and plays the conversation.

---

# Security Warning

If RTP is not encrypted, anyone with access to the capture can:

- Capture packets
- Decode RTP
- Listen to calls

This is one reason secure deployments use SRTP.

---

# Step 5: RTP Statistics

Available information includes:

- Call duration
- Jitter
- Packet loss
- Buffer information

Detailed RTP statistics:

```text
Telephony → RTP → RTP Streams
```

---

# Re-INVITE Comparison

| Feature | Re-INVITE ON | Re-INVITE OFF |
|---|---|---|
| RTP in capture | Very little | Hundreds of packets |
| Audio playback | Not possible | Available |
| RTP statistics | Not available | Available |
| Server load | Lower | Higher |
| Call recording | Not possible | Available |

---

# Summary

Disable:

```ini
canreinvite=no
```

Reload:

```bash
sip reload
```

Capture:

```bash
tcpdump -i eth0 -s 0 -w test.pcap
```

Then analyze:

- SIP
- RTP
- Audio playback
- Jitter
- Packet loss

---

# Next Step

The next tutorial explores RTP statistics in greater detail.
