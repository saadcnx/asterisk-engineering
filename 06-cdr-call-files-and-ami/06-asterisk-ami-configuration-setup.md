# AMI Configuration & Setup

## Getting Hands-On

Last time was theory. Today, we configure AMI step by step.

---

## Step 1: Check if AMI is Currently Enabled

```bash
netstat -lpn | grep asterisk
```

You'll see ports for SIP, IAX2, etc. But the AMI port (default 5038) is missing. By default, AMI is disabled for security reasons.

---

## Step 2: Edit manager.conf

File: `/etc/asterisk/manager.conf`

**⚠️ Security Warning (Read This):**

- Only enable AMI if you know what you're doing.
- NEVER enable AMI directly on a public IP address without protection.
- Use SSL/TLS or a VPN tunnel if accessing over the internet.
- For learning/demo on a local network, unencrypted is OK.
- Even with permit/deny IP rules, that alone is NOT enough security.

**Enable AMI:**

```ini
[general]
enabled = yes
port = 5038
bindaddr = 0.0.0.0
```

| Parameter | Purpose |
|---|---|
| enabled | yes to turn AMI on. |
| port | TCP port to listen on (default 5038). |
| bindaddr | IP to bind to. 0.0.0.0 = all interfaces. For testing, 127.0.0.1 (localhost only) is safer. |

**TLS Options (For Production):**

The config file also has parameters for enabling TLS encryption with certificates. We skip this for now, but use it in production.

---

## Step 3: Create a Manager User

```ini
[matthias]
secret = verysecretpassword
deny = 0.0.0.0/0.0.0.0
permit = 192.168.100.0/255.255.255.0
read = all
write = all
```

**Parameter Explanations:**

| Parameter | Purpose |
|---|---|
| [username] | The login name. Name it after the machine/purpose (e.g., crm-system, erp, monitoring). |
| secret | Password for this user. |
| deny | Deny access from these IPs/networks. Use 0.0.0.0/0.0.0.0 to deny everything by default. |
| permit | Allow access from these IPs/networks. Whitelist specific IPs. |
| read | Which event classes the user can READ. |
| write | Which event classes the user can WRITE (send commands for). |

**Important: Use Full Netmask Notation**

You must use the full dotted netmask (e.g., `255.255.255.0`), NOT CIDR notation (`/24`). AMI does not accept `/24`.

---

## IP-Level Security (Whitelisting)

```ini
deny = 0.0.0.0/0.0.0.0          ; Deny everything by default
permit = 192.168.100.0/255.255.255.0  ; Only allow my local network
```

This restricts which IPs can even attempt to connect. Only the CRM server, monitoring server, or your admin machine should be in the permit list.

**Alternative: Use ACLs (Access Control Lists)**

File: `/etc/asterisk/acl.conf`

Define named ACL groups:

```ini
[servers]
deny = 0.0.0.0/0.0.0.0
permit = 192.168.100.50/255.255.255.255
permit = 192.168.100.60/255.255.255.255
```

Then in `manager.conf`, reference the ACL by name instead of repeating IPs per user. This is cleaner when you have multiple users or config files.

---

## Permission Classes

| Class | Controls Access To |
|---|---|
| system | System-level commands (restart, shutdown). |
| call | Call control (originate, hangup, transfer). |
| log | Logging control. |
| verbose | Verbose output. |
| agent | Agent/queue management. |
| user | User/peer management. |
| config | Configuration read/write. |
| command | CLI command execution. |
| dtmf | DTMF events. |
| reporting | CDR and reporting data. |
| all | Everything above. |

**Best Practice for Learning:**

- Start with `read = all` and `write = all` to understand what's possible.
- Then restrict. For a read-only monitoring dashboard: `read = all`, `write = (nothing)`.
- NEVER use `read = all, write = all` on a production system unless you absolutely know what you're doing.

---

## Step 4: Restart Asterisk

When you change port bindings or enable/disable services, a reload is NOT enough. You must restart:

```bash
service asterisk restart
# or
systemctl restart asterisk
```

Verify the port is now open:

```bash
netstat -lpn | grep asterisk
```

You should now see TCP port 5038 listed.

---

## Current Status

| Step | Status |
|---|---|
| AMI enabled | ✅ Port 5038 open |
| User created | ✅ Username, password, permissions set |
| IP whitelist | ✅ Only local network permitted |
| Ready to connect | ⬜ Next time |

---

## Next Time

We'll actually connect to AMI via Telnet, log in, send commands, and watch the event stream — and see if our configuration actually works.
