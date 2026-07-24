# SIP Provider Configuration: Registration

## Two Common Authentication Methods

SIP providers use different methods to send calls to your Asterisk server. The two most common are:

### Method 1: IP-Based Authentication
* You provide your public IP address to the SIP provider.
* The provider simply sends all calls to that IP address.
* Your firewall must be configured to forward incoming SIP traffic to your Asterisk server.
* No registration needed. The provider trusts that your IP is you.

### Method 2: Registration (Most Common)
* Your Asterisk server actively registers itself with the SIP provider.
* During registration, Asterisk tells the provider: *"Here is my current IP address. Please send all calls here."*
* This is the most common approach (90% of cases, if you're not a carrier).
* **NAT Advantage:** Registration opens a pinhole in your NAT/firewall. The provider (or Asterisk) sends a keep-alive packet every ~30 seconds to keep the NAT table entry open. You don't need to open inbound firewall ports — only allow established/related connections outbound.

---

## Real Provider Example: Flowroute

We use **Flowroute** as an example (chosen because it has an English front-end).

### Credentials Provided by the Provider
1. **SIP Server / Host:** The address to register to (e.g., `sip.flowroute.com`).
2. **Username:** Your account username.
3. **Password:** Your account password (visible in plain text on their portal — you need it to put in your Asterisk config anyway, so hiding it is pointless).

These are the three essential pieces of information you need.

---

## Step 1: Registration String in `sip.conf`

The registration string goes in the `[general]` section of `sip.conf`.

### Syntax
```text
register => username:password@host[:port]/extension
```

### Parameters

| Parameter | Required? | Description |
| :--- | :--- | :--- |
| **username** | Yes | Your provider account username. |
| **password** | Yes | Your provider account password. |
| **host** | Yes | The provider's SIP server address. |
| **port** | No | Only needed if not the default UDP 5060. |
| **extension** | Depends | The extension/number to register. With a trunk, which number do you register? It varies by provider — could be your main number, could be part of the SIP header. We'll see later. |

### Example
```ini
register => myuser:mypassword@sip.provider.com/1234
```

After adding this, reload SIP:
```bash
sip reload
```
*No error messages = good.*

---

## Step 2: Check Registration Status

### On Asterisk
```bash
sip show registry
```

**Output shows:**

| Field | Value |
| :--- | :--- |
| **Host** | The provider's SIP server. |
| **Port** | Usually 5060. |
| **Username** | Your account username. |
| **State** | Should say `Registered`. |
| **Refresh** | Time until next re-registration (e.g., 105 seconds). |

Asterisk only says **"Registered"** when it has received a positive response from the provider. This is your proof that:
- IP connectivity works.
- DNS resolution works (if you used a hostname).
- Credentials are correct.

### On the Provider Portal
Most SIP providers have a status page showing if you are registered or not (often a green/red lamp). This can be slow to update — trust the Asterisk CLI. If Asterisk says you're registered, you are.

---

## Important: Registration Does NOT Handle Calls Yet

At this point:
- The provider knows your IP address.
- The NAT pinhole is open.
- The provider would send incoming calls to your Asterisk.

**BUT:** Your Asterisk will drop all incoming calls because:
1. We haven't created a peer for the provider yet.
2. We have no inbound rules (context) to handle incoming calls.

So Asterisk will receive the call and say: *"I don't know what to do with this. Drop it."*

---

## The Correct Order of Configuration

1. **First:** Create a peer definition for the provider in `sip.conf` (so Asterisk knows who this provider is).
2. **Then:** Add the registration string (so the provider knows where to send calls).
3. **Then:** Configure the dialplan to handle incoming calls (so Asterisk knows what to do with them).

> **Note:** We tested registration first just to verify credentials and network connectivity — it's a good quick proof that everything is configured correctly so far.

---

## Next Steps

Next time, we'll create the peer definition for the provider and configure inbound call handling so we can actually receive a call from the outside world through a real SIP provider.
