# SIP Provider: Inbound Call Success (Fixing the "Unauthorized" Error)

## Where We Left Off

We created the peer for our provider. Now we need two things to make inbound calls work:

1. An inbound routing rule in the dialplan.
2. A fix for a common authentication problem.

---

## Step 1: Creating the Inbound Dialplan Rule

We need a `[provider]` context in `extensions.conf` to match the `context=provider` we set in the peer definition.

**Simple Rule (All Calls to One Phone):**

```ini
[provider]
exten => _X.,1,Goto(phones,100,1)
```

**Explanation:**

- `_X.` : Matches any number of any length (all incoming calls, regardless of which number was dialed).
- `Goto(phones,100,1)` : Sends all calls to extension 100 in the `phones` context (Matthias's phone).

**In a Real System, You Would Normally:**

- Route different numbers to different extensions.
- Send calls to a queue or IVR instead of a single phone.

But for testing, routing everything to one phone lets you see the exact number format in the logs and then build proper rules later.

**Apply:**

```bash
dialplan reload
```

---

## Step 2: The "Unauthorized" Problem

We make the inbound call. The phone makes a sad sound and disconnects. What happened?

**SIP Debug Shows:**

```
INVITE from [provider IP] for [our_number]
401 Unauthorized
```

**Why?**

Run `sip show peers`:

```
provider/[provider_name]   [provider_IP]
```

The peer name is `provider`. But the INVITE is addressed TO our phone number (e.g., `1234567890`), not to `provider`. Asterisk compares the INVITE's username to the peer name. They don't match → "Unauthorized."

**The Problem Visually:**

- Peer name in config: `[provider]`
- INVITE is for: `sip:1234567890@our_ip`
- Asterisk thinks: "I have a peer called 'provider', but you're calling '1234567890'. These don't match. Go away."

---

## Step 3: The Fix — `insecure=invite`

We don't want Asterisk to care about the username in the INVITE. We want it to authenticate based on IP address/port only.

**Add to the Peer Definition in `sip.conf`:**

```ini
[provider]
type=friend
context=provider
host=sip.provider.com
secret=your_password
insecure=invite
```

**What `insecure=invite` Does:**

- Tells Asterisk: "Don't check the username in incoming INVITEs for this peer."
- Asterisk will still check the IP address and port match the peer definition.
- It just ignores whether the INVITE username matches the peer name.

**Why This is Needed:**

- Providers send calls TO your phone number, not TO a peer name.
- If you have 100 numbers, you'd need 100 peer definitions (one per number) without this setting.
- `insecure=invite` is the standard, correct solution.

**Apply:**

```bash
sip reload
```

---

## Step 4: The Successful Inbound Call

Make the call again.

**Result:**

- The call connects!
- The calling number is displayed correctly.
- The CLI shows the full SIP flow: INVITE → 200 OK → ACK → RTP.

**Always Check the Audio:**

We didn't check audio this time (international mobile call costs), but in real life, ALWAYS verify audio works in both directions. SIP signaling and RTP audio can take different paths. A call connecting does NOT guarantee audio works. Check for one-way audio, no audio, or poor quality.

---

## The Complete Working Provider Peer

```ini
[provider]
type=friend
context=provider
host=sip.provider.com
secret=your_password
insecure=invite
nat=force_rport,comedia
allow=ulaw
allow=alaw
```

---

## Summary: Inbound Call Checklist

| Step | What | Status |
|---|---|---|
| 1 | Registration string in [general] | ✅ |
| 2 | Peer definition | ✅ |
| 3 | insecure=invite (critical fix) | ✅ |
| 4 | Inbound context in dialplan | ✅ |
| 5 | Test call & check audio | ✅ (call connects) + verify audio! |

---

## Next Step

Inbound calls work. Now the more complicated part: configuring outbound calls through the provider — making calls from internal phones to the outside world via the SIP trunk.
