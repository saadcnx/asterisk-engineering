# Wireshark Part 4: RTP Statistics & Audio Debugging

---

## The Mystery of the Third INVITE (Answered)

A viewer noticed something in our last capture: there were **three INVITEs**, not just two.

1. **First INVITE** — no authentication.
2. **Second INVITE** — with authentication.
3. **Third INVITE** — we didn't explain.

### The Answer

It's a quirk of our test environment. Our testing machine has multiple network interfaces and multiple IP addresses. During the call, the softphone decided to change its source IP address (from `192.168.100.x` to `10.10.1.x`). It sent a new INVITE to inform Asterisk of the IP change.

This is normal behavior — a phone can send a new INVITE (a **re-INVITE** or contact update) to update its contact IP address, and it just works. In a normal production environment without multiple virtual interfaces, this won't happen.

> **Note:** If you see unexpected re-INVITEs in production, check for network interface changes, VPN toggles, or NAT rebinding on the endpoint.

---

## Viewing Combined Call Flow (Multiple Call Legs)

You can analyze more than one call leg at once in Wireshark.

### How to Combine Flows

1. Go to **Telephony → VoIP Calls**.
2. Select multiple call legs (**Ctrl+Click**).
3. Click **Flow**.

Wireshark merges the flows into a single diagram showing the complete end-to-end SIP conversation. Here you can clearly see all four RTP streams (two per call leg), and the IP address change in our test environment becomes very obvious.

---

## Analyzing RTP Streams

Go to **Telephony → RTP → RTP Streams**.

### What You See

Instead of the expected 4 streams (2 call legs × 2 directions), we saw **6** — again due to the IP address change during the call (the softphone started sending on one IP, then switched to another).

### RTP Stream Metrics

| Metric | Meaning |
|--------|---------|
| **Packets** | Total RTP packets in the stream. |
| **Lost** | Number of packets lost (0 in our test — local machine, no loss). |
| **Max Delta** | Maximum time gap between packets. |
| **Max Jitter** | Maximum jitter (variation in packet arrival time). |
| **Mean Jitter** | Average jitter. |

> **Tip:** Wireshark calculates packet loss from RTP sequence numbers. Out-of-order packets may appear differently depending on Wireshark's reassembly settings.

---

## The Golden Rule: Audio Quality Comes First

Before diving deep into statistics, do this:

1. **Listen to the audio.** Use the **Play Streams** feature from the last tutorial.
2. If it sounds good, the numbers don't matter. The audio is good.
3. If it sounds bad, **THEN** use the statistics to find out **WHY**.

---

## Common Audio Problems & How to Recognize Them

### 1. Packet Loss

The most common problem.

- Listen to the audio to learn what packet loss sounds like (**gaps, choppy audio, missing words**).
- Then compare what you hear to the data.
- Once you know the sound of packet loss, you can diagnose it by ear in future calls.

### 2. Jitter

Variation in packet arrival time. High jitter = packets arriving inconsistently = poor audio.

- Wireshark shows **Max Jitter** and **Mean Jitter** in milliseconds.
- **Rough thresholds:**
  - `< 30 ms` — Generally acceptable.
  - `30–100 ms` — May cause noticeable quality issues.
  - `> 150 ms` — Severe audio degradation likely.

### 3. Latency (High Delay)

- One-way latency > 150 ms becomes noticeable to users.
- > 300 ms makes conversation feel unnatural (people talk over each other).
- Use `ping` or RTCP reports to measure round-trip time.

---

## The RTP Graph (Visual Analysis)

A powerful feature: Wireshark can generate a visual graph of the RTP stream.

### How to Access

1. In the **RTP Streams** window, select a stream.
2. Click **Analyze → Graph**.

### What the Graph Shows

- A timeline of the RTP stream.
- Peaks and dips in jitter, delta, and other metrics.
- If there's a sudden spike/peak — that's where the problem occurred.
- You can zoom into that exact millisecond to see what happened.

### Why This is Powerful

This level of analysis is completely impossible in the Asterisk CLI. `sip set debug on` cannot give you jitter graphs or packet loss statistics. This is why Wireshark is the professional tool for SIP debugging.

---

## Limitation: No Live View

The disadvantage of this approach (`tcpdump → copy file → Wireshark`) is that it's not live.

> **Alternative for live SIP analysis:** Tools like **`sngrep`** or **`ngrep`** can show live SIP traffic directly on the server without capturing to a file. They're lightweight and ideal for quick checks.

There are other tools that can provide live RTP analysis, but they're beyond the scope of these tutorials for now.

---

## Important: SRTP & Encrypted Calls

If your deployment uses **SRTP** (encrypted RTP), Wireshark will **not** be able to decode or play the audio unless you provide the encryption keys (via the *Edit → Preferences → Protocols → RTP → SRTP* settings). In most production environments, SRTP keys are exchanged via SDES in the SIP signaling or via DTLS-SRTP — capturing the SIP + RTP together is essential if you need to decrypt later.

> **Security reminder:** If you are capturing production traffic with SRTP disabled, treat the `.pcap` file as highly sensitive — it contains actual call audio.

---

## Summary: The Professional Debugging Stack

| Step | Tool | Purpose |
|------|------|---------|
| 1. Capture | `tcpdump -i eth0 -s 0 -w file.pcap` | Capture full traffic on the server. |
| 2. Transfer | `scp` | Copy to local machine. |
| 3. Analyze SIP | Wireshark **VoIP Calls → Flow** | See the complete SIP conversation. |
| 4. Analyze RTP | Wireshark **RTP Streams → Graph** | Find jitter, packet loss, IP issues. |
| 5. Listen | Wireshark **Play Streams** | Actually hear the audio quality. |

This is a solid introduction to professional SIP debugging. Next, we move on to connecting to real SIP providers.
