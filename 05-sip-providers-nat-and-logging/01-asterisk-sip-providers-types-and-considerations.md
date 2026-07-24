# SIP Providers: Types, Differences & Considerations

## What is a SIP Provider?
A SIP provider takes your calls via SIP (pure IP connection — no analog, no GSM) and sends them to the PSTN (the public telephone network: landlines, mobile phones). It's like a normal telephony provider, but uses only SIP.

## Two Main Types of SIP Provider Services

### Type 1: Internet-Based (Registration Only)
- The provider gives you an account (username/password).
- You register your Asterisk server to their SIP proxy over the public internet.
- They give you a number (or numbers) and route calls to/from the PSTN.
- **How to find them:** Search "SIP providers" on Google. You'll find many.

**The Problem: No Quality of Service (QoS)**
What's between you and the SIP provider is the internet — different routers, different ISPs, different paths. The provider is only responsible from their registration point to the PSTN. They cannot guarantee the quality of the line between you and them.

If call quality is bad:
- Is it your PBX?
- Is it your internet connection?
- Is it a router somewhere in between?
- Is it the provider?

It's very hard to debug, and very hard to determine who is responsible for the problem. In most cases it works fine, but when there's a problem, it's painful.

### Type 2: Dedicated/Guaranteed Connection
- The provider ensures the connection to your site.
- You might get a separate physical line for VoIP, or a dedicated VLAN on your existing connection.
- They can guarantee Quality of Service (QoS) from their SIP switch all the way to your PBX.

**The Advantage:**
- One provider is responsible for the entire service.
- If something doesn't work, you call them and say: "Fix it."
- They can't blame your internet or a router in between — they own/control the whole path.

**The Trade-off:**
- These connections are more expensive.
- But if you have high call volume and really need good quality, the return on investment (ROI) is better in the long run than losing business to poor call quality.

## Which to Choose?
Consider your needs:
- If your business relies heavily on phone calls, invest in a guaranteed connection.
- If losing phone service for a day isn't critical (you mainly work via email), an internet-based provider may be sufficient.

## Other Factors to Consider When Choosing a Provider

### 1. Codec Support
- Does your Asterisk server support the same codecs as the provider?
- Most providers support G.711 (ALAW/ULAW). Check for G.729 if bandwidth is a concern.
- If you use HD codecs (G.722), confirm the provider supports them (most don't).

### 2. SIP Peer vs. SIP Trunk

| Type | Description |
|---|---|
| SIP Peer (User-based) | One account = one number. If you need 5 numbers, you create 5 separate accounts/registrations. |
| SIP Trunk | One registration = many numbers. You register once, and all your numbers are routed through that single trunk. A trunk can carry multiple simultaneous calls. |

This is an important distinction. A trunk is more scalable for businesses with many numbers.

## Quick Decision Checklist

| Question | Why It Matters |
|---|---|
| Can they guarantee QoS to my site? | Determines who is responsible for call quality issues. |
| Which codecs do they support? | Must match your Asterisk configuration. |
| Is it a trunk or peer-based service? | Affects how you configure multiple numbers. |
| Does my existing internet provider offer VoIP? | Bundling can simplify support and accountability. |

## Next Steps
This was a theoretical overview. Next time, we'll choose a real SIP provider, get credentials, and actually configure Asterisk to connect to it hands-on.
