# AMI: Finding & Executing Actions (Originate)

## Beyond Login: Doing Something Useful

Login is nice, but you need it because otherwise you see nothing. Now we learn how to find out which commands (Actions) are available on YOUR specific Asterisk version, and how to use them.

---

## How to Find Available AMI Actions

Just like we found dialplan applications on the Asterisk CLI, we can find AMI actions the same way.

**List All Actions:**

```bash
asterisk -rvvv
manager show commands
```

This lists every action your Asterisk version supports. You can scroll through it or use your terminal's search function.

**Get Detailed Help for a Specific Action:**

```bash
manager show command Originate
```

This shows:

- Required parameters (mandatory).
- Optional parameters (in brackets).
- Dependencies (e.g., if you use Extension, you MUST also provide Context and Priority).
- Descriptions of each parameter.

This is critical because different Asterisk versions support different actions and parameters. Googling might give you outdated info. Always check YOUR system.

---

## The Originate Action (Creating a Call)

This is the AMI equivalent of a call file. It tells Asterisk: "Call this channel, and when answered, connect it to that destination."

**Command (Prepared Offline and Pasted):**

```
Action: Originate
Channel: SIP/james
Context: phones
Extension: 100
Priority: 1
```

**Note:** AMI actions are case-sensitive. `Action: Originate` must have the capital O. Type carefully, or better: prepare your commands in a text editor and paste them. In a real program, the AMI library handles this for you.

**Parameters:**

| Parameter | Required | Purpose |
|---|---|---|
| Action | Yes | The action name (Originate). |
| Channel | Yes | The channel to call (e.g., SIP/james). |
| Context | Conditional | The dialplan context to enter (required if using Extension). |
| Extension | Conditional | The extension to dial (required if using Context). |
| Priority | Conditional | Priority to start at (required if using Extension/Context). |
| CallerID | Optional | Set caller ID for the call. |
| ActionID | Optional | An ID you assign. The response will include this ID so you can match it in heavy traffic. |

---

## Executing the Originate

1. Connect and authenticate to AMI (see previous tutorial).
2. Paste the Originate command.
3. Press Enter twice (empty line to signal end of command).

**Immediate Response:**

You get an immediate success/failure response. You know right away if the command was accepted.

**Event Stream:**

Then events start flowing:

- Channel created.
- Call ringing.
- Answered.
- Connected to context.
- Hangup.

You can monitor every single step.

---

## Call Files vs. AMI Originate

| Aspect | Call Files | AMI Originate |
|---|---|---|
| Ease of Use | Simple: copy a file. | Requires a program/script. |
| Feedback | Fire and forget. You don't know what happened unless you script CDR checks. | Immediate response + full event stream. |
| Control | No real-time control. | You can track every event and react programmatically. |
| Best For | System admins, simple alarming. | Programmers, complex integrations. |

**Top Tip from Matthias:**

- If you're a system administrator → use call files. They're simple and reliable.
- If you're a programmer → use AMI. It gives you full control and feedback.
- Don't type AMI commands manually for real work. Use a library for your programming language.

---

## How Libraries Simplify AMI

In a real application, you don't type raw AMI commands. You use a library (Python, PHP, Java, Node.js, etc.):

```python
# Pseudo-code example
ami = AMI.connect("192.168.100.55", 5038, "matthias", "secret")
call = ami.originate(channel="SIP/james", context="phones", extension="100", priority=1)
if call.success:
    print("Call placed!")
    call.on_answer(lambda: print("Call answered!"))
    call.on_hangup(lambda: print("Call ended!"))
```

The library handles:

- Connection and authentication.
- Sending commands.
- Parsing the event stream.
- Matching responses to requests (using ActionID).
- Providing clean APIs for common operations.

---

## Summary

| What We Learned | How |
|---|---|
| List all AMI actions | manager show commands |
| Get help on an action | manager show command Originate |
| Place a call via AMI | Action: Originate with Channel, Context, Extension, Priority |
| Understand the flow | Immediate response + event stream |
