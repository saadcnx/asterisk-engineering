# CDR Part 2: Understanding Fields & Customization

## Two Goals for Today

1. Understand what each field in the CDR record means.
2. Learn how to add custom information (like marking a call for a specific project or customer) directly from the dialplan.

---

## Understanding the CDR Fields

Here's an example record from `/var/log/asterisk/cdr-csv/Master.csv`:

```
"James","100","voicemail","phones","SIP/james-00000001","SIP/matthias-00000002",
"Dial","SIP/matthias,5","2024-01-15 10:30:00","2024-01-15 10:30:02","2024-01-15 10:30:03",
2,1,"ANSWERED","DOCUMENTATION","",""
```

**How to Find What Each Field Means:**

The best reference is the Asterisk wiki. Search for "Asterisk CDR Variables" — these are the dialplan variables, but they map directly to the CSV fields.

**Key Fields Explained:**

| Field | Example Value | Meaning |
|---|---|---|
| accountcode | (empty) | Can be set for grouping/billing. |
| src (Caller ID) | James | Who made the call. |
| dst (Destination) | 100 | The extension/number dialed. |
| dcontext (Destination Context) | phones | The dialplan context the call ended in. |
| clid (Caller ID Name) | "James" | Full caller ID string. |
| channel | SIP/james-... | The caller's channel. |
| dstchannel | SIP/matthias-... | The called party's channel. |
| lastapp | Dial | The last application executed. |
| lastdata | SIP/matthias,5 | Arguments to the last application. |
| start | 2024-01-15 10:30:00 | When the call started. |
| answer | 2024-01-15 10:30:02 | When the call was answered. |
| end | 2024-01-15 10:30:03 | When the call ended. |
| duration | 2 | Total time in seconds (ringing + talk). |
| billsec | 1 | Billable seconds (only from answer to hangup). If call was never answered, billsec = 0. |
| disposition | ANSWERED | What happened: ANSWERED, NO ANSWER, BUSY, FAILED. |
| amaflags | DOCUMENTATION | Billing-related flag (usually default). |
| userfield | (empty) | A custom field YOU can set. |

**Important:** Asterisk writes the CDR record at the end of the call (when the channel hangs up). The `lastapp` and `lastdata` fields show you the final state — e.g., Voicemail with `100` and `b` (busy greeting) tells you the call went to voicemail with the busy message.

---

## Customizing CDRs from the Dialplan

You can add your own information to CDR records directly from the dialplan. This is useful for marking calls by project, customer, or campaign.

**Method 1: Using the userfield**

The userfield is a built-in empty field specifically for your custom data.

```ini
exten => 100,1,Set(CDR(userfield)=ProjectA)
exten => 100,n,Dial(SIP/matthias)
```

After the call, the CSV will contain `ProjectA` in the userfield column. You can then filter/search for all calls marked with `ProjectA`.

**Method 2: Adding Your Own Custom Fields**

You can create entirely new fields:

```ini
exten => 100,1,Set(CDR(mycustomfield)=SomeValue)
```

This adds `mycustomfield` with the value `SomeValue` to the CDR record. This gives you unlimited flexibility.

**Good Practice:** Use the userfield for standard custom data. Create new fields only when you need more than one custom value.

---

## Testing the Custom Field

1. Add the `Set(CDR(userfield)=YourValue)` line to your dialplan.
2. Reload: `dialplan reload`
3. Make a test call.
4. Check the CSV file: `/var/log/asterisk/cdr-csv/Master.csv`
5. Your value appears in the userfield column.

---

## Beyond CSV: Database Storage

CSV files are hard to analyze (not reader-friendly, not easy to query). Asterisk supports writing CDRs directly to databases:

- MySQL / MariaDB
- PostgreSQL
- SQLite
- ODBC (any ODBC-compatible database)

Configuration files like `cdr_mysql.conf` and `cdr_odbc.conf` handle this.

**Custom Mappings:**

If you don't like Asterisk's default CDR structure, you can define exactly which fields go where using `cdr_custom.conf`. You can map every single field to your own schema.

This allows integration with billing systems, reporting dashboards, and business intelligence tools.

---

## Summary

| What You Can Do | How |
|---|---|
| Understand CDR fields | Asterisk Wiki: CDR Variables |
| Add custom data | Set(CDR(userfield)=value) or Set(CDR(customfield)=value) |
| Store in database | Configure cdr_mysql.conf or cdr_odbc.conf |
| Custom field mapping | Use cdr_custom.conf |

CDRs are essential for billing and reporting. With these basics, you can mark calls, query records, and integrate with external systems. If you have specific questions, leave a comment and we'll address them.
