# Permissions & Access Control

> **Category 2: Control & Access**
Linux doesn't treat every user equally.
A file can exist on the system, but that doesn't mean everyone can read it, modify it, or execute it.
Permissions are the mechanism Linux uses to answer:
> **Who can access this resource, and what are they allowed to do?**

---

# 🧠 The Mental Model — Keys, Rooms & Access Cards
Imagine an office building.

There are different people inside:
* **Owner** → the person responsible for a room
* **Group** → a team of people who share access
* **Others** → everyone else
* **Read (`r`)** → you can look inside
* **Write (`w`)** → you can modify what's inside
* **Execute (`x`)** → you can use/run it

So when Linux shows:

```text
-rwxr-x---
```

you can think:

```text
Owner   → read + write + execute
Group   → read + execute
Others  → no access
```

Permissions are therefore not just numbers or letters.
They're an **access-control system**.

---
# 1. Understanding Your Identity
Before asking what you're allowed to access, you need to know **who you are**.

## 2.1 `whoami` — Who Am I?

```bash
whoami
```

Example:

```text
abdullah
```

### Why does it matter?
Your identity affects what you can access and which privileged actions you can perform.

### Takeaway
**`whoami` = Know which account you're operating as.**

---
## 2.2 `id` — What Is My Identity?

```bash
id
```

Example:

```text
uid=1000(abdullah) gid=1000(abdullah) groups=1000(abdullah),27(sudo)
```

This gives information such as:
* User ID (`uid`)
* Primary group ID (`gid`)
* Supplementary groups

### Why does it matter?
Two users may have different access because they belong to different groups.

### Takeaway
**`id` = See your identity and group memberships.**

---
## 2.3 `groups` — Which Groups Am I In?

```bash
groups
```

Example:

```text
abdullah sudo docker
```

### Why does it matter?
Group membership can grant access to files, devices, or administrative capabilities.

### Takeaway
**`groups` = See which access groups your account belongs to.**

<img width="645" height="355" alt="Screenshot 2026-09-18 050829" src="https://github.com/user-attachments/assets/28ded0ea-6138-4f9e-83bb-024b6ca1fab5" />


---

# 2. Reading Permission Information
Now that we know who we are, we can inspect what a file allows.

## 2.4 `ls -l` — Read the Permission Bits
```bash
ls -l notes.txt
```

Example:

```text
-rw-r--r-- 1 abdullah abdullah 42 Sep 28 14:30 notes.txt
```

The important part is:

```text
-rw-r--r--
```

Break it down:
```text
-   rw-   r--   r--
│    │     │     │
│    │     │     └── Others
│    │     └──────── Group
│    └────────────── Owner
└─────────────────── File type
```

### Why does it matter?
This is one of the fastest ways to understand who can read, modify, or execute a file.

### Takeaway
**`ls -l` = Quickly inspect ownership and permissions.**

---
## 2.5 `stat` — Inspect Permissions in Detail

```bash
stat notes.txt
```

You may see information such as:

```text
Access: (0644/-rw-r--r--)
Uid: (1000/abdullah)
Gid: (1000/abdullah)
```

### Why does it matter?
`ls -l` gives you a useful summary. `stat` gives you deeper metadata.

### Takeaway
**`stat` = Inspect the details behind a file.**

---
## 2.6 `namei` — Follow Permissions Through a Path

```bash
namei -l /home/user/file.txt
```

This displays the permissions and ownership of each component of the path.

### Why does it matter?
A file may have the correct permissions but still be inaccessible because one of its parent directories prevents traversal.

### Takeaway
**`namei` = Trace permissions through the entire path.**

<img width="642" height="454" alt="Screenshot 2026-09-18 050252" src="https://github.com/user-attachments/assets/24ec066e-faae-49da-a0e3-2f06e051d486" />
<img width="635" height="513" alt="Screenshot 2026-09-18 050327" src="https://github.com/user-attachments/assets/c9cc7580-c74f-4419-b0d5-a60bcf8aadba" />


---
# 3. Changing Basic Permissions
Now we move from **observing access** to **controlling access**.

## 2.7 `chmod` — Change Permissions
Numeric notation:

```bash
chmod 755 script.sh
```

Symbolic notation:

```bash
chmod u+x script.sh
```

The three permission positions represent:

```text
u = user/owner
g = group
o = others
```

And:

```text
r = read
w = write
x = execute
```

### Why does it matter?
Permissions determine what different users are allowed to do with a resource.

### Takeaway
**`chmod` = Change what users are allowed to do.**

---
## 2.8 `chmod` Symbolic Permissions — Make Precise Changes
Instead of replacing the entire permission set, you can modify specific permissions.

```bash
chmod u+x script.sh
chmod g-w notes.txt
chmod o-r secret.txt
```

Meaning:

