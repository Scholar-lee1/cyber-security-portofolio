# Linux Walkthrough — Processes

> **Category 5: Monitor & Control**
A Linux system is constantly running processes.

Programs you launch, services running in the background, shells, system components, and applications all execute as processes.

Process management is about understanding:
> **What is running? Who started it? What resources is it using? What is it doing? And how can I control it?**

---
# 🧠 The Mental Model — The Factory
Imagine a factory.
The factory has many workers performing different tasks.
A supervisor needs to know:

* Who is working?
* What task are they performing?
* Who assigned the task?
* How much equipment are they using?
* Which workers are waiting?
* Which workers should stop?
* Which workers should get more or less priority?

Linux processes work similarly.
A **process** is a running instance of a program.
Linux gives each process a **PID (Process ID)** so it can identify and manage it.
Processes can also create other processes, producing parent-child relationships.
So process management is essentially:

> **Observe → Understand → Control**

---
# 1. Orientation Commands
Before controlling processes, first learn how to see them.

## 5.1 `ps` — List Processes

```bash
ps
```

By default, this shows processes associated with your current shell/session.
A common broader view is:

```bash
ps aux
```

### Why does it matter?
You cannot safely manage a process you cannot identify.

### Takeaway
**`ps` = Take a snapshot of running processes.**

---
## 5.2 `pstree` — See Process Relationships

```bash
pstree
```

This displays processes as a tree.
You can see how processes relate to their parents and children.

### Why does it matter?
Programs can create other processes. Understanding these relationships becomes useful when investigating how software starts and behaves.

### Takeaway
**`pstree` = See the parent-child structure of processes.**

---
## 5.3 `jobs` — Track Shell Jobs

```bash
jobs
```

Example:

```text
[1]+  Running    python server.py &
[2]-  Stopped    nano notes.txt
```

`jobs` focuses on jobs controlled by your **current shell**, rather than every process on the system.

### Why does it matter?
It helps you manage commands you've started in the current terminal.

### Takeaway
**`jobs` = Track background and suspended jobs in your current shell.**

<img width="898" height="340" alt="Screenshot 2026-09-20 045601" src="https://github.com/user-attachments/assets/d3544835-c66a-41f1-8d21-905d61f373fc" />
<img width="905" height="423" alt="Screenshot 2026-09-20 045612" src="https://github.com/user-attachments/assets/5ace673c-4e91-4a26-9933-06e3fb665ef1" />
<img width="908" height="163" alt="Screenshot 2026-09-20 045716" src="https://github.com/user-attachments/assets/e046282a-994b-499b-bb7a-5e9df3f21ac0" />

---
# 2. Viewing Commands
Now move from basic snapshots to real-time observation and investigation.

## 5.4 `top` — Monitor Processes in Real Time

```bash
top
```

It displays continuously updating information about:
* CPU usage
* Memory usage
* Process IDs
* Running processes
* System load

Press `q` to exit.

### Why does it matter?
A static process list can become outdated quickly. `top` lets you watch the system as processes change.

### Takeaway
**`top` = Real-time process and resource monitoring.**

---
## 5.5 `htop` — Interactive Process Monitor

```bash
htop
```

`htop` provides an interactive interface for viewing and managing processes.
It may need to be installed separately:

```bash
sudo apt install htop
```

### Why does it matter?
It provides a more convenient interface for exploring processes and resource usage.

### Takeaway
**`htop` = An interactive alternative to `top`.**

---
## 5.6 `ps aux | grep` — Search a Process List

```bash
ps aux | grep python
```

This filters the output of `ps aux` for lines containing `python`.

### Why does it matter?
When many processes are running, filtering helps you quickly locate processes you're interested in.

### Important note
This technique can also match the `grep` command itself.
For more reliable process-name searching, tools such as `pgrep` are often preferable.

### Takeaway
**`ps aux | grep` = Filter a process listing to find something specific.**

---
## 5.7 `lsof` — See What Processes Have Open

```bash
lsof -p 1234
```

