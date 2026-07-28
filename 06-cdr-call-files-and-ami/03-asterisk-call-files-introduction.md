# Asterisk Call Files: Introduction & Basics

## What Are Asterisk Call Files?

As the name says: a file. You put something in it, copy it to the Asterisk spool directory, and Asterisk reads it and performs the instructions inside. It connects a channel to a target and executes applications — like placing an outbound call automatically.

---

## Common Use Cases

**1. Alarming / Monitoring (Most Common)**

- You have a network monitoring system (Nagios, Icinga, Zabbix, etc.).
- In case of an alarm, you want to call an administrator.
- Email alerts require many things to work: internet connection, DNS, mail server. A broken chain means no alert.
- With call files, you just SCP a file to the Asterisk server. If the server and phone line are up, the call goes through.
- Rock solid, simple technology. No API, no connection to maintain. Just a file.

**2. Batch Calling / Predictive Dialing**

- You want to call 1,000 people and connect them to agents.
- You generate call files in bulk. Asterisk processes them.

**3. Payment Reminders / Notifications**

- "You didn't pay your bill. We're calling to remind you."
- Integrate with your billing system to automatically generate reminder calls.

**4. Click-to-Dial (Less Common Now)**

- You click a button in a web app, and a call file is created to connect you to a customer.
- Note: The Asterisk AMI (Asterisk Manager Interface) is generally better for this because it provides direct API communication. But call files can still work.

**AMI vs. Call Files:**

- AMI gives you real-time, bidirectional communication with the server.
- Call Files are simpler and more reliable for fire-and-forget scenarios like alarming. No connection to maintain.

---

## The Call File Format

A minimal call file has three key-value pairs:

```
Channel: SIP/james
Application: Playback
Data: tt-monkeys
```

**Fields Explained:**

| Field | Purpose | Example |
|---|---|---|
| Channel | The channel/peer to connect to. Can be a local peer or a provider trunk. | SIP/james or SIP/provider/123456789 |
| Application | The Asterisk application to execute when the channel answers. | Playback, Dial, Queue |
| Data | Arguments/data passed to the application (NOT in brackets like the dialplan). | tt-monkeys |

**Important:**

- Channel connects first. Only if the channel answers does the application execute. If the channel doesn't answer, the call file mechanism stops (more on retry behavior in the next tutorial).
- You can use ANY Asterisk application and data, just like in the dialplan.
- To call a local peer: `SIP/james` (no number needed if it's a direct peer).
- To call an external number via a provider: `SIP/provider/49301234567`.

---

## Creating and Using a Call File

**The Spool Directory:**

`/var/spool/asterisk/outgoing/`

**⚠️ CRITICAL WARNING: Do NOT Edit Files Directly in the Spool Directory!**

- Some editors create the file immediately when you open it.
- Asterisk sees the empty file, reads it, executes nothing, and deletes the file.
- Your work is lost when you save.
- Always create/edit the file elsewhere, then COPY it to the spool directory. (Don't move — copy. This preserves the original for reuse/modification.)

**Step-by-Step:**

1. Create the call file in a working directory (e.g., `/root/`):

```bash
nano testcall.call
```

(The filename doesn't matter. No extension needed.)

2. Add the content:

```
Channel: SIP/james
Application: Playback
Data: tt-monkeys
```

3. Copy it to the spool directory:

```bash
cp testcall.call /var/spool/asterisk/outgoing/
```

4. Asterisk immediately processes it:
   - Calls the channel `SIP/james`.
   - When James answers, plays `tt-monkeys`.
   - Deletes the call file.

---

## Reference Resource

The examples used are from voip-info.org — a great reference source mentioned many times in this tutorial series. Search for "call files" there for more advanced examples.

---

## Next Steps

We've covered the basics. Next time, we'll go into more depth: additional parameters like CallerID, MaxRetries, RetryTime, WaitTime, and handling what happens when the channel doesn't answer.
