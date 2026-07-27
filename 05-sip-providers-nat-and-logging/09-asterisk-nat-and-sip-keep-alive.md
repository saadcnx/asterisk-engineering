# NAT & SIP: Keep-Alive and Why You (Usually) Don't Need Port Forwarding

## Quick SIP Recap

- Your PBX registers with the SIP provider.
- The registration tells the provider: "This is my IP address. Please send calls here."
- The provider looks at where the packet actually came from (the public IP on the NAT router) and sends calls there.
- This works because of NAT. The provider sees your public IP, not your private one.

---

## The Big Question: Source NAT, Destination NAT, or Both?

Look at the network picture:

- **Your Side:** PBX on a private local network behind a router with NAT.
- **Provider Side:** SIP carrier on the public internet.

**You definitely need Source NAT:**

Without it, your private IP wouldn't be routable on the internet. You couldn't make any connection at all.

**Do you also need Destination NAT (Port Forwarding)?**

Many people think: "I registered with the provider. But the NAT table entry only lives for maybe one minute. If the entry expires and then the provider sends me a call, the router won't know where to send it. So I must forward port 5060 to my PBX."

This is the common — and dangerous — mistake.

---

## The Danger of Destination NAT (Port Forwarding)

If you forward SIP ports from the outside world to your PBX:

- You might restrict it to the provider's IP address. But providers often have many IP addresses (load balancers, multiple servers).
- The host you register TO might not be the same host that sends calls TO you.
- If you open it to one IP, calls from other provider IPs get blocked.

Then you think: "I'll just accept ALL incoming SIP requests from ANY IP."

This is the death of your system. Port scanners constantly probe for open SIP ports. Brute-force attacks will hit your server. It works really well — at destroying your security.

**If you MUST use destination NAT:** Restrict it to the known IP range of your provider. It's only a little more secure, but better than completely open.

---

## The Real Solution: Keep the NAT Table Alive

You don't need destination NAT if the NAT table never forgets your SIP connection.

**How It Works:**

1. You register with the provider using Source NAT.
2. The NAT table creates an entry: "Packets from provider to public-IP:5060 → forward to PBX-private-IP."
3. The provider sends a small packet every ~30 seconds.
4. We saw this in our SIP debug: packets labeled "PING" from the provider.
5. Asterisk responds with "404 Not Found" (doesn't know what PING is).
6. But that's enough! The exchange of packets refreshes the NAT table entry. The TTL resets.

**Result:**

- The NAT entry stays alive indefinitely.
- When a call comes in 10 minutes later, the router still knows where to send it.
- No destination NAT needed. No port forwarding needed. No security holes opened.

---

## When Destination NAT MIGHT Be Needed

There are problematic scenarios:

**Scenario: Provider Uses Different Hosts for Different Traffic**

- You register to `sip.provider.com` (resolves to Host A).
- But SIP signaling comes from Host B.
- And RTP (audio) comes from Host C.
- The NAT table has an entry for Host A. Packets from Host B or C are dropped.

**How Some Providers Handle This (Good Providers):**

- They know about this NAT problem.
- They ensure all communication (signaling and media) comes from the SAME host you registered to.
- This is standard for commercial SIP trunks.

**How to Check Your Provider:**

1. Enable firewall/SIP debug.
2. Note the IP address you register TO.
3. Make an inbound call.
4. Check if the INVITE comes from the same IP address.
5. If yes → good provider, no destination NAT needed.
6. If no → you have a problem.

**Workaround for Bad Providers:**

- Register to ALL possible hosts (some platforms like Moby Dick have templates with 30+ hosts listed).
- Or, as a last resort, use destination NAT restricted to the provider's entire IP range.

---

## The Two Types of Provider Services (Revisited)

| Type | NAT Handling |
|---|---|
| Internet SIP Account/Trunk | Relies on NAT. You must manage keep-alive and NAT traversal. |
| Dedicated SIP Line | Provider gives you a box. The network terminates at your site. No NAT involved. Dedicated line, guaranteed quality. |

---

## Key Takeaway

You almost never need to forward ports (destination NAT) for SIP.

- Use Source NAT.
- Register with the provider.
- The provider's keep-alive packets (PING) maintain the NAT table entry.
- Incoming calls work without opening any inbound ports.
- Only fall back to destination NAT if your provider has a broken multi-host setup, and even then, restrict it to their IP range.

---

## Next Steps

We'll continue the NAT deep dive based on your comments and questions. Let us know if this made sense or if you need more detail on specific scenarios.
