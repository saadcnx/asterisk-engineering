# Asterisk Logging: Configuration Basics

## The Problem with Console-Only Debugging

Throughout these tutorials, we've used the Asterisk CLI with high verbosity (`-vvv`) to see what's happening in real time. This works for development and testing, but has two major problems for production:

- **Heavy Load:** On a busy system, output scrolls impossibly fast. Finding YOUR call among hundreds of simultaneous calls is nearly impossible.
- **Historical Debugging:** A user says: "Yesterday at 2 PM, my call failed." You can't go back in time to watch the console. You need logs stored on disk.

---

## The Logging Configuration File: logger.conf

Located at `/etc/asterisk/logger.conf`.

**Key Sections:**

**General Settings:**

Date format, log rotation settings, etc.

**Log Levels Available:**

- `debug` — Very detailed.
- `notice` — Normal but significant events.
- `warning` — Potential problems.
- `error` — Real problems.
- `verbose` — The dialplan execution lines we see in the console.
- `dtmf` — Key presses.
- `fax` — Fax-related.
- `security` — Authentication failures, attacks.

**Defining Log Destinations:**

The format is: `destination => levels`

```ini
; Default: Log to the messages file
messages => notice,warning,error

; Default: Log to the console
console => notice,warning,error

; We add: verbose to the messages file
messages => notice,warning,error,verbose
```

- `messages` : A file at `/var/log/asterisk/messages`.
- `console` : The Asterisk CLI of the first process started. Verbosity here is controlled by the `-v` switches at startup.
- You can create custom files (e.g., `detailed => notice,warning,error,verbose,dtmf`).

---

## Important: Verbosity Level for Files

When you log into the Asterisk console with `asterisk -rvvv`, you get verbosity level 3. But this does NOT affect the log file.

**To set the file's verbosity level:**

1. After changing `logger.conf` to include verbose, reload:

```bash
logger reload
```

2. Then set the verbosity level for the logger:

```bash
core set verbose 3
```

3. Verify: `logger show channels` — shows active log channels (but sadly doesn't display the verbosity level here; trust that it's set).

Now, dialplan execution lines appear in `/var/log/asterisk/messages`.

---

## Using the Log File for Debugging

The advantage of file-based logging:

- **Searchable:** Use `less` or `grep` to find specific call IDs, timestamps, or extensions.
- **Historical:** If a user reports a problem at a specific time, you can jump directly to that timestamp and see exactly what happened.
- **Quiet:** No scrolling console. Analyze at your own pace.

**Common Scenario:**

User says: "I had a problem at 2:15 PM." You open the log, search for the timestamp, and trace the entire call flow.

---

## Log Rotation

Logs don't grow forever. Asterisk has built-in log rotation.

**Default Behavior:**

- Files like `/var/log/asterisk/messages` get rotated.
- Rotated files appear as `messages.0`, `messages.1`, etc. (older files have higher numbers).
- Eventually, old files are deleted.

**Configuration:**

- Rotation settings can be adjusted in `logger.conf`.
- Separate log files exist for different components (e.g., `queue_log` for call center/queue statistics, used by tools like QueueMetrics).

---

## Best Practice Recommendations

**For New Installations / Testing Phase:**

- Enable verbose logging to files.
- This helps iron out bugs and understand system behavior.
- Once stable, you can reduce logging.

**For Production (Stable):**

- Consider reducing verbose logging to save disk space and CPU, especially on heavy-traffic systems.
- Keep notice, warning, error enabled at minimum.
- Configure log rotation to match your retention needs.

**Don't Leave Verbose Logging On Forever Without Rotation:**

On a busy system, it consumes significant disk space. Set appropriate rotation.

---

## Summary: Console vs. File Logging

| Method | Best For |
|---|---|
| Console (-vvv) | Live development, immediate testing of dialplan changes. |
| File Logging | Production debugging, historical analysis, searching by time or call ID. |

---

## What's Next?

We've covered the basics of Asterisk logging. There's more depth (CDR — Call Detail Records, custom log formats, security logging), but this gives you the foundation for debugging anything in your dialplan or call flow. Let us know in the comments what topics you'd like us to cover next.