```text
u+x  → give owner execute permission
g-w  → remove group write permission
o-r  → remove others' read permission
```

### Why does it matter?
Symbolic notation lets you make targeted permission changes without rewriting the entire mode.

### Takeaway
**Symbolic `chmod` = Change exactly what you intend.**

---
## 2.9 `umask` — Control Default Permissions
Check the current mask:

```bash
umask
```

Example:

```text
0022
```

The `umask` influences the permissions assigned to newly created files and directories.

### Why does it matter?
You don't want every newly created resource to automatically receive overly permissive access.

### Takeaway
**`umask` = Influence the default permissions of newly created resources.**


<img width="761" height="628" alt="Screenshot 2026-09-18 073053" src="https://github.com/user-attachments/assets/1161ac62-5ad9-4c7d-87ea-9e7a105e949f" />


---

# 4. Changing Ownership
Permissions aren't the only access-control mechanism.
Every file also has an **owner** and a **group**.

## 2.10 `chown` — Change Ownership

```bash
sudo chown newuser notes.txt
```

Change both owner and group:

```bash
sudo chown newuser:newgroup notes.txt
```

### Why does it matter?
Ownership determines which account is treated as the file's owner and which group is associated with it.

### Takeaway
**`chown` = Change who owns the resource.**

---
## 2.11 `chgrp` — Change Group Ownership

```bash
sudo chgrp developers notes.txt
```

### Why does it matter?
Group ownership determines which group is associated with the file's group permission set.

### Takeaway
**`chgrp` = Change the resource's group.**

<img width="651" height="456" alt="Screenshot 2026-09-18 055150" src="https://github.com/user-attachments/assets/08db4df7-0418-4e0e-af23-faec2962d4a7" />
<img width="643" height="487" alt="Screenshot 2026-09-18 055126" src="https://github.com/user-attachments/assets/fcef62b1-d36b-4ea5-b961-26121222c479" />
<img width="635" height="516" alt="Screenshot 2026-09-18 055100" src="https://github.com/user-attachments/assets/dea5d7b4-3424-4720-ace2-71667ea4e03f" />


---

# 5. Advanced Access Control
Basic `rwx` permissions aren't always enough.

## 2.12 `getfacl` — Inspect ACLs

```bash
getfacl notes.txt
```

ACLs can provide more granular access rules than the traditional owner/group/other model.

### Why does it matter?
You may need to give one specific user access without changing the file's owner or standard group permissions.

### Takeaway
**`getfacl` = See detailed access-control rules.**

---
## 2.13 `setfacl` — Set ACLs

For example:

```bash
setfacl -m u:username:rwx file.txt
```

This gives a specific user additional ACL permissions.

### Why does it matter?
ACLs allow more precise access control than basic permission bits alone.

### Takeaway
**`setfacl` = Give specific users or groups granular access.**

<img width="645" height="355" alt="Screenshot 2026-09-18 050829" src="https://github.com/user-attachments/assets/c2d542dd-d44a-4f31-b5b0-e5262a06e1af" />
<img width="761" height="628" alt="Screenshot 2026-09-18 073053" src="https://github.com/user-attachments/assets/28be8232-3a26-4374-b841-b40b5a06ee3a" />

---
# 6. Privileged Access
Some operations require more authority than a normal account has.

## 2.14 `sudo` — Execute With Elevated Privileges

```bash
sudo apt update
```

Or:

```bash
sudo chown root file.txt
```

### Why does it matter?
Linux restricts powerful operations. `sudo` allows authorized users to perform commands according to the system's sudo policy.

### Takeaway
**`sudo` = Temporarily perform an authorized privileged action.**

---
## 2.15 `su` — Switch User

```bash
su username
```

Start a login shell for another account:

```bash
su - username
```

With no username:

```bash
su -
```

typically attempts to switch to the root account.

### Why does it matter?
Sometimes you need to operate within another user's account context.

### Takeaway
**`su` = Switch to another user identity.**

---
# 7. Testing Access
Sometimes you don't need to change permissions.
You simply need to ask:

> "Can this account actually do this?"

## 2.16 `test` — Check File Access
```bash
test -r file.txt
```

Check whether it's writable:

```bash
test -w file.txt
```

Check whether it's executable:

```bash
test -x file.txt
```

You can use it in scripts:

```bash
if test -r file.txt; then
    echo "File is readable"
fi
```

### Why does it matter?
Scripts can make decisions based on whether a resource is accessible.

### Takeaway
**`test` = Ask whether a particular access condition is true.**

<img width="649" height="472" alt="Screenshot 2026-09-18 055247" src="https://github.com/user-attachments/assets/ecec44d4-052d-41c4-986c-5d4a6a7589b2" />
<img width="648" height="448" alt="Screenshot 2026-09-18 055231" src="https://github.com/user-attachments/assets/13dc3869-68a3-467c-b107-b8662d85b8e1" />
<img width="647" height="511" alt="Screenshot 2026-09-18 055210" src="https://github.com/user-attachments/assets/b2994387-1323-4327-8c6b-59afb467fe94" />

