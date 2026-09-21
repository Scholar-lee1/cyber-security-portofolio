# Command Injection

## Overview

```
Imagine you tell a worker:
“Go and call John.”
But instead of only doing that, the worker blindly follows whatever you write on the note.
So someone writes:
“Call John and also do something else.”
If the worker follows both instructions, that's the basic idea behind Command Injection.

What is it?
👉 Tricking a computer into running a command that you didn't originally intend it to run.
Simple example:
A website asks:
“Enter a website/IP to check.”
The website is supposed to check only what you entered.
If it's poorly programmed, someone may add extra instructions to their input, causing the computer to perform an additional action.

Why does it happen?
Because the application trusts the user's input too much and passes it to the computer as a command without properly separating or checking it.

What are we looking for?
We're basically checking:
“Can I make my input do more than what this application intended?”
So remember:
Command Injection = User input sneaks an extra instruction into a computer command.
```
So technically,
Command Injection is a vulnerability where user input is passed directly to system commands without proper sanitization. An attacker can inject OS commands to execute arbitrary code on the server.
*Risk Level:* Critical (Remote Code Execution)


---
## 1. Interface
<img width="854" height="390" alt="Screenshot 2026-09-19 111121" src="https://github.com/user-attachments/assets/2e78732b-7aee-48fa-8645-2ee8fae8ecd1" />

Brief: what the form does (accepts IP, runs ping)


---
## 2. Level: LOW

### Source Code
<img width="987" height="614" alt="Screenshot 2026-09-19 111152" src="https://github.com/user-attachments/assets/4d4a90e5-810c-4cb1-a54b-a73ffdae6a8f" />

What makes it exploitable: Reviewing the source code reveals three main actions: verifying input presence, determining the OS to run the appropriate ping command, and returning the output to the user. Crucially, the input is left unsanitized, allowing an attacker to inject and execute arbitrary commands. 

### Exploitation
What happened: Seeing the vulnerability, we can target the machine at IP `127.0.0.1` and inject additional commands into the input. By using `;` to separate commands, we can execute commands such as `whoami` to identify the current user and `cat /etc/passwd` to display the contents of the file containing information about user accounts on the system.

```text
127.0.0.1 ; whoami ; cat /etc/passwd
```

<img width="908" height="840" alt="Screenshot 2026-09-19 111236" src="https://github.com/user-attachments/assets/143e3726-6065-43fa-993c-f22696b1d8c2" />

---
## 3. Level: MEDIUM

### Source Code

<img width="986" height="735" alt="Screenshot 2026-09-19 111428" src="https://github.com/user-attachments/assets/5e3a65b7-501c-43bf-a6b3-5c0f29a89e94" />

How it differs from Low:
A filter has now been added. It removes certain characters like `;` and `&&` that can be used to join commands. The application also checks the operating system to know which type of `ping` command to use.

Why Low's payload fails now:
Our Low-level payload used `;` or `&&` to add another command. Since the filter now removes them, that method doesn't work anymore.

**How we exploit it anyway:**
The filter only blocks specific characters. It doesn't block every way of joining commands. We can use `|` instead, which the filter doesn't remove, to pass another command to the system.

```text
127.0.0.1 | whoami
```

### Exploitation
<img width="903" height="528" alt="Screenshot 2026-09-19 111535" src="https://github.com/user-attachments/assets/995502f9-fc39-4bd5-8f1e-2b9dc0a8eb6e" />


---
## 4. Level: HIGH

### Source Code

<img width="984" height="826" alt="Screenshot 2026-09-19 111843" src="https://github.com/user-attachments/assets/807afce5-6567-4dbf-b9d8-2b7a6d5e909f" />

**How it differs from Medium:**
The filter is stronger. It now blocks more ways of joining commands, including the `|` operator when it is written with a space after it.

**Why Medium's bypass fails now:**
Our Medium-level payload used `|` with a space, like `127.0.0.1 | whoami`. The stronger filter detects this format, so the payload doesn't work.

**How we exploit it anyway:**
The filter is still not properly sanitizing the input. We can remove the space after `|` and use it directly before the command:
```text
127.0.0.1 |whoami
```

This can still cause the system to interpret `whoami` as an additional command.

### Exploitation
<img width="927" height="475" alt="Screenshot 2026-09-19 112032" src="https://github.com/user-attachments/assets/c2e2e569-c1f1-4c44-a414-a9d79d8d47b6" />


---
## 5. Level: IMPOSSIBLE

### Source Code

<img width="982" height="767" alt="Screenshot 2026-09-19 112125" src="https://github.com/user-attachments/assets/0a3009b6-fac1-47ea-a3e5-7b58709050d0" />

**Why nothing works here:**
Unlike the previous levels, the input is properly sanitized using an **allow-list** rather than a blacklist. Instead of looking for and blocking known dangerous characters or commands, the application only accepts input that matches what is expected.

Because unexpected commands and characters are rejected, there is no useful way to inject another command through the input.


---
## 6. Summary Table
```
| Level | Protection | Bypass |
|-------|-----------|--------|
| Low | None | Direct injection |
| Medium | Blocks ` ; ` ` && ` | ` | ` |
| High | Blocks more chars including ` | ` | ` |` |
| Impossible | Input validation (allow-list) | None |
```

---


## My Key Takeaway:
Command injection taught me that user input can become dangerous when it is passed directly to the operating system. I learned how weak filters can sometimes be bypassed, while proper input validation and allow-lists can prevent unexpected commands from being executed. The main lesson is that security should not depend only on blocking known bad inputs; applications should carefully control and validate what users are allowed to submit.
