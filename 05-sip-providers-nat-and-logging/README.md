# 🌐 05 — SIP Providers, NAT & Logging

> Part of the [**Asterisk Telephony Notes**](https://github.com/saadcnx/asterisk-telephony-notes) series — a hands-on, from-scratch guide to building, understanding, and running an Asterisk PBX.

This module builds on [Module 04](../04-advanced-ivr-sip-and-debugging) and takes your PBX from a lab setup to the real world — connecting to actual SIP trunk providers, handling inbound/outbound calls, setting Caller ID, solving NAT issues, and configuring proper logging.

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- Understand SIP provider types and what to consider when choosing one
- Register with a SIP provider from your Asterisk box
- Debug inbound calls from a SIP provider
- Configure SIP provider peers correctly
- Get inbound calls working end-to-end successfully
- Configure and place outbound calls through a SIP provider
- Set and manage Caller ID for outbound calls
- Understand NAT — why it breaks VoIP and how to fix it
- Use NAT + SIP keep-alive to maintain stable connections
- Configure Asterisk logging for troubleshooting and auditing

---

## 🗂️ Tutorials in This Module

| # | Tutorial | Description |
|---|----------|--------------|
| 01 | [SIP Providers — Types & Considerations](./01-asterisk-sip-providers-types-and-considerations.md) | Understanding different SIP provider types and what to look for |
| 02 | [SIP Provider Registration](./02-asterisk-sip-provider-registration.md) | Registering your Asterisk PBX with a SIP trunk provider |
| 03 | [SIP Provider Debugging — Inbound Calls](./03-asterisk-sip-provider-debugging-inbound-calls.md) | Troubleshooting inbound calls from your SIP provider |
| 04 | [SIP Provider Peer Configuration](./04-asterisk-sip-provider-peer-configuration.md) | Correctly configuring peer settings for your provider |
| 05 | [SIP Provider — Inbound Call Success](./05-asterisk-sip-provider-inbound-call-success.md) | Getting inbound calls working end-to-end |
| 06 | [SIP Provider — Outbound Calls](./06-asterisk-sip-provider-outbound-calls.md) | Configuring and placing outbound calls through your provider |
| 07 | [Caller ID for Outbound Calls](./07-asterisk-caller-id-outbound-calls.md) | Setting and managing Caller ID on outbound calls |
| 08 | [NAT Basics — Why & How](./08-asterisk-nat-basics-why-and-how.md) | Understanding NAT and why it causes VoIP issues |
| 09 | [NAT & SIP Keep-Alive](./09-asterisk-nat-and-sip-keep-alive.md) | Using SIP keep-alive to maintain stable NAT'd connections |
| 10 | [Logging Configuration](./10-asterisk-logging-configuration.md) | Configuring Asterisk logging for troubleshooting and audits |

---

## 🧰 Prerequisites

- Completion of [Module 04 — Advanced IVR, SIP & Debugging](../04-advanced-ivr-sip-and-debugging)
- A working Asterisk installation with at least one registered SIP peer
- An account with a SIP trunk provider (trial or production) to follow along
- Basic understanding of networking — public/private IPs, routers, and firewalls

---

## 🚀 Getting Started

Clone the repository and navigate to this module:

```bash
git clone https://github.com/saadcnx/asterisk-telephony-notes.git
cd asterisk-telephony-notes/05-sip-providers-nat-and-logging
```

Work through the tutorials in numeric order — starting with choosing a SIP provider and ending with logging configuration — to take your PBX from a lab environment to real-world, production-ready calling.

---

## 🗺️ Series Roadmap

This is Module 5 of the **Asterisk Telephony Notes** series. Planned future modules include:

- AMI (Asterisk Manager Interface) & AGI scripting
- Advanced call routing and failover strategies
- Security hardening and fraud prevention

---

## 🤝 Contributing

Found an issue, typo, or have a suggestion? Feel free to open an [issue](https://github.com/saadcnx/asterisk-telephony-notes/issues) or submit a pull request.

## 📄 License

This project is shared for educational purposes. Check the main repository for license details.

---

<p align="center">Made with 🌐 and ☕ by <a href="https://github.com/saadcnx">saadcnx</a></p>
