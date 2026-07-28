# CDR (Call Detail Records): Introduction

## What Are CDRs?

CDR stands for Call Detail Records. It's a mechanism that stores information about every call: who called, who was called, duration, billing time, and more.

---

## Who Needs CDRs?

**Basic Users (Simple Logging):**

- Just want a record of calls for reference. The default CSV file is enough.

**Business-Critical Users (Billing):**

- CDRs are the most important part of the system because they generate invoices and revenue.
- You can write bills based on CDR records.

---

## The Complexity: Duration vs. Billing Time

This is where CDRs get complicated.

**Simple Scenario (Outbound Call):**

- You dial out. The call takes 60 seconds total.
- But it rang for 20 seconds before the person answered.
- You can only bill for 40 seconds (the speaking time, not the ringing time).
- Asterisk stores both: duration (total) and billsec (billable seconds = time from answer to hangup).

**More Complex Scenario (Inbound Call Center):**

- You get paid for inbound calls.
- Asterisk answers the channel immediately (auto-attendant): "Hello, welcome to... the next available agent..."
- Billsec starts counting from the moment the channel is answered — by a MACHINE, not a human.
- You may only get paid for the time a HUMAN agent actually talks.
- This mismatch makes billing complex.

**Even More Complex (Transfers):**

- Call comes in. Agent talks. Agent transfers to supervisor. Supervisor transfers back.
- What part is billable? To whom?
- Different campaigns may have different billing rates.

**Why the Configuration is So Complex:**

Because billing can be essential for your business model — even critical. Asterisk provides many configuration files and options to handle all these scenarios.

---

## The Default CDR Configuration

**Where to find CDR files:**

`/etc/asterisk/` — contains many CDR-related files:

- `cdr.conf` — Basic CDR configuration.
- `cdr_custom.conf` — Custom CDR formatting.
- `cdr_manager.conf` — CDR via AMI (Asterisk Manager Interface).
- `cdr_odbc.conf` / `cdr_mysql.conf` — Database connections (MySQL, ODBC, PostgreSQL, etc.).
- `cdr_syslog.conf` — CDR to syslog.

**The Default CSV Output:**

- File: `/var/log/asterisk/cdr-csv/Master.csv`
- Format: Comma-Separated Values (CSV) — one line per call.
- Contains columns like: caller, callee, duration, billsec, timestamp, etc.

**Important Behavior:**

- Asterisk writes the CDR record at the end of the call (when the channel hangs up). This is the default. You see nothing until the call is completely finished.

---

## Forcing Early CDR Write

You might want to write a CDR record before the call ends.

**Use Case:**

- Agent answers. Deals with customer. Transfers to a survey system ("Press 1 if you were happy...").
- You want to bill for the agent portion, not the survey portion.
- You can force a CDR write at the moment of transfer. A second CDR record starts for the survey part.

**Application:**

- Use `NoCDR()` or `ResetCDR()` in the dialplan to control when records are written and when they reset.

---

## What's Next

CDRs are a deep topic. We've covered the basics:

- What CDRs are.
- The difference between duration and billsec.
- Why billing gets complex.
- Default CSV configuration.

Next time, we'll look at some tricks and tweaks you can do easily with CDR configuration — not building a full billing engine, but practical adjustments you can make.