This shows files opened by process `1234`.
You can also inspect network-related open files:

```bash
sudo lsof -i :8080
```

### Why does it matter?
On Linux, many resources are represented through file descriptors.

`lsof` can help connect:

```text
Process → Files → Network resources
```

### Takeaway
**`lsof` = Discover resources currently opened by processes.**

---
## 5.8 `watch` — Repeat a Command

```bash
watch -n 1 'ps aux | grep python'
```

The command runs repeatedly and refreshes the output.

### Why does it matter?
It turns a normal command into a simple live monitor.

### Takeaway
**`watch` = Repeatedly execute a command and observe changes.**

<img width="316" height="64" alt="Screenshot 2026-09-20 045728" src="https://github.com/user-attachments/assets/2fbd7e80-89e2-4bfa-81d2-5867e8126b4e" />
<img width="338" height="53" alt="Screenshot 2026-09-20 050305" src="https://github.com/user-attachments/assets/c62faeb4-de57-4397-baa3-a262479eae07" />
<img width="883" height="215" alt="Screenshot 2026-09-20 050253" src="https://github.com/user-attachments/assets/1d288699-d776-4f0d-a96f-b636ba8a6579" />
<img width="899" height="228" alt="Screenshot 2026-09-20 050216" src="https://github.com/user-attachments/assets/1e7a05cb-2107-4b7c-ba5a-399b7dab678c" />
<img width="286" height="98" alt="Screenshot 2026-09-20 050027" src="https://github.com/user-attachments/assets/6cbeb9b5-6a36-491d-ae4f-04483d6eec5d" />
<img width="904" height="268" alt="Screenshot 2026-09-20 045905" src="https://github.com/user-attachments/assets/13bb8547-15d7-4463-92da-6c936c03a72b" />

---
# 3. Manipulation Commands
Once you understand what is running, you can control processes and shell jobs.

## 5.9 `kill` — Send a Signal to a Process

```bash
kill 1234
```

By default, `kill` sends the `TERM` signal, requesting that the process terminate gracefully.
A stronger signal is:

```bash
kill -9 1234
```

`SIGKILL` forces termination and cannot be caught or handled by the target process.

### Why does it matter?
Processes sometimes need to be stopped when they hang, consume excessive resources, or are no longer needed.

### Important note
`kill` doesn't necessarily mean "immediately destroy." It means **send a signal**.

### Takeaway
**`kill` = Send a signal to a process.**

---
## 5.10 `pkill` — Signal Processes by Name

```bash
pkill python
```

This can signal processes whose names match `python`.
For matching against the full command line:

```bash
pkill -f script.py
```

### Why does it matter?
You don't always know the PID of the process you want to manage.

### Warning
Be careful with broad patterns. A command such as:

```bash
pkill python
```

may affect multiple Python processes.

### Takeaway
**`pkill` = Send signals to processes based on matching criteria.**

---
## 5.11 `killall` — Signal Processes by Name

```bash
killall firefox
```

This sends a signal to processes matching the specified name.

### Why does it matter?
It provides another convenient way to manage multiple instances of the same program.

### Security mindset
Always understand what will match before sending a signal to multiple processes.

### Takeaway
**`killall` = Signal processes matching a program name.**

---
## 5.12 `bg` / `fg` — Control Shell Jobs
Suspend a running foreground job with:

```text
Ctrl+Z
```

Then:

```bash
bg %1
```

continues job 1 in the background.

Bring it back:

```bash
fg %1
```

### Why does it matter?
You can control whether a shell job occupies your terminal or continues running in the background.

### Takeaway
**`bg` / `fg` = Move shell jobs between background and foreground execution.**

<img width="333" height="531" alt="Screenshot 2026-09-20 051731" src="https://github.com/user-attachments/assets/2d155703-5e00-4242-b386-25ad10b5eac0" />

---
# 4. Analysis Commands
Now we move from observing *what* is running to understanding *how* processes interact with the operating system.

## 5.13 `strace` — Trace System Calls

```bash
strace ls
```

This traces system calls made by the program.

