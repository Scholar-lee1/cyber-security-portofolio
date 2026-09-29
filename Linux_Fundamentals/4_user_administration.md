# Linux Walkthrough — User Administration

> **Category 4: Manage People & Access**
Linux can have multiple users, groups, and sessions operating on the same system.

User administration is how Linux answers questions such as:
> **Who exists on this system? Who are they? Which groups do they belong to? What can they access? And who is allowed to perform administrative actions?**

---
# 🧠 The Mental Model — A Building With Different People
Imagine a large building with many workers.
Everyone has an **identity card**.
Some workers belong to the:

* Engineering team
* Security team
* Administration team

Different teams have access to different rooms.
There are also supervisors who are allowed to perform actions ordinary workers cannot.

Linux works similarly:
* **User** → an individual identity
* **Group** → a collection of users
* **UID** → numerical identity of a user
* **GID** → numerical identity of a group
* **Password** → authentication credential
* **sudo** → authorized administrative access
* **su** → switch to another user identity

So user administration isn't simply about creating accounts.

It's about understanding:
> **Who exists, how identities are organized, and how those identities interact with access control.**

---
# 1. Orientation Commands
Before managing users, first understand your own identity and group membership.

## 4.1 `whoami` — Who Am I?

```bash id="5k8s2d"
whoami
```

Example:

```text id="8qf5l7"
abdullah
```

### Why does it matter?
Your current identity determines which permissions and privileges apply to your commands.

### Takeaway
**`whoami` = Quickly identify the account you're using.**

---
## 4.2 `id` — Understand Your Identity

```bash id="v6e9ak"
id
```

Example:

```text id="6a3q7s"
uid=1000(abdullah) gid=1000(abdullah) groups=1000(abdullah),27(sudo)
```

This shows information such as:
* UID
* Primary GID
* Supplementary groups

### Why does it matter?
A user's group membership can affect what resources they can access.

### Takeaway
**`id` = See your complete user and group context.**

---
## 4.3 `groups` — See Your Groups

```bash id="0d6n3m"
groups
```

Example:

```text id="8t0y6c"
abdullah sudo docker
```

### Why does it matter?
Groups allow Linux to manage permissions for multiple users efficiently.

### Takeaway
**`groups` = See the groups associated with your account.**

<img width="915" height="362" alt="Screenshot 2026-09-20 035503" src="https://github.com/user-attachments/assets/52aa5259-619c-451c-bdc2-7e2b1a3dd062" />

---
# 2. Creation Commands
Now we can create identities and groups.

## 4.4 `useradd` — Create a User
`useradd` is a lower-level utility for creating user accounts.

```bash id="4e9r1p"
sudo useradd -m -s /bin/bash newuser
```

Here:

* `-m` → create a home directory
* `-s /bin/bash` → assign Bash as the login shell

### Why does it matter?
It's useful for controlled and automated account creation.

### Takeaway
**`useradd` = Create a user account with explicitly defined settings.**

---

## 4.5 `adduser` — Create a User Interactively
On systems that provide it:

```bash id="u4b7qp"
sudo adduser john
```

It typically walks you through creating the account and setting information.

### Why does it matter?
It's generally easier for interactive account creation than manually specifying every option.

### Takeaway
**`adduser` = A friendlier interactive interface for creating users.**

---
## 4.6 `passwd` — Manage Passwords
Change your own password:

```bash id="g4x1me"
passwd
```

An administrator can change another user's password:

```bash id="l7d2s9"
sudo passwd username
```

### Why does it matter?
Passwords are one mechanism Linux can use to authenticate users.

### Takeaway

**`passwd` = Manage a user's password credential.**

---

## 4.7 `groupadd` — Create a Group

```bash id="2u4m6k"
sudo groupadd developers
```

A specific GID can also be assigned:

```bash id="y1q8vs"
sudo groupadd -g 1001 admins
```

### Why does it matter?
Groups allow permissions to be assigned to collections of users instead of configuring every user individually.

### Takeaway

**`groupadd` = Create a group for organizing access.**

<img width="497" height="544" alt="Screenshot 2026-09-20 040818" src="https://github.com/user-attachments/assets/111f08fb-cd54-4cc1-982d-4c023e5c904f" />
<img width="774" height="541" alt="Screenshot 2026-09-20 041057" src="https://github.com/user-attachments/assets/d3bdabc2-3d2a-44ab-889f-cffa5ba0941d" />
<img width="254" height="109" alt="Screenshot 2026-09-20 041111" src="https://github.com/user-attachments/assets/2475fd69-a62c-427c-9434-1a89358522bf" />
<img width="444" height="191" alt="Screenshot 2026-09-20 041355" src="https://github.com/user-attachments/assets/1e3290ac-66b2-4459-9eff-f783f17f2367" />
<img width="534" height="229" alt="Screenshot 2026-09-20 041811" src="https://github.com/user-attachments/assets/5a2986e9-8fa5-4fff-ab67-d50ad03e97d8" />
<img width="528" height="466" alt="Screenshot 2026-09-20 042529" src="https://github.com/user-attachments/assets/d47a69c0-510e-40c6-b01a-4eba8c1e093f" />

