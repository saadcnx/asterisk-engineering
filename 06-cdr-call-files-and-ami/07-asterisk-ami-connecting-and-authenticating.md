# AMI: Connecting & Authenticating (Telnet Demo)

## The Moment of Truth

Last time, we configured AMI. Now, we actually test it by connecting via Telnet.

**⚠️ Reminder:** Telnet is unencrypted. We're doing this for learning on a local test system. Do NOT use Telnet in production. We are not responsible if you compromise your system.

---

## Step 1: Connecting to AMI

Connect to the Asterisk server on the AMI port:

```bash
telnet 192.168.100.55 5038
```

**Response:**

```
Asterisk Call Manager/1.3
```

The AMI says hello. The connection is open. Now we can talk to it.

**Important Syntax Rule:** After every command, you must press Enter twice — once to finish the last line, and again to send an empty line. The empty line signals "this command is complete." If you don't send the empty line, nothing happens, and the connection eventually times out.

If you just press Enter without a command:

```
Response: Error
Message: Missing action in request
```

---

## Step 2: The Two Security Layers

Even though AMI is bound to `0.0.0.0` (all interfaces), there are two security layers:

1. **Connection Layer:** Can you reach the port? Controlled by `bindaddr` and your firewall.
2. **Authentication Layer:** Can you log in? Controlled by the user's permit/deny rules.

The permit/deny rules are checked at login time, not at connection time. You can connect from any IP (if the port is reachable), but you can only authenticate from permitted IPs.

---

## Step 3: Testing the ACL (IP Restriction)

**Attempt 1: Connect via localhost (wrong network)**

```bash
telnet 127.0.0.1 5038
```

Then authenticate:

```
Action: Login
Username: matthias
Secret: 12345678
```

**Result:**

```
Response: Error
Message: Authentication failed
```

This fails because `127.0.0.1` is in the `127.0.0.0/8` network — NOT in the permitted `192.168.100.0/24` network. The ACL works!

---

## Step 4: Successful Authentication

**Attempt 2: Connect via the correct network IP**

```bash
telnet 192.168.100.55 5038
```

Authenticate:

```
Action: Login
Username: matthias
Secret: 12345678
```

**Result:**

```
Response: Success
Message: Authentication accepted
```

We are now logged in and can send commands.

---

## Step 5: The Event Stream

Once authenticated, AMI immediately starts sending events — everything happening on the system.

Make a test call from a phone, and you'll see events like:

- `Newstate` — call state changes.
- `Newchannel` — a new channel is created.
- `Dial` — a dial is happening.
- `RTP` — RTP audio stream details.
- `Hangup` — call ended.

This is much more detailed than the Asterisk CLI. You see every single step, every state change, every piece of metadata.

---

## CLI vs. AMI for Monitoring

| Aspect | Asterisk CLI | AMI |
|---|---|---|
| Readability | Colored, human-friendly. | Raw text, more verbose. |
| Information | Shows dialplan execution. | Shows EVERYTHING — channels, RTP, events, metadata. |
| Heavy Load | Hard for humans to follow. | Impossible for humans to follow directly. |
| Automation | Not designed for automation. | Designed exactly for software to parse and react. |

**The Real Use Case:**

You don't sit and read AMI events manually. You write a program (using an AMI library for your language) that listens for specific events and reacts automatically. Example: "When a call is answered, log it to the database" or "When a queue has more than 5 waiting callers, send an alert."

---

## Summary: Basic AMI Commands So Far

| Action | Purpose |
|---|---|
| Login | Authenticate with username and secret. |
| Logoff | Disconnect. |
| (Events) | Automatically streamed after login. |

---

## Next Steps

We've connected, authenticated, and seen the event stream. Next time, we'll send our first real command (not just login) — like originating a call or querying system status. And we'll cover more about securing AMI properly (voting dependent on IdeaScale).