You may see calls related to:
* opening files
* reading data
* writing output
* creating processes
* networking
* memory management

### Why does it matter?
Applications don't directly perform every low-level operation themselves.
They interact with the kernel through system calls.

`strace` lets you observe that interaction.

### Takeaway
**`strace` = Observe a program's interaction with the Linux kernel.**

---
## 5.14 `/proc/[PID]/status` — Inspect Process Metadata
Linux exposes process information through `/proc`.

For example:

```bash
cat /proc/1234/status
```

You may find information about:
* Process state
* PID
* Parent PID
* Memory
* Threads
* User/group IDs

### Why does it matter?
It provides detailed information about an individual process directly through Linux's process filesystem.

### Takeaway
**`/proc/[PID]/status` = Inspect detailed kernel-provided information about a process.**

---

## 5.15 `uptime` — Check System Runtime and Load

```bash
uptime
```

Example:

```text
17:30:00 up 5 days, 3:24, 2 users, load average: 0.50, 0.40, 0.30
```

### Why does it matter?
It gives a quick overview of:

* How long the system has been running
* Number of logged-in users
* Recent load averages

### Takeaway
**`uptime` = Quick view of system runtime and load.**

---
## 5.16 `free` — Inspect Memory

```bash
free -h
```

This displays memory and swap information in human-readable units.

### Why does it matter?
Processes consume memory. Understanding available RAM and swap helps you interpret system performance.

### Takeaway
**`free` = Inspect system memory and swap usage.**

<img width="911" height="582" alt="Screenshot 2026-09-20 052150" src="https://github.com/user-attachments/assets/938c798f-816a-44f1-8840-2facefffe774" />
<img width="291" height="106" alt="Screenshot 2026-09-20 052615" src="https://github.com/user-attachments/assets/802e49a8-bbf0-4d0d-9768-b4504a772287" />
<img width="921" height="607" alt="Screenshot 2026-09-20 052158" src="https://github.com/user-attachments/assets/63a00a7d-9531-4694-9646-e538cc376d81" />
<img width="601" height="538" alt="Screenshot 2026-09-20 052703" src="https://github.com/user-attachments/assets/440818df-ae1a-4ec5-b0e8-2ae480b57510" />

---
# 5. Advanced Commands
These commands let you control execution priority, persistence, terminal sessions, and system services.

## 5.17 `nice` / `renice` — Adjust Process Priority
Start a command with a modified niceness value:

```bash
nice -n 10 command
```

Change the niceness of an existing process:

```bash
renice 5 -p 1234
```

A **higher niceness value generally means lower scheduling priority**.

### Why does it matter?
Not every process should compete equally for CPU resources.

### Important note
Increasing priority by using a negative niceness value generally requires appropriate privileges.

### Takeaway
**`nice` / `renice` = Influence CPU scheduling priority.**

---
## 5.18 `nohup` — Survive Terminal Hangups

```bash
nohup python script.py &
```

`nohup` makes a command ignore the hangup signal normally associated with a terminal closing.

### Why does it matter?
It can allow a long-running command to continue after you disconnect from a terminal.

### Important note
`nohup` is not a complete process-management system. For reliable long-running services, tools such as `systemd` are generally more appropriate.

### Takeaway
**`nohup` = Run a command so it can survive a terminal hangup.**

---
## 5.19 `screen` — Persistent Terminal Sessions

Create a session:

```bash
screen -S mysession
```

Detach from it:

```text
Ctrl+A, D
```

List sessions:

```bash
screen -ls
```

Reconnect:

```bash
screen -r mysession
```

### Why does it matter?
A remote SSH connection can disappear while you still need a terminal session to continue running.

`screen` allows you to detach and reconnect later.

### Takeaway
**`screen` = Maintain a persistent terminal session across disconnects.**

---
## 5.20 `systemctl` — Manage System Services

Check a service:

```bash
systemctl status ssh
```

Start one:

```bash
sudo systemctl start ssh
```

Enable it at boot:

```bash
sudo systemctl enable ssh
```

