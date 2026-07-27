# NAT Basics: What It Is & How It Works

## Why Do We Need NAT?

You have a local private network (home, office) with private IP addresses. You have a router connected to the internet with a public IP address. You want to reach Google (or any server on the internet).

**The Problem with Private IP Addresses:**

- Private IP addresses (like `192.168.x.x`, `10.x.x.x`) are not unique. Your neighbor might use the same ones.
- Private addresses cannot be routed over the public internet. Internet carriers will not forward them.
- If you tried to send a packet with a private source address to Google, Google wouldn't know where to send the reply — your private address means nothing on the public internet.

**The Solution: NAT (Network Address Translation)**

- NAT hides (masquerades) all your private internal addresses behind your single public IP address.
- When you make a request to Google, Google sees the request coming from your router's public IP, not your private notebook IP.
- Google sends the reply to your public IP. Your router translates it back to your private IP and delivers it.

**Why Else? (The Original Reasons)**

- **IPv4 Address Shortage:** There aren't enough public IPv4 addresses for every device in the world. NAT lets many devices share one public IP.
- **IPv6 Note:** With IPv6, NAT is technically no longer needed because there are enough addresses for every device to have its own public IP.
- **Security Side-Effect:** NAT hides your internal hosts. They are not directly reachable from the internet. This provides a basic level of security (though it's not a firewall).

---

## The NAT Table (Critical Concept)

NAT doesn't just translate addresses blindly. It must remember every translation so replies can be correctly routed back.

**How It Works:**

1. Your notebook (`192.168.1.10`) sends a request to Google.
2. The router changes the source address to your public IP and forwards it.
3. The router stores an entry in the NAT table: "Request from `192.168.1.10` to Google. When a reply comes back from Google, send it to `192.168.1.10`."
4. Google replies to your public IP.
5. The router looks up the NAT table, finds the matching entry, translates the destination back to `192.168.1.10`, and delivers it.

**How Long Do Entries Stay? (TTL — Time To Live)**

- Entries are NOT stored forever.
- Most implementations have a timeout (TTL): 30 seconds, 60 seconds, maybe 2 minutes — depends on the router.
- If no new packets are exchanged for that connection before the timeout, the entry is deleted.
- If there IS ongoing traffic (a download, a long call), the entry is constantly refreshed and stays alive.
- If an entry expires and a reply packet arrives later, the router doesn't know where to send it — the packet is dropped.

This TTL concept is extremely important for SIP, as we'll see in the next tutorial.

---

## Two Types of NAT

**1. Source NAT (SNAT) — The Most Common**

- Changes the source address of outbound packets.
- Used when you want to hide your local network behind a public IP for internet access.
- "I change the source address of my request to my public IP."
- 99% of home/office internet usage uses Source NAT.

**2. Destination NAT (DNAT) — For Inbound Services**

- Changes the destination address of inbound packets.
- Used when you want to expose an internal service to the internet (port forwarding).
- Example: "If someone accesses my public IP on port 5060, translate the destination to my Asterisk server at `192.168.1.50`."
- Used for hosting game servers, web servers, or a PBX that must be reachable from outside.

---

## Summary

| Concept | Description |
|---|---|
| Private IP | Cannot be routed on the internet. |
| NAT | Translates private ↔ public addresses. |
| NAT Table | Stores active translations so replies can be routed back. |
| TTL (Timeout) | Entries expire if no traffic keeps them alive. |
| Source NAT | Masks internal devices behind one public IP (outbound traffic). |
| Destination NAT | Exposes internal services to the internet (inbound traffic). |

---

## Next Step

Now that we understand what NAT is and how the NAT table works (especially TTL/timeouts), next time we'll look at what this means specifically for SIP connections — and why NAT causes so many problems for VoIP.
