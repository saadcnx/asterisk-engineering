# 📊 06 — CDR, Call Files & AMI

> Part of the [**Asterisk Telephony Notes**](https://github.com/saadcnx/asterisk-telephony-notes) series — a hands-on, from-scratch guide to building, understanding, and running an Asterisk PBX.

This module builds on [Module 05](../05-sip-providers-nat-and-logging) and moves into automation and reporting — Call Detail Records (CDR) for call logging/billing, Call Files for programmatically originating calls, and the Asterisk Manager Interface (AMI) for controlling Asterisk from external applications.

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- Understand what CDR is and why it matters for reporting and billing
- Customize CDR fields to capture the data you actually need
- Understand and create basic Call Files to trigger calls
- Build advanced Call Files with custom contexts and retry logic
- Understand what AMI is and how it fits into Asterisk automation
- Configure AMI (`manager.conf`) securely
- Connect to and authenticate with the AMI
- Use AMI actions like `Originate` to trigger calls programmatically

---

## 🗂️ Tutorials in This Module

| # | Tutorial | Description |
|---|----------|--------------|
| 01 | [CDR — Introduction](./01-asterisk-cdr-call-detail-records-introduction.md) | What Call Detail Records are and why they matter |
| 02 | [CDR Fields & Customization](./02-asterisk-cdr-fields-and-customization.md) | Customizing CDR fields for your reporting/billing needs |
| 03 | [Call Files — Introduction](./03-asterisk-call-files-introduction.md) | Basics of Call Files and triggering calls without a dialplan action |
| 04 | [Call Files — Advanced Context & Retries](./04-asterisk-call-files-advanced-context-retries.md) | Advanced Call Files with custom contexts and retry logic |
| 05 | [AMI — Introduction](./05-asterisk-ami-introduction.md) | What the Asterisk Manager Interface is and what it's used for |
| 06 | [AMI — Configuration & Setup](./06-asterisk-ami-configuration-setup.md) | Setting up `manager.conf` securely |
| 07 | [AMI — Connecting & Authenticating](./07-asterisk-ami-connecting-and-authenticating.md) | Connecting to the AMI and authenticating a session |
| 08 | [AMI Actions — `Originate`](./08-asterisk-ami-actions-originate.md) | Using the `Originate` action to trigger calls via AMI |

---

## 🧰 Prerequisites

- Completion of [Module 05 — SIP Providers, NAT & Logging](../05-sip-providers-nat-and-logging)
- A working Asterisk installation with at least one registered SIP peer
- Basic familiarity with the command line and a scripting/programming language (for AMI examples)

---

## 🚀 Getting Started

Clone the repository and navigate to this module:

```bash
git clone https://github.com/saadcnx/asterisk-telephony-notes.git
cd asterisk-telephony-notes/06-cdr-call-files-and-ami
```

Work through the tutorials in numeric order — starting with CDR fundamentals and ending with triggering calls via AMI — to understand how Asterisk logs, automates, and exposes control over calls.

---

## 🗺️ Series Roadmap

This is Module 6 of the **Asterisk Telephony Notes** series. Planned future modules include:

- AGI (Asterisk Gateway Interface) scripting
- Advanced call routing and failover strategies
- Security hardening and fraud prevention

---

## 🤝 Contributing

Found an issue, typo, or have a suggestion? Feel free to open an [issue](https://github.com/saadcnx/asterisk-telephony-notes/issues) or submit a pull request.

## 📄 License

This project is shared for educational purposes. Check the main repository for license details.

---

<p align="center">Made with 📊 and ☕ by <a href="https://github.com/saadcnx">saadcnx</a></p>
