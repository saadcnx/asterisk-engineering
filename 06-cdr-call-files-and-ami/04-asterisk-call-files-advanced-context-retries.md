# Call Files Part 2: Advanced Usage (Context, Retries & More)

## Correction from Last Time: Required Module

Call files require a specific Asterisk module: `pbx_spool.so`

- Good news: It's loaded by default in standard Asterisk installations.
- If you compiled Asterisk yourself: You might have excluded it from the build, or it might be on an exclusion list.

**How to check:**

```bash
asterisk -rvvv
module show like pbx_spool
```

Look for `pbx_spool.so`. If it's not there, call files won't work. Check your `/etc/asterisk/modules.conf` to ensure it's not excluded.

---

## Advanced Call File: Connecting to a Context (Instead of an Application)

Last time, we connected a channel directly to an application. But you can also connect a channel to a context — just like when a phone picks up and enters the dialplan. This is much more powerful because you can use all your existing dialplan logic (queues, IVRs, voicemail, etc.).

**Example Call File:**

```
Channel: SIP/james
Context: phones
Extension: 100
Priority: 1
MaxRetries: 2
RetryTime: 60
WaitTime: 30
```

**Fields Explained:**

| Field | Purpose | Example |
|---|---|---|
| Channel | The channel/peer to call first. | SIP/james |
| Context | The dialplan context to enter when the channel answers. | phones |
| Extension | The extension to dial within that context. | 100 |
| Priority | The priority to start at (usually 1). | 1 |
| MaxRetries | How many times to retry if the call fails. | 2 |
| RetryTime | Seconds to wait between retries. | 60 |
| WaitTime | Seconds to wait for the channel to answer before giving up. | 30 |

---

## How the Context Method Works

1. Asterisk calls `SIP/james`.
2. James answers.
3. Asterisk enters the `phones` context, finds extension `100`, starts at priority 1.
4. The dialplan executes exactly as if James had picked up his phone and dialed 100.

In our setup: James is connected to Matthias (because `exten => 100,1,Dial(SIP/matthias)`).

**Why This is Powerful:**

- You don't need to specify an application in the call file.
- All the logic is already in your dialplan (queues, IVRs, voicemail, time conditions).
- For batch calling: Create call files that send callers into a queue context. The queue handles distribution, hold music, and agent assignment.
- You can combine this with CDR customizations to track which batch a call belongs to.

---

## The Retry Mechanism

| Parameter | Description |
|---|---|
| MaxRetries | Number of retry attempts (e.g., 2 = try once, then retry once more = 2 total attempts). |
| RetryTime | How many seconds to wait after a failed attempt before retrying. |
| WaitTime | How many seconds to let the phone ring before considering it a failure. |

**Example:**

```
MaxRetries: 2
RetryTime: 300
WaitTime: 30
```

- Call James. Wait up to 30 seconds for answer.
- If no answer → wait 300 seconds (5 minutes) → try again.
- If still no answer → stop (2 total attempts).

**Advanced Spammer Tip (Don't Actually Do This for Spam):**

You can write scripts that reschedule call files with increasing retry intervals — first retry after 1 hour, then 2 hours, then 1 day. But don't use this for spam. It's for legitimate retry logic.

---

## Use Case: Alarm System with Contexts

**Create a dedicated context in extensions.conf:**

```ini
[alarm]
exten => s,1,Answer()
exten => s,n,Playback(server-xyz-is-down)
exten => s,n,Hangup()
```

**Create a call file:**

```
Channel: SIP/admin-phone
Context: alarm
Extension: s
Priority: 1
```

When triggered, Asterisk calls the admin, enters the alarm context, answers, and plays the alert message.

**Even Better:**

Create one context per server:

- `[alarm-server1]` → plays "Server 1 is down."
- `[alarm-server2]` → plays "Server 2 is down."

Monitoring system copies the appropriate call file based on which server failed.

---

## Summary: Two Methods Compared

| Method | Call File Specifies | Best For |
|---|---|---|
| Application | Application: + Data: | Simple: "Call X and play Y." |
| Context | Context: + Extension: + Priority: | Complex: use existing dialplan logic (queues, IVRs, conditions). |