---
# 3. Viewing Commands
Creating users is only useful if you can understand the identities already present on the system.

## 4.8 `/etc/passwd` — View User Account Information

```bash id="v7y0dk"
cat /etc/passwd
```

Each line contains fields such as:

```text
username:x:UID:GID:comment:home:shell
```

For example:

```text id="l6x9sa"
abdullah:x:1000:1000::/home/abdullah:/bin/bash
```

### Important note
`/etc/passwd` contains account information, but **it does not normally contain users' actual password hashes** on modern Linux systems. Those are generally stored in `/etc/shadow`.

### Why does it matter?
It provides a view of local user accounts and their associated configuration.

### Takeaway
**`/etc/passwd` = A key source of local user-account information.**

---
## 4.9 `/etc/group` — View Group Information

```bash id="4v8t2c"
cat /etc/group
```

Entries contain information such as:

```text
groupname:GID:members
```

### Why does it matter?
It helps you understand which groups exist and which users are associated with them.

### Takeaway
**`/etc/group` = Inspect local group definitions and memberships.**

---

## 4.10 `who` — See Active Sessions

```bash id="m7g4rp"
who
```

Example:

```text id="5z1j0b"
abdullah pts/0 2026-09-29 15:30
```

### Why does it matter?
It shows users who currently have active login sessions.

### Takeaway
**`who` = See who is currently logged in.**

---
## 4.11 `w` — See Users and Activity

```bash id="2x5c8n"
w
```

It provides information about logged-in users and their current activity.

### Why does it matter?
It gives you more context than simply knowing who is logged in.

### Takeaway
**`w` = See active users and what their sessions are doing.**

<img width="501" height="354" alt="Screenshot 2026-09-20 042700" src="https://github.com/user-attachments/assets/6389b0b0-c4fa-403e-a91a-76fad5e37941" />
<img width="317" height="307" alt="Screenshot 2026-09-20 042710" src="https://github.com/user-attachments/assets/be77436c-8bc6-447a-b8e7-04960c4b0a74" />
<img width="770" height="288" alt="Screenshot 2026-09-20 042722" src="https://github.com/user-attachments/assets/3fa0a19d-48cd-4fa9-b89d-59d572187d1b" />

---
# 4. Manipulation Commands
User administration also means modifying and removing identities.

## 4.12 `usermod` — Modify a User
Add a user to a supplementary group:

```bash id="0e6y8w"
sudo usermod -aG sudo username
```

Change the user's shell:

```bash id="z3v6q1"
sudo usermod -s /bin/bash username
```

### Why does it matter?
User accounts change over time. `usermod` lets administrators modify existing accounts without recreating them.

### Important detail
With supplementary groups, `-aG` is important. Omitting `-a` can replace the user's existing supplementary group memberships.

### Takeaway
**`usermod` = Modify an existing user's configuration.**

---
## 4.13 `userdel` — Remove a User

```bash id="n8x4y2"
sudo userdel username
```

Remove the account and its home directory:

```bash id="q6w2r9"
sudo userdel -r username
```

### Why does it matter?
Inactive or unnecessary accounts can increase the system's attack surface.

### Takeaway
**`userdel` = Remove a user account when it is no longer needed.**

---
## 4.14 `groupdel` — Remove a Group

```bash id="k2f8w4"
sudo groupdel developers
```

### Why does it matter?
Unused groups should not remain unnecessarily on a system.

### Takeaway

**`groupdel` = Remove an unnecessary group.**

<img width="681" height="544" alt="Screenshot 2026-09-20 043703" src="https://github.com/user-attachments/assets/53ccd991-7633-47a5-a655-0fce00f81be4" />
<img width="432" height="319" alt="Screenshot 2026-09-20 043733" src="https://github.com/user-attachments/assets/99ba7703-ea89-45c2-aa1b-c94d9ac74546" />
<img width="434" height="430" alt="Screenshot 2026-09-20 043918" src="https://github.com/user-attachments/assets/cc2800ee-5955-40b1-b491-e8ef37db4767" />

---
# 5. Analysis Commands
User information can also be queried through the system's configured identity databases.

## 4.15 `getent` — Query Identity Databases
Query a user:

```bash id="7c4m9x"
getent passwd username
```

Query a group:

```bash id="d8v2k6"
getent group groupname
```

### Why does it matter?
Unlike directly reading `/etc/passwd` or `/etc/group`, `getent` uses the system's Name Service Switch configuration.

That means it can retrieve information from configured sources beyond local files, depending on the system.

### Takeaway
**`getent` = Ask the system's configured identity service for an entry.**

---
## 4.16 `lastlog` — Inspect Login History

```bash id="m3q7z1"
lastlog
```

For a specific user:

```bash id="p8n4k2"
lastlog -u username
```

### Why does it matter?
Login history can help with account auditing and investigation.

### Takeaway

**`lastlog` = Check recorded login information for users.**

