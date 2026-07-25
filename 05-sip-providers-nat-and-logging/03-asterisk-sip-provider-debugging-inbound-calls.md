# SIP Provider: Debugging & Learning from Inbound Calls

## Fix from Last Time: Registration Confirmed

Last time, the provider's portal didn't show us as registered immediately. After waiting ~30 seconds and refreshing, it updated. The portal now shows:
* Our external IP address.
* The port.
* When registration refreshes.
* User agent: `Asterisk PBX 11`.

> **Rule of Thumb:** Trust Asterisk CLI first — `sip show registry` saying **"Registered"** is the source of truth. The provider's web portal can be slow to update.

---

## Seeing the NAT Keep-Alive in Action

We can prove that the provider sends packets to keep the NAT connection open.

### Enable SIP Debug for the Provider's IP
*(Even without a peer configured)*

```bash
sip set debug ip sip.flowroute.com
```
*(Asterisk automatically does a DNS lookup and enables debugging for that IP.)*

### What We See
After waiting ~30 seconds, we get a packet from the provider:

```text
<--- SIP read from [provider IP]: "PING"
```

Asterisk responds: *"I don't know what 'PING' is, not found (404)."*

**Why this matters:**
The exchange of packets keeps the NAT table entry alive. The provider sends a ping, Asterisk responds (even with an error), and the NAT pinhole stays open. This is why you don't need to open inbound ports on your firewall — registration handles it.

> 🔒 **Security Note:** Many people forward all traffic from the firewall to the PBX and open all ports. This is very dangerous. Please use registration and keep-alive instead.

---

## The Smart Trick: Learn from a Failed Inbound Call

Now we need to figure out how **THIS** specific provider works so we can configure Asterisk correctly. The best approach: make an inbound call and learn from what happens.

### Step 1: Make an Inbound Call
Dial the number you registered with the provider from your mobile phone.

### Step 2: Observe the Failure
The call fails with a message like:

```text
Call from [caller_number] to extension [dialed_number] rejected because extension not found in context 'public'
```

#### Why This Happens
We have no peer configured for the provider. When Asterisk receives a call from an unknown source, it tries to place it in the `[public]` context by default. Since we haven't configured anything in `[public]`, the call is rejected.

---

## The Goldmine of Information from a Failed Call

Even though the call failed, we now have critical information about how this provider operates:

| Information Learned | Why It Matters |
| :--- | :--- |
| **Registration works** | Provider knows our IP. |
| **NAT is open** | The call reached us successfully. |
| **Incoming number format** | We can see exactly how the provider sends the caller's number (the `From` header). |
| **Destination number format** | We can see exactly how the provider sends the dialed number (the `To` header). |
| **Codecs offered** | We can see which codecs the provider supports in the `INVITE`. |

All this information is visible in the SIP debug output from the failed call. We can copy-paste these formats directly when building our inbound routing rules.

---

## Why This Order Works Best

### The Instructor's Recommended Order
1. **Register first** (prove connectivity and credentials work).
2. **Make an inbound call** (gather information about how the provider formats numbers).
3. **Then create the peer and inbound routing** (using the information gathered in step 2).

### The Alternative (Peer First, Then Call)
If you create the peer first and make an outbound call that fails, you get almost no useful debug information — just a failed outbound attempt.

### Why Inbound First is Better
* You can see the exact format the provider uses for caller ID and dialed numbers.
* You can see which codecs they offer.
* You can confirm end-to-end connectivity.
* All with **zero configuration** on your side — it's a low-effort, high-information test.

---

## Next Steps

Now that we know:
* The provider's number format.
* The codecs they use.
* That connectivity is working.

**We will:**
1. Create a peer definition for the provider in `sip.conf`.
2. Create a dedicated context for inbound calls from the provider.
3. Build inbound routing rules to actually ring a phone.

*(All using the exact formats we observed from this failed call.)*
