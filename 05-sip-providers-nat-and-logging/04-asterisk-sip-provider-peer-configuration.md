# SIP Provider Peer Configuration

## Where We Are Now

We registered with our SIP provider. The provider knows our IP address and sends calls to us.

We made a test inbound call. It failed, but we got valuable information: number formats, codecs, and confirmation that connectivity works.

Now we need to create a proper peer definition so Asterisk can handle these calls correctly.

## Phone Peer vs. Provider Peer: Key Differences

We've created peers for phones many times. A provider peer is similar but has critical differences.

| Setting | Phone Peer | Provider Peer |
|---|---|---|
| host | dynamic (phone registers TO us, we don't know its IP) | Provider's hostname/IP (we send calls TO them, we know where they are) |
| context | phones (trusted internal context) | provider (UNTRUSTED external context) |
| secret | Phone's password | Provider's password for authentication |
| nat | Usually not needed (local phones) | Often needed (provider is across the internet) |

## The Peer Configuration

```ini
[provider]
type=friend
context=provider
allow=ulaw
allow=alaw
secret=your_provider_password
host=sip.provider.com
nat=force_rport,comedia
```

### Parameter Explanations:

**type=friend**

A friend means both directions work: we can send calls to the provider AND receive calls from the provider.

peer = outgoing only. user = incoming only. friend = both.

For a provider, we need both directions, so friend is the correct shortcut.

**context=provider**

This is the context incoming calls from this provider will enter.

CRITICAL SECURITY RULE: This context is UNTRUSTED. Calls from the outside world land here. Anyone can call into this context.

Never put external calls into the same context as your internal phones.

Every time you review your dialplan, check: "Is this untrusted context safe?"

Tip: Name the context after your specific provider (e.g., flowroute, telnyx) rather than just provider. This makes it clear which numbers belong to which provider.

**host=sip.provider.com**

This is the provider's SIP server address.

This is the key difference from phone peers (which use host=dynamic).

We register TO the provider, so we know exactly where they are. The provider registers to us, so they use dynamic on their end.

**secret**

The password shared between you and the provider for authentication.

**nat=force_rport,comedia**

This handles NAT traversal. force_rport and comedia together help audio flow correctly when Asterisk is behind NAT.

We'll cover NAT in detail in a future dedicated tutorial (by popular request).

For now, just know this is needed when your server is behind a router/firewall.

## Apply and Verify

```bash
sip reload
sip show peers
```

You should now see:
- The peer named provider.
- The provider's IP address.
- NAT settings (force_rport,comedia).
- Latency/qualify status.

## Summary: The Configuration Order So Far

| Step | What We Did | Status |
|---|---|---|
| 1 | Add registration string to [general] | ✅ Done |
| 2 | Verify registration (sip show registry) | ✅ Done |
| 3 | Make test inbound call, learn number formats | ✅ Done |
| 4 | Create peer definition | ✅ Done (today) |
| 5 | Create inbound dialplan routing | ⬜ Next time |

## Next Step

Now that the peer exists, we need to create the [provider] context in extensions.conf with proper inbound routing rules so that when a call comes in from the outside world, it actually rings a phone instead of being rejected.
