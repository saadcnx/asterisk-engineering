# SIP Provider: Outbound Calls Configuration

## What We Need

Two things are required for outbound calls through our SIP provider:

1. A dialplan rule that knows when to dial an outside number.
2. Correct peer configuration so the provider accepts our calls.

---

## Step 1: The Dialplan (Outbound Routing)

We already built this structure in earlier tutorials when we simulated the outside world. Now we replace the dummy peer with our real provider.

**The Outbound Dialplan:**

```ini
[phones]
; Outbound calls: dial 0 + number to reach the outside world
exten => _0X.,1,Goto(outgoing,${EXTEN:1},1)

[outgoing]
exten => _X.,1,Dial(SIP/provider/${EXTEN})
exten => _X.,n,Hangup()
```

**How It Works:**

1. User dials 0 followed by a number (e.g., `049123456789`).
2. `_0X.` matches in the `phones` context.
3. `${EXTEN:1}` strips the leading zero.
4. Jumps to `outgoing` context with the clean number.
5. `Dial(SIP/provider/${EXTEN})` dials the number through the provider peer.

**The Critical Syntax Difference:**

- Calling a single phone: `Dial(SIP/james)` — the peer name IS the destination.
- Calling through a provider: `Dial(SIP/provider/49301234567)` — the peer is the provider, and the number is added after a slash. The provider represents the whole world; you must specify which number in the world you want.

---

## Why Separate Contexts?

You should always have at least three contexts:

- **incoming** — calls from outside.
- **phones** (internal) — trusted internal phones.
- **outgoing** — calls to the outside world.

If everything is in one context, any peer can dial any number in your dialplan. That's not what you want.

---

## Step 2: The First Attempt (It Will Fail)

Make a test call: dial 0 + country code + number.

**Result:** The call fails.

**SIP Debug Shows:**

```
Failed to authenticate on INVITE to sip:Matthias@[local_IP]
```

**What's Happening:**

- Asterisk sends the INVITE with `From: Matthias@192.168.x.x` (our local IP).
- The provider sees this and says: "Who is Matthias? I don't know that IP address. Rejected."
- The provider expects the `From` header to contain:
  - **User:** Your account username (not your peer name or local extension).
  - **Domain:** The provider's domain (not your local IP).

---

## Step 3: Fixing the Peer — Two Critical Parameters

We need to manipulate what Asterisk sends in the SIP headers.

**Add to the Peer Definition in `sip.conf`:**

```ini
[provider]
type=friend
context=provider
host=sip.provider.com
secret=your_password
insecure=invite
fromdomain=sip.provider.com
defaultuser=your_account_username
```

**What These Parameters Do:**

| Parameter | Purpose |
|---|---|
| fromdomain | Changes the domain in the From header from your local IP to the provider's domain. |
| defaultuser | Changes the username in the From header to your provider account username. |

**Before (Failing):**

```
From: "Matthias" <sip:Matthias@192.168.100.55>
```
Provider says: "Who is Matthias? Unknown IP. Rejected."

**After (Working):**

```
From: "Matthias" <sip:your_username@sip.provider.com>
```
Provider says: "I know this username and domain. Authenticated."

---

## Step 4: Apply and Test

```bash
sip reload
```

Make the call again. Enable SIP debug to verify:

```bash
sip set debug peer provider
```

**Successful Debug Output Shows:**

- The From header now contains your username and the provider's domain.
- The To header contains the number you're dialing.
- Authentication succeeds.
- The call goes through.

---

## The General Approach for Any Provider

Different providers have slightly different requirements. The parameters to experiment with:

| Parameter | Try This If... |
|---|---|
| fromdomain | Provider rejects calls because the domain is your local IP. |
| defaultuser | Provider rejects calls because the username is wrong. |
| fromuser | Similar to defaultuser but specifically sets the From username (some providers want this instead). |

**How to Find the Right Settings:**

1. Enable SIP debug: `sip set debug peer provider`
2. Make a test call.
3. Read the error in the debug output.
4. Adjust `fromdomain`, `defaultuser`, or `fromuser` based on what the provider is rejecting.
5. Google: "Asterisk [provider name] configuration" — most providers have example configs online.

Now you have the background knowledge to understand WHY those examples work.

---

## Current Status

| Feature | Status |
|---|---|
| Registration | ✅ Working |
| Inbound Calls | ✅ Working |
| Outbound Calls | ✅ Working (basic) |
| Caller ID Transmission | ⬜ Next time |

---

## Next Step

The call works, but the recipient sees "Private Number" or no caller ID. Next time, we'll configure proper caller ID transmission so your outbound calls show the correct number.
