# ☎️ Asterisk Engineering

A complete, hands-on journey through **Asterisk** — from compiling it from source to building a production-style PBX with IVR, queues, voicemail, SIP trunking, NAT traversal, CDR, and AMI automation. This repository documents that journey as a structured set of tutorials, followed by a real-world project that ties everything together.

---

## 📖 About This Repository

This repo is a personal, in-depth study log covering everything needed to go from "what is Asterisk?" to confidently designing, deploying, and debugging a real PBX system. It's organized into **6 progressive modules**, each building on the last, followed by a capstone project that applies the concepts end-to-end.

Whether you're new to telephony or brushing up on Asterisk internals, the modules are written to be followed in order — each one assumes the knowledge from the previous.

---

## 🗂️ Modules

| # | Module | Focus |
|---|--------|-------|
| 01 | [Asterisk Fundamentals & PBX Setup](./01-asterisk-fundamentals-and-pbx-setup) | Installing, compiling, and configuring Asterisk; core concepts; first dialplan and incoming call |
| 02 | [Dialplan, Voicemail & Call Routing](./02-dialplan-voicemail-and-call-routing) | Pattern matching, variables, time-based routing, subroutines, voicemail integration |
| 03 | [Call Queues, Database & IVR](./03-call-queues-database-and-ivr) | Queues, agent management, Music on Hold, AstDB, custom prompts, IVR design |
| 04 | [Advanced IVR, SIP & Debugging](./04-advanced-ivr-sip-and-debugging) | Advanced IVR logic, the SIP protocol, and real-world debugging with tcpdump/Wireshark |
| 05 | [SIP Providers, NAT & Logging](./05-sip-providers-nat-and-logging) | Connecting to real SIP trunk providers, inbound/outbound calls, Caller ID, NAT, logging |
| 06 | [CDR, Call Files & AMI](./06-cdr-call-files-and-ami) | Call Detail Records, Call Files, and automating Asterisk with the Manager Interface (AMI) |

Each module folder has its own README with a detailed tutorial index.

---

## 🏗️ Capstone Project

### [Small Office PBX System — Call Flow Project](./projects/pbx-call-routing-asterisk)

A fully functional PBX built for a simulated small company with three departments (**Sales, Support, Admin**), applying the concepts learned across all 6 modules into one working system.

**Highlights:**
- Time-based call routing (`GotoIfTime`) — business hours vs. after-hours voicemail
- Interactive DTMF-driven IVR menu for department selection
- Department-based call distribution with `GoSub` subroutines and argument passing
- Voicemail fallback on no-answer/busy using `${DIALSTATUS}`
- Pattern-matched internal dialing (`_1XX`) across all extensions
- Custom audio prompts for a professional caller experience

👉 See the [project README](./projects/pbx-call-routing-asterisk/README.md) for full architecture, configuration details, and testing results.

---

## 🎓 Skills Covered Across This Repository

- ✅ Compiling and installing Asterisk from source
- ✅ Core Asterisk architecture — extensions, peers, trunks, contexts
- ✅ Dialplan design — pattern matching, variables, subroutines, time-based logic
- ✅ Voicemail configuration and dialplan integration
- ✅ Call queues, agent management, and Music on Hold
- ✅ The Asterisk database (AstDB)
- ✅ IVR design — from basics to error handling and best practices
- ✅ The SIP protocol, message flow, and codecs
- ✅ Real-world debugging with `tcpdump` and Wireshark (SIP + RTP analysis)
- ✅ Connecting to and troubleshooting real SIP trunk providers
- ✅ NAT traversal and SIP keep-alive
- ✅ CDR (Call Detail Records) and Call Files
- ✅ Automating Asterisk via the AMI (Asterisk Manager Interface)
- ✅ Applying all of the above in a complete, working PBX project

---

## 🧰 Requirements

- Asterisk 18.x or later (recommended)
- A Linux server or VM (Ubuntu/Debian/CentOS)
- A softphone client for testing (e.g., Zoiper, Linphone, MicroSIP)
- `tcpdump` and [Wireshark](https://www.wireshark.org/) for the debugging modules
- Basic familiarity with the Linux command line

---

## 🚀 Getting Started

```bash
git clone https://github.com/saadcnx/asterisk-engineering.git
cd asterisk-engineering
```

Start with [Module 01](./01-asterisk-fundamentals-and-pbx-setup) and work through the modules in order. Once you've completed all 6, check out the [capstone project](./projects/pbx-call-routing-asterisk) to see everything applied together.

---

## 🙏 Credits & Acknowledgements

Special thanks to **[pascom GmbH & Co. KG](https://www.pascom.net/)**, whose lectures on Asterisk and technical resources served as a valuable reference while researching and writing several of these notes.

---

## 🤝 Contributing

Found an issue, typo, or have a suggestion? Feel free to open an [issue](https://github.com/saadcnx/asterisk-engineering/issues) or submit a pull request.

## 📄 License

This project is shared for educational and portfolio purposes. Feel free to fork and adapt it for your own learning.

---

<p align="center">Made with ☎️ and ☕ by <a href="https://github.com/saadcnx">saadcnx</a></p>
