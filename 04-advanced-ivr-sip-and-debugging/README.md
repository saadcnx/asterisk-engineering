# 🐛 04 — Advanced IVR, SIP & Debugging

> Part of the [**Asterisk Telephony Notes**](https://github.com/saadcnx/asterisk-telephony-notes) series — a hands-on, from-scratch guide to building, understanding, and running an Asterisk PBX.

This module builds on [Module 03](../03-call-queues-database-and-ivr) and goes deeper into IVR error handling, the SIP protocol itself, and real-world debugging with `tcpdump` and Wireshark — the skills you need to actually troubleshoot a production PBX.

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- Build a basic IVR menu from scratch
- Handle invalid input and timeouts gracefully in an IVR
- Add loop counts and repeat-menu logic to your IVR
- Understand the SIP protocol and how it works
- Follow SIP message flow, including re-INVITEs
- Understand codecs and get started with SIP debugging
- Capture SIP traffic with `tcpdump`
- Analyze SIP call flows in Wireshark
- Analyze RTP streams in Wireshark and disable re-INVITE for direct media
- Read RTP statistics — jitter, packet loss, and call quality metrics
- Understand direct media / re-INVITE behavior and signaling vs. media at a conceptual level

---

## 🗂️ Tutorials in This Module

| # | Tutorial | Description |
|---|----------|--------------|
| 01 | [IVR — Basic Setup](./01-asterisk-ivr-basic-setup.md) | Building a basic IVR menu from scratch |
| 02 | [IVR Error Handling — Invalid Input & Timeout](./02-asterisk-ivr-error-handling-invalid-timeout.md) | Gracefully handling invalid input and timeouts in an IVR |
| 03 | [IVR Loop Count & Repeat Menu](./03-asterisk-ivr-loop-count-repeat-menu.md) | Adding loop limits and repeat-menu behavior |
| 04 | [SIP Protocol — Introduction](./04-asterisk-sip-protocol-introduction.md) | A practical introduction to the SIP protocol |
| 05 | [SIP Message Flow & Re-INVITE](./05-asterisk-sip-message-flow-and-reinvite.md) | Understanding SIP message flow, including re-INVITEs |
| 06 | [Codecs & SIP Debugging — Intro](./06-asterisk-codecs-and-sip-debugging-intro.md) | Codec basics and getting started with SIP debugging |
| 07 | [SIP Debugging — `tcpdump` Capture](./07-asterisk-sip-debugging-tcpdump-capture.md) | Capturing SIP traffic on the wire with `tcpdump` |
| 08 | [Wireshark — SIP Call Flow Analysis](./08-asterisk-wireshark-sip-call-flow-analysis.md) | Analyzing SIP call flows visually in Wireshark |
| 09 | [Wireshark — RTP Analysis & Disabling Re-INVITE](./09-asterisk-wireshark-rtp-analysis-disable-reinvite.md) | Analyzing RTP streams and disabling re-INVITE for direct media |
| 10 | [Wireshark — RTP Statistics: Jitter & Packet Loss](./10-asterisk-wireshark-rtp-statistics-jitter-packet-loss.md) | Reading RTP statistics to diagnose jitter and packet loss |

---

## 📎 Bonus Notes

Supplementary reference notes that support the tutorials above:

| Note | Description |
|------|--------------|
| [Direct Media & Re-INVITE Explained](./asterisk-direct-media-reinvite-explained.md) | A focused explainer on direct media and how re-INVITE works |
| [Signaling vs. Media — Simple Explanation](./asterisk-signaling-vs-media-simple-explanation.md) | A beginner-friendly breakdown of signaling vs. media in VoIP |

---

## 🧰 Prerequisites

- Completion of [Module 03 — Call Queues, Database & IVR](../03-call-queues-database-and-ivr)
- A working Asterisk installation with at least one registered SIP peer
- `tcpdump` and [Wireshark](https://www.wireshark.org/) installed for the debugging tutorials
- Basic familiarity with networking concepts (IP, ports, packet capture)

---

## 🚀 Getting Started

Clone the repository and navigate to this module:

```bash
git clone https://github.com/saadcnx/asterisk-telephony-notes.git
cd asterisk-telephony-notes/04-advanced-ivr-sip-and-debugging
```

Work through the tutorials in numeric order — starting with IVR fundamentals and ending with RTP-level debugging in Wireshark — then check the bonus notes for extra conceptual clarity on media/signaling.

---

## 🗺️ Series Roadmap

This is Module 4 of the **Asterisk Telephony Notes** series. Planned future modules include:

- Trunking and PSTN/SIP provider integration
- AMI (Asterisk Manager Interface) & AGI scripting
- Advanced call routing and failover strategies

---

## 🤝 Contributing

Found an issue, typo, or have a suggestion? Feel free to open an [issue](https://github.com/saadcnx/asterisk-telephony-notes/issues) or submit a pull request.

## 📄 License

This project is shared for educational purposes. Check the main repository for license details.

---

<p align="center">Made with 🐛 and ☕ by <a href="https://github.com/saadcnx">saadcnx</a></p>