---
# 8. Searching for Permission Patterns
Permissions become particularly interesting when you're analyzing an entire filesystem.

## 2.17 `find` — Search by Permissions
Find files with a specific permission mode:

```bash
find . -perm 644
```

Find files with a permission bit such as SUID:

```bash
find / -perm /u+s 2>/dev/null
```

### Why does it matter?
You can search large filesystems for files that match specific permission characteristics.

### Security relevance
Permission enumeration is an important part of Linux security assessment.

### Takeaway
**`find` + permissions = Search the filesystem for access-control patterns.**

<img width="684" height="804" alt="Screenshot 2026-09-18 072000" src="https://github.com/user-attachments/assets/57834875-1a58-43fa-ae42-c7ac20caa5bb" />
<img width="757" height="863" alt="Screenshot 2026-09-18 071933" src="https://github.com/user-attachments/assets/33351ae1-9233-49de-a193-6c281735a8a0" />

---
# 9. Understanding Special Permissions
Linux has more than ordinary `rwx` permissions.

## 2.18 SUID — Execute With Owner Identity
SUID is a special permission bit that can cause an executable to run with the privileges of its owner.

Find SUID files:

```bash
find / -perm /u+s 2>/dev/null
```

### Why does it matter?
SUID can be legitimate and necessary, but unexpected or poorly configured SUID programs can become a security concern.

### Takeaway
**SUID = A special permission that affects the identity under which an executable runs.**

---
## 2.19 SGID — Group-Based Special Permission
SGID can have different effects depending on whether it is applied to a file or directory.
For directories, newly created files can inherit the directory's group.
Find SGID files:

```bash
find / -perm /g+s 2>/dev/null
```

### Why does it matter?
SGID can be useful for shared directories and group-based workflows, but it also needs to be understood during security analysis.

### Takeaway
**SGID = A special permission associated with group privileges and inheritance behavior.**

---
## 2.20 Sticky Bit — Control Deletion in Shared Directories
The sticky bit is commonly used on shared directories such as `/tmp`.
Inspect it:

```bash
ls -ld /tmp
```

You may see:

```text
drwxrwxrwt
```

The final `t` represents the sticky bit.

### Why does it matter?
In a shared writable directory, the sticky bit helps prevent users from deleting or renaming files they don't own.

### Takeaway
**Sticky bit = Add deletion protection to shared writable directories.**

---

# 🔬 Practical Permission Lab
Instead of practicing the commands independently, connect them into one investigation.

```bash
mkdir permission_lab
cd permission_lab

touch notes.txt

whoami
id
groups

ls -l notes.txt
stat notes.txt

chmod 640 notes.txt
ls -l notes.txt

chmod u+x notes.txt
ls -l notes.txt

mkdir shared
chmod 770 shared

namei -l "$(pwd)/notes.txt"

getfacl notes.txt
```

Then experiment with ownership and access using a safe lab environment:

```bash
sudo chown "$USER":"$USER" notes.txt
```

Finally inspect special permissions on the system:

```bash
find / -perm /u+s 2>/dev/null
find / -perm /g+s 2>/dev/null
```

---

# 🔐 Why This Matters in Cybersecurity
Permissions are one of the foundations of Linux security.
When assessing a system, you may need to understand:
* Who owns a file?
* Which groups have access?
* Can another user read sensitive information?
* Can a process modify a configuration file?
* Which files have special permission bits?
* Can a user execute something with elevated privileges?
* Why can one account access something while another cannot?

This leads directly into concepts such as:
**Identity → Permissions → Groups → Privilege → Access Control → Privilege Escalation**
The goal isn't to memorize:

```text
755
644
640
```

The real skill is being able to look at a permission configuration and reason:

> **"Who can do what here, and what happens if that access is too broad or too restrictive?"**

---

# 📸 Evidence

### Terminal Walkthrough
Document the permission lab with readable screenshots showing:

1. Your identity and groups
2. Permission changes with `chmod`
3. Ownership and metadata
4. ACL inspection
5. Permission enumeration

---
# 🧠 Key Takeaways
After this section, I should be able to:

* Read Linux permission notation
* Understand owner, group, and other permissions
* Identify my current user and groups
* Change permissions with `chmod`
* Understand numeric and symbolic permissions
* Change ownership and group ownership
* Understand `umask`
* Inspect and modify ACLs
* Use `sudo` and `su` appropriately
* Test access programmatically
* Search for permission patterns
* Understand SUID, SGID, and sticky bit

### The core idea

> **Linux access control is about identity, ownership, permissions, and privilege — understanding how these interact is essential to understanding Linux security.**