### Why does it matter?
Many Linux services run continuously in the background.

`systemctl` provides an interface for managing services controlled by `systemd`.

### Takeaway
**`systemctl` = Manage systemd services and their lifecycle.**

<img width="905" height="612" alt="Screenshot 2026-09-20 053104" src="https://github.com/user-attachments/assets/b97b6cd5-59b4-4a74-ac98-100ee6bf32f4" />
<img width="915" height="613" alt="Screenshot 2026-09-20 052936" src="https://github.com/user-attachments/assets/3416cf00-ac70-4b89-a8d9-eca3b9305e73" />
<img width="607" height="435" alt="Screenshot 2026-09-20 053125" src="https://github.com/user-attachments/assets/187334f9-a389-4387-a018-2b4a48e85ac2" />


---
# 🔬 Practical Process Lab
Use your Linux VM for this exercise.

Start a simple process:

```bash
sleep 300 &
```

Check your shell jobs:

```bash
jobs
```

Find the process:

```bash
ps
```

Or:

```bash
ps aux | grep sleep
```

Inspect its PID:

```bash
pgrep sleep
```

Observe the process:

```bash
ps -p $(pgrep sleep)
```

Inspect its `/proc` information:

```bash
cat /proc/$(pgrep sleep)/status
```

Then terminate it gracefully:

```bash
kill $(pgrep sleep)
```

Verify:

```bash
ps aux | grep sleep
```

You can also practice process monitoring:

```bash
watch -n 1 'ps -eo pid,ppid,comm,%cpu,%mem --sort=-%cpu | head'
```

Press:

```text
Ctrl+C
```

to stop `watch`.

---
# 🔐 Why This Matters in Cybersecurity
Processes are one of the most important things to understand when investigating a Linux system.

When analyzing a machine, you may ask:
* What processes are running?
* Which user started them?
* What parent process created them?
* What files are they accessing?
* What network resources are they using?
* How much CPU or memory are they consuming?
* What system calls are they making?
* Which services start automatically?

This creates another security chain:

```text
Program
   ↓
Process
   ↓
User
   ↓
Resources
   ↓
System calls
   ↓
Kernel
```

That relationship becomes extremely important when studying:
* Process enumeration
* Malware analysis
* Persistence
* Privilege escalation
* Incident response
* Service security
* Linux internals

The key security question becomes:
> **"What is this process doing, who started it, and what does it have access to?"**

---
# 📸 Evidence

### Terminal Walkthrough

Document the practical lab with readable screenshots showing:
1. Listing processes
2. Inspecting process relationships
3. Monitoring resource usage
4. Inspecting `/proc`
5. Starting and controlling a background process
6. Sending a signal to terminate it
7. Checking a system service

---
# 🧠 Key Takeaways

After this section, I should be able to:
* List running processes
* Understand PIDs
* Understand parent-child process relationships
* Monitor CPU and memory usage
* Find specific processes
* Inspect open files and resources
* Read process information through `/proc`
* Send signals to processes
* Manage shell jobs
* Adjust process priority
* Run persistent terminal sessions
* Understand system services
* Trace system calls

### The core idea
> **A Linux system is a collection of processes interacting with resources through the kernel. Understanding those processes means understanding what the system is actually doing.**

---
# 🎯 Walkthrough Complete

The five foundational categories are now:

| #  | Category              | Core Question                                      |
| -- | --------------------- | -------------------------------------------------- |
| 01 | File Management       | **Where are things and how do I manipulate them?** |
| 02 | Permissions           | **Who can access them?**                           |
| 03 | Environment Variables | **What context surrounds the commands?**           |
| 04 | User Administration   | **Who are the identities on the system?**          |
| 05 | Processes             | **What is actually running?**                      |

Together:

```text
FILES
  ↓
PERMISSIONS
  ↓
ENVIRONMENT
  ↓
USERS
  ↓
PROCESSES
```

This gives you a foundation for moving from basic Linux usage into **system enumeration, networking, scripting, privilege escalation, and security labs**.
