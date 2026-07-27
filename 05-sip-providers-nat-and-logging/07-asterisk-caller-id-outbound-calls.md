# Setting Caller ID for Outbound Calls

## The Problem

When we make outbound calls through our SIP provider, the recipient sees "Private Number" or "Anonymous." We don't know what number will be displayed — maybe the provider sends nothing, maybe the first number in your range, maybe something random. You can't rely on luck. It's a best practice to set the caller ID explicitly.

---

## Why Set Caller ID?

- So the person you're calling sees your correct number.
- For teams: you might want to send the main office number instead of individual extensions.
- For call redirection: you might want to send the original caller's number, not your PBX number.
- If you don't set it, you don't know what the recipient will see.

---

## Step 1: Find Out What Format Your Provider Expects

Instead of trial and error, use the information you already have from an inbound call.

**The Trick:**

1. Enable SIP debug for your provider:

```bash
sip set debug peer provider
```

2. Make an inbound call to your registered number from your mobile phone.
3. Stop the debug (`sip set debug off`).
4. Scroll up and look at the INVITE from the provider.

**What You'll See:**

- The From header shows how the provider formats the caller's number.
- Example: `From: +49123456789` (German mobile with +49 country code).
- Example: `From: +12125551234` (US number with +1 country code and area code).

**The Logic:** Your provider sends numbers in a specific format. It will almost certainly accept outbound caller IDs in the same format. This saves you trial and error.

---

## Step 2: Set the Caller ID in the Dialplan

The variable is `CALLERID(num)`.

**Modify the Outbound Dialplan:**

```ini
[phones]
exten => _0X.,1,Set(CALLERID(num)=+12125551234)
exten => _0X.,n,Goto(outgoing,${EXTEN:1},1)

[outgoing]
exten => _X.,1,Dial(SIP/provider/${EXTEN})
exten => _X.,n,Hangup()
```

**How It Works:**

1. User dials 0 + external number.
2. `Set(CALLERID(num)=+12125551234)` sets the caller ID to your desired number, in the format you determined from the inbound call test.
3. The call proceeds through the `outgoing` context and is dialed via the provider.
4. The recipient sees your specified number, not "Private" or "Anonymous."

---

## Step 3: Test

1. Reload the dialplan: `dialplan reload`
2. Make an outbound call to your mobile.
3. Check what number appears on the mobile phone.

**If it works:** Your mobile shows the number you set. Done!

**If it fails (call rejected or wrong number):**

- The provider might not allow you to set arbitrary numbers.
- This depends on a feature called CLIP No Screening.

---

## CLIP No Screening

**What It Is:**

A feature some providers enable that allows you to set any caller ID number — even numbers you don't own.

Dangerous but useful: You can set the original caller's number when doing call forwarding. For example: Customer calls your PBX → PBX forwards to your mobile → your mobile shows the customer's number (so you can call them back).

**How to Test If It's Enabled:**

1. Set an arbitrary caller ID (a number you don't own).
2. If the call goes through with that number → CLIP No Screening is enabled.
3. If the call fails or the provider overrides your caller ID → CLIP No Screening is disabled.

**If Disabled:** Ask your provider to enable it, or only set numbers within your registered range.

---

## Different Methods (If CALLERID(num) Doesn't Work)

Sometimes setting `CALLERID(num)` isn't enough. Some providers look at different SIP headers for caller ID. In that case, you might need to manipulate:

- `fromuser` in the peer definition (sip.conf).
- PAI (P-Asserted-Identity) header.
- RPID (Remote-Party-ID) header.

For most standard providers, `CALLERID(num)` works out of the box. This is the most common method.

---

## Summary: Setting Caller ID

| Step | Action |
|---|---|
| 1 | Make an inbound call, capture the provider's number format from the INVITE. |
| 2 | Use Set(CALLERID(num)=...) in the dialplan with the same format. |
| 3 | Reload and test. |
| 4 | If arbitrary numbers work → CLIP No Screening enabled. |
| 5 | If not → test with numbers from your registered range, or contact provider. |

---

## What We've Covered for SIP Providers (Complete Basics)

| Feature | Status |
|---|---|
| Registration | ✅ |
| Peer Configuration | ✅ |
| Inbound Calls | ✅ |
| Outbound Calls | ✅ |
| Caller ID | ✅ (today) |

---

## Next

Back to NAT — by popular request, a deep dive into how NAT affects SIP and how to troubleshoot it.