---
## 4.17 `finger` — User Information Lookup
On systems where the `finger` utility is installed:

```bash id="x5v9c3"
finger username
```

It can display information about a user and their login activity.

### Why does it matter?
It can provide a convenient user-information lookup.

### Important note
`finger` is **not installed by default on many modern Linux distributions**, so treat it as an optional/legacy utility rather than a core Linux command.

### Takeaway
**`finger` = Query additional user information when the utility is available.**

<img width="488" height="536" alt="Screenshot 2026-09-20 044146" src="https://github.com/user-attachments/assets/32844b37-47d5-49a5-9ae8-be310b5b579c" />
<img width="625" height="78" alt="Screenshot 2026-09-20 044804" src="https://github.com/user-attachments/assets/6d1b391f-00de-4afd-8f71-72c7b4bd91ed" />
<img width="266" height="66" alt="Screenshot 2026-09-20 044818" src="https://github.com/user-attachments/assets/3ee52291-ab38-4a16-947b-c18b0a0e9390" />
<img width="601" height="123" alt="Screenshot 2026-09-20 045225" src="https://github.com/user-attachments/assets/60fee9d7-8105-4f68-8304-031c2ed6f5ce" />


---
# 6. Advanced Commands
Some administrative operations directly affect privilege and system access.

## 4.18 `sudo` — Perform Authorized Administrative Actions
Run a command with elevated privileges:

```bash id="r4y7m2"
sudo useradd newuser
```

View your sudo permissions:

```bash id="w6p2k8"
sudo -l
```

### Why does it matter?
Linux doesn't require every task to be performed as root.
`sudo` allows authorized users to perform specific privileged actions while remaining in their normal account.

### Takeaway
**`sudo` = Perform an authorized administrative action without permanently becoming root.**

---
## 4.19 `su` — Switch User
Switch to another account:

```bash id="j5n8c4"
su username
```

Start a login shell as another user:

```bash id="v2x6q9"
su - username
```

With no username:

```bash id="r7m3k1"
su -
```

This typically attempts to switch to the root account.

### Why does it matter?
Sometimes you need to operate within another user's identity and environment.

### Takeaway
**`su` = Switch your current shell to another user identity.**

---
## 4.20 `visudo` — Safely Edit Sudo Configuration

```bash id="c8y4p6"
sudo visudo
```

`visudo` edits the sudoers configuration while performing syntax checks before accepting changes.

### Why does it matter?
A mistake in sudo configuration can affect administrative access. `visudo` helps reduce the risk of saving invalid sudoers syntax.

### Takeaway
**`visudo` = Safely manage sudo authorization rules.**

<img width="732" height="517" alt="Screenshot 2026-09-20 045248" src="https://github.com/user-attachments/assets/298c9ffa-acff-4187-83a8-2bc353346654" />


---

# 🔬 Practical User Administration Lab
Use a dedicated Linux VM or other safe test environment for account-management exercises.

Create a test group:

```bash id="n2k7x5"
sudo groupadd linuxlab
```

Create a test user:

```bash id="q4m8v1"
sudo useradd -m -s /bin/bash labuser
```

Set its password:

```bash id="x9c3r6"
sudo passwd labuser
```

Add the user to the test group:

```bash id="v5j2d8"
sudo usermod -aG linuxlab labuser
```

Inspect the account:

```bash id="m8w4q2"
id labuser
getent passwd labuser
getent group linuxlab
```

Check active sessions:

```bash id="k6p3y9"
who
w
```

When finished, remove the test account and group:

```bash id="s4n7c2"
sudo userdel -r labuser
sudo groupdel linuxlab
```

---

# 🔐 Why This Matters in Cybersecurity
User administration is directly connected to security because **identity is one of the foundations of access control**.

When examining a Linux system, you may need to determine:
* Which accounts exist?
* Which accounts are active?
* Which users have administrative privileges?
* Which groups exist?
* Who belongs to privileged groups?
* Are there unnecessary accounts?
* When did accounts last log in?
* What sudo permissions are available?

This creates a chain:

```text
Identity
   ↓
Groups
   ↓
Authentication
   ↓
Permissions
   ↓
Privileges
   ↓
Access
```

A security mindset asks:
> **"Who can do what, and why?"**

That question connects user administration directly to later topics such as **privilege escalation, access control, system enumeration, and account security**.

---
# 📸 Evidence

### Terminal Walkthrough
Document the practical lab with readable screenshots showing:

1. Current user and groups
2. Creating a test user and group
3. Inspecting account information
4. Modifying group membership
5. Checking active sessions
6. Removing the test account

---

# 🧠 Key Takeaways
After this section, I should be able to:

* Identify the current user
* Understand UIDs and GIDs
* Inspect group memberships
* Create users and groups
* Manage passwords
* Inspect local account databases
* Modify and remove accounts
* Query users and groups through NSS
* Review login information
* Understand sudo authorization
* Switch user contexts
* Safely manage sudoers configuration

### The core idea
> **Linux security begins with identity: knowing who exists, who you are, which groups you're part of, and what privileges those identities have.**
