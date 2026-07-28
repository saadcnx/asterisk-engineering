# Asterisk Manager Interface (AMI): Introduction

## What is AMI?

The easiest explanation: It's a remote control for Asterisk. Just like a remote control for your TV, AMI lets you remotely control your Asterisk server.

---

## Call Files vs. AMI: Why AMI is Better

We previously covered call files (put a file in a directory, Asterisk does something). Call files work, but have major drawbacks compared to AMI.

| Call Files | AMI |
|---|---|
| Fire and Forget — You put the file there and don't know what happens. | Real-time — You can read and write every event as it happens. |
| No Process Control — You can't monitor or react during the call. | Full Control — You see every event and can react immediately. |
| File Transfer Required — You must SCP/copy files to the server, requiring SSH certificates and access. | Network Connection — Connect via TCP/IP. No file transfer needed. |
| No State Reading — You can't query what Asterisk is doing. | Read State — You can see channels, calls, queues in real-time. |
| No Event Reaction — You can't trigger actions based on events (e.g., "when James calls, ring a bell, log to database, increment a counter"). | Event-Driven — You can react to every single event (call started, answered, hung up, etc.). |

---

## How AMI Works

- **TCP/IP Connection:** AMI listens on a TCP port (default: 5038). By default, it's disabled for security reasons.
- **Authentication:** You log in with a username and password.
- **Bidirectional Communication:**
  - You send Actions (commands) to Asterisk.
  - Asterisk sends Events (what's happening) to you.
- **Event Stream:** Every single thing happening on Asterisk generates events — calls, registrations, queue activity, etc. There are a LOT of events.

---

## Security Warning ⚠️

- AMI is dangerous if configured incorrectly.
- By default, AMI is deactivated (which is a good thing).
- If enabled without proper security, anyone could remotely control your Asterisk server.
- You CAN make it secure. We'll show how.

**For Learning/Demo:**

- We'll use Telnet (unencrypted) to demonstrate the raw protocol. It's easy to understand.
- NEVER use Telnet in production. Use encrypted connections (TLS/SSL).

**For Real Applications:**

- You don't communicate with AMI directly by typing commands.
- You use a program or script that talks to AMI.
- Pre-built libraries exist for almost every programming language (Python, PHP, Java, Node.js, etc.) that abstract away the raw protocol. You don't need to know every command or event — the library handles it.

---

## Why Start from Scratch?

To truly understand AMI, we'll start with raw Telnet connections and see exactly what commands and events look like. Once you understand the basics, you can use any library with confidence.

---

## What We'll Cover Next (Practical)

1. Enabling AMI in `/etc/asterisk/manager.conf`.
2. Creating a manager user with appropriate permissions.
3. Connecting via Telnet.
4. Logging in.
5. Sending a command (Action).
6. Watching events stream in.

---

## Summary

| Aspect | Detail |
|---|---|
| What it is | A real-time remote control API for Asterisk. |
| Default Port | TCP 5038 (disabled by default). |
| Communication | Bidirectional: you send Actions, Asterisk sends Events. |
| Security | Disabled by default. Can be secured. Use TLS in production. |
| Libraries | Available for Python, PHP, Java, Node.js, and more. |
