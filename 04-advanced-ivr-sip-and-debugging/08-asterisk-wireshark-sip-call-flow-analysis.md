# Wireshark: SIP Call Flow Analysis

## Last Time's Mystery

Previously, the packet capture file was smaller than expected.

The reason becomes clear after opening it in Wireshark.

It is related to:

```text
Re-INVITE
```

and the media path used during the call.

---

# Opening the Capture

Copy the `.pcap` file from the Asterisk server to the local computer using SCP or a graphical file-transfer tool.

Open Wireshark and use:

```text
File → Open
```
<img width="1007" height="662" alt="image" src="https://github.com/user-attachments/assets/61896170-ecdd-44fe-b540-e7c4e5c7a12f" />
<img width="995" height="660" alt="image" src="https://github.com/user-attachments/assets/e42b89d7-207b-471d-bf25-4d93429e19cf" />

Do not start a live capture on the local computer because the SIP traffic occurred on the server.

---

# Wireshark Interface Overview

Wireshark is a general network protocol analyzer with useful telephony tools.

The capture may include:

- SSH
- TCP
- MDNS
- ICMP
- SIP
- SDP
- RTP

On a busy server, manually checking every packet is not practical.

---
<img width="993" height="659" alt="image" src="https://github.com/user-attachments/assets/192952cb-bbc9-4bfe-b734-90fca6dc27f3" />

# VoIP Calls

Open:

```text
Telephony → VoIP Calls
```
<img width="985" height="662" alt="image" src="https://github.com/user-attachments/assets/77205650-b87c-4d6a-a9b1-8894e97e2e79" />

Wireshark analyzes the capture and displays the calls it finds.

This is one of the best starting points for SIP analysis.

---

# Why One Call Appears as Two Calls

Asterisk normally creates two call legs:

```text
Phone A → Asterisk
Asterisk → Phone B
```
<img width="990" height="660" alt="image" src="https://github.com/user-attachments/assets/9f6203a1-2959-4f6f-a9ba-3f82dd006b5b" />

Each leg may appear as a separate line.

One leg may contain more packets because the initiating side can generate additional signaling.

---

# The Flow Diagram

Select a call and click:

```text
Flow
```
<img width="1307" height="455" alt="image" src="https://github.com/user-attachments/assets/4bba936f-41b2-47be-96d6-a4c60c3c38d8" />

Wireshark creates a visual diagram of the SIP conversation.

Clicking a message in the diagram:

1. Highlights the matching packet.
2. Shows it in the main capture window.
3. Displays full packet details below.

This is useful when several calls exist in the same capture.

---

# Reading the SIP Messages

## First `INVITE`

Example:

```text
INVITE sip:200@PBX_IP
```

Meaning:

```text
Phone A wants to call extension 200.
```

## `401 Unauthorized`

Asterisk responds with:

```text
401 Unauthorized
```

This is an authentication challenge.

## Second `INVITE`

Phone A sends another `INVITE` with an:

```text
Authorization
```

header.

It contains authentication details such as a realm and password hash rather than the plain-text password.

The message may also contain SDP.

---

# SDP Information

SDP describes:

- Supported codecs
- Media IP address
- RTP port
- Session information

Example codec preferences may include:

```text
Speex
iLBC
```

---

# Normal Call Responses

```text
100 Trying
180 Ringing
200 OK
```

## `100 Trying`

The PBX is processing the request.

## `180 Ringing`

The destination phone is ringing.

## `200 OK`

The destination answered the call.

---

# The Re-INVITE Discovery

Normally, many RTP packets should appear after the call is answered.

However, this capture contains very little RTP.

After the call was established, Asterisk sent new `INVITE` messages to both phones.

These re-INVITEs told them to send RTP directly to each other.

The media path changed from:

```text
Phone A → Asterisk → Phone B
```

to:

```text
Phone A → Phone B
```

SIP signaling continued through Asterisk.

---

# Why the Capture Was Small

The capture was taken on the Asterisk server.

Once direct media began, most RTP packets bypassed the server.

Only one or two initial RTP packets may appear.

Therefore:

```text
Less RTP captured
        ↓
Smaller PCAP file
```

---

# Packet Structure

Selecting a packet shows the protocol layers.

| Layer | Protocol | Purpose |
|---|---|---|
| 1 | Frame | Ethernet frame information |
| 2 | IP | Internet Protocol addressing |
| 3 | UDP | Transport |
| 4 | SIP or SDP | Signaling and session description |

These layers add overhead beyond the raw media payload.

---

# Analysis Workflow

```text
Open the PCAP
        ↓
Telephony → VoIP Calls
        ↓
Select a call
        ↓
Open Flow
        ↓
Inspect INVITE and responses
        ↓
Review SDP
        ↓
Check RTP packets
        ↓
Identify re-INVITE or direct media
```

---

# Key Finding

```text
SIP signaling remained through Asterisk.
RTP media moved directly between the phones.
```

This explains why the server-side capture contained very little audio traffic.

---

# Next Step

To inspect complete RTP streams, direct media must be disabled so all audio passes through Asterisk.

Then:

1. Capture traffic again.
2. Open the new PCAP in Wireshark.
3. Inspect the full RTP stream.
4. Analyze the audio payload.
