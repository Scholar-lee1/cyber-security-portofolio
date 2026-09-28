# File Management

> **Category 1: The Foundation**

Linux is built around the filesystem. Before working with permissions, users, processes, networking, or security tools, you need to understand how to move through the filesystem and work with the things inside it.

---
## The Mental Model — A Filing System

Imagine you're working in a huge office.
The office has:
* **Rooms** → directories
* **Documents** → files
* **Copies of documents** → backups
* **Cabinets** → directories containing other directories
* **Your current room** → your current working directory

Now imagine someone tells you:
> "Go to the engineering room, find the aircraft report, make a copy, inspect it, then archive the entire folder."
You need different actions for each step.

Linux works the same way.

* `pwd` → **Where am I?**
* `ls` → **What's here?**
* `cd` → **Move somewhere else**
* `mkdir` → **Create a folder**
* `touch` → **Create a file**
* `cp` → **Make a copy**
* `mv` → **Move or rename**
* `rm` → **Remove**
* `find` → **Locate something**
* `file` → **Identify what something actually is**
* `stat` → **Inspect its metadata**
* `tar` → **Bundle things together**
* `gzip` → **Compress them**

The goal isn't to memorize isolated commands.
It's to understand **how Linux manages information.**

---
# 1. Orientation

Before manipulating anything, first understand **where you are and what's around you.**

## 1.1 `pwd` — Where Am I?

`pwd` stands for **Print Working Directory**.
It shows the absolute path of your current location.

```bash
pwd
```

Example:

```text
/home/user/linux-lab
```

### Why does it matter?
Linux commands often operate relative to your current location.
If you don't know where you are, you can easily work on the wrong file or directory.

### Takeaway
**`pwd` = Know your location before you act.**

---
## 1.2 `ls` — What's Here?
`ls` lists files and directories.

```bash
ls
```

A more detailed view:

```bash
ls -la
```

### Why does it matter?
Before working with a directory, you usually want to know what's inside it.

### Takeaway
**`ls` = See what's around you.**

---
## 1.3 `cd` — Move Around
`cd` stands for **Change Directory**.

```bash
cd Documents
```

Move one level up:

```bash
cd ..
```

Return to your home directory:

```bash
cd ~
```

### Why does it matter?
The filesystem contains many directories. `cd` lets you move between them.

### Takeaway
**`cd` = Move through the filesystem.**

<img width="643" height="434" alt="Screenshot 2026-09-18 024544" src="https://github.com/user-attachments/assets/eca19b8d-6f49-47e3-85ff-6bf664c86d78" />

---



# 2. Creating Files & Directories
Once you've reached the right location, you can create the things you need.

## 1.4 `mkdir` — Create a Directory
`mkdir` stands for **Make Directory**.

```bash
mkdir my_project
```

Create nested directories:

```bash
mkdir -p parent/child/nested
```

### Why does it matter?
Directories provide structure for organizing files and projects.

### Takeaway
**`mkdir` = Create a new place to organize things.**

---
## 1.5 `touch` — Create a File

`touch` can create an empty file.

```bash
touch notes.txt
```

It can also update a file's timestamps if the file already exists.

### Why does it matter?
It gives you a quick way to create files directly from the terminal.

### Takeaway
**`touch` = Create a file without opening an editor.**

---
## 1.6 `cp` — Make a Copy
`cp` copies files or directories.

```bash
cp notes.txt notes_backup.txt
```

For directories:

```bash
cp -r folder/ backup_folder/
```

### Why does it matter?
Copies are useful when you want to preserve an original before making changes.

### Takeaway
**`cp` = Duplicate before you modify.**

---
## 1.7 `nano` — Edit a File
`nano` is a simple terminal text editor.

```bash
nano notes.txt
```

If the file doesn't exist, `nano` can create it.
Common controls:

```text
Ctrl + O    Save
Ctrl + X    Exit
```

### Why does it matter?
Not everything requires a graphical text editor. Linux lets you create and modify files directly from the terminal.

### Takeaway
**`nano` = Edit files without leaving the terminal.**

<img width="647" height="517" alt="Screenshot 2026-09-18 025843" src="https://github.com/user-attachments/assets/34d2a275-1fa4-438a-85ad-517f8401532c" />
<img width="643" height="513" alt="Screenshot 2026-09-18 030655" src="https://github.com/user-attachments/assets/d6bc9258-2f2d-4462-9f28-c191e9ebd044" />

---
# 3. Viewing File Contents
Creating a file is only half the job. You also need to inspect what's inside.

## 1.8 `cat` — Read the File
`cat` displays file contents.

```bash
cat notes.txt
```

### Why does it matter?
It's one of the quickest ways to inspect a small text file.

### Takeaway
**`cat` = Read the contents directly.**

---
## 1.9 `less` — Read Large Files
`less` lets you view a file page by page.

```bash
less bigfile.log
```

Useful controls:

```text
Space   Move forward
b       Move backward
q       Quit
```

### Why does it matter?
Large files can produce overwhelming terminal output. `less` lets you inspect them more comfortably.

### Takeaway
**`less` = Inspect large files without dumping everything at once.**

---
## 1.10 `head` — See the Beginning
`head` displays the first lines of a file.

```bash
head logfile.txt
```

Show the first five lines:

```bash
head -n 5 logfile.txt
```

### Why does it matter?
Sometimes you only need a quick look at the beginning of a file.

### Takeaway
**`head` = Get a quick look at the beginning.**

---
## 1.11 `tail` — See the End
`tail` displays the last lines of a file.

```bash
tail logfile.txt
```

Follow new content as it appears:

```bash
tail -f logfile.txt
```

### Why does it matter?
Log files constantly change. `tail -f` can be useful when monitoring them in real time.

### Takeaway
**`tail` = See what's happening at the end of a file.**


<img width="640" height="471" alt="Screenshot 2026-09-18 032619" src="https://github.com/user-attachments/assets/1be8c85b-f919-4737-a5d9-0d860bf8fcc3" />
<img width="646" height="342" alt="Screenshot 2026-09-18 032710" src="https://github.com/user-attachments/assets/6dd6759a-df64-4e69-8366-e870cb3fe9fd" />
<img width="630" height="227" alt="Screenshot 2026-09-18 032742" src="https://github.com/user-attachments/assets/66f44f8e-fbb2-413a-bd2c-48a4e272508b" />

---
# 4. Moving, Renaming & Removing
Now we can manipulate the filesystem.

## 1.12 `mv` — Move or Rename
`mv` can move a file:

```bash
mv file.txt /path/to/directory/
```

Or rename it:

```bash
mv oldname.txt newname.txt
```

### Why does it matter?
The same command handles both movement and renaming.

### Takeaway
**`mv` = Change where something is or what it's called.**

---
## 1.13 `rm` — Remove
`rm` removes files.

```bash
rm notes.txt
```

Remove a directory and its contents:

```bash
rm -r folder/
```

### Why does it matter?
Removing unnecessary files keeps the filesystem organized.

### ⚠️ Be careful
`rm` normally does not move files to a recycle bin. Deleted files may not be easily recoverable.

### Takeaway
**`rm` = Remove something deliberately, not casually.**

---
## 1.14 `rename` — Batch Rename
Some Linux distributions provide `rename` as a separate utility for renaming multiple files.

For example:

```bash
rename 's/old/new/' *.txt
```

### Why does it matter?
Renaming files individually becomes inefficient when dealing with many files.

### Note
`rename` implementations can differ between Linux distributions. Learn the version available on your system before relying on a particular syntax.

### Takeaway
**`rename` = Rename multiple files efficiently.**

<img width="638" height="465" alt="Screenshot 2026-09-18 040709" src="https://github.com/user-attachments/assets/fe8cb42b-8f1a-47ba-b28a-b957c8171231" />
<img width="640" height="510" alt="Screenshot 2026-09-18 040738" src="https://github.com/user-attachments/assets/e3e9ca40-0cb7-44e1-9a5b-2752b0c4a6b4" />
<img width="637" height="281" alt="Screenshot 2026-09-18 042453" src="https://github.com/user-attachments/assets/9c00190a-2a4d-4b5c-b98a-4c33bd113f6f" />

---
# 5. Searching & Inspecting
Sometimes you don't know exactly what a file is or where it is.
That's where filesystem analysis becomes useful.

## 1.15 `find` — Locate Files
`find` searches directories based on conditions.
Search by name:

```bash
find . -name "*.txt"
```

Search for files larger than 1 MB:

```bash
find . -type f -size +1M
```

### Why does it matter?
You won't always know where a file is. `find` lets you search based on properties such as name, type, size, and timestamps.

### Takeaway
**`find` = Search the filesystem systematically.**

---
## 1.16 `file` — Identify the Real File Type
`file` determines the type of data contained in a file.

```bash
file document.pdf
```

Example:

```text
document.pdf: PDF document
```

### Why does it matter?
A filename extension isn't proof of what a file actually contains.

### Takeaway
**`file` = Ask the system what a file actually is.**

---
## 1.17 `stat` — Inspect Metadata
`stat` provides detailed information about a file.

```bash
stat notes.txt
```

It can show information such as:

* Size
* Permissions
* Ownership
* Inode
* Access time
* Modification time
* Metadata change time

### Why does it matter?
Sometimes the contents aren't enough. You also need to understand the file's metadata.

### Takeaway
**`stat` = Look beneath the surface of a file.**

---
## 1.18 `wc` — Measure File Contents
`wc` counts things such as lines, words, and bytes.

```bash
wc notes.txt
```

Count only lines:

```bash
wc -l notes.txt
```

### Why does it matter?
It provides quick statistics about text files.

### Takeaway
**`wc` = Measure the contents of a file.**

<img width="782" height="534" alt="Screenshot 2026-09-18 042556" src="https://github.com/user-attachments/assets/9cbcc365-6d75-4eaf-b209-8553d88dfc58" />

---

# 6. Archiving & Compression
Sometimes you need to package or reduce data.

## 1.19 `tar` — Bundle Files Together
Create an archive:

```bash
tar -cvf archive.tar folder/
```

Extract it:

```bash
tar -xvf archive.tar
```

### Why does it matter?
An archive combines multiple files and directories into one package, making them easier to store or transfer.

### Takeaway
**`tar` = Bundle multiple things into one archive.**

---
## 1.20 `gzip` — Compress Data
Compress a file:

```bash
gzip file.txt
```

This produces:

```text
file.txt.gz
```

Decompress it:

```bash
gunzip file.txt.gz
```

### Why does it matter?
Compression reduces the amount of storage space needed and can make transfers more efficient.

### Important distinction
**Archiving** and **compression** are not the same thing.

* `tar` → combines files
* `gzip` → compresses data

They are often used together:

```bash
tar -czvf archive.tar.gz folder/
```

### Takeaway
**`gzip` = Reduce the size of data.**

<img width="641" height="415" alt="Screenshot 2026-09-18 042648" src="https://github.com/user-attachments/assets/08733dfa-ba8e-4573-81c5-d95e230a81a6" />
<img width="649" height="472" alt="Screenshot 2026-09-18 042707" src="https://github.com/user-attachments/assets/4522326e-59e7-48e5-9db6-1771b072569b" />
<img width="652" height="507" alt="Screenshot 2026-09-18 042726" src="https://github.com/user-attachments/assets/f84771ac-c2c2-469d-95a1-260b5b2f3069" />


---

# 🔬 Practical Walkthrough
Instead of practicing these commands as isolated examples, connect them into one filesystem workflow.

```bash
pwd

mkdir linux_lab
cd linux_lab

touch notes.txt
nano notes.txt

cat notes.txt
head notes.txt
tail notes.txt

cp notes.txt notes_backup.txt
mv notes_backup.txt backup.txt

ls -la

file notes.txt
stat notes.txt
wc notes.txt

find . -type f

mkdir archive
mv backup.txt archive/

tar -cvf archive.tar archive/
gzip archive.tar

ls -lh
```

The workflow represents a basic lifecycle:

```text
Navigate
   ↓
Create
   ↓
Edit
   ↓
Read
   ↓
Copy
   ↓
Move
   ↓
Inspect
   ↓
Search
   ↓
Archive
   ↓
Compress
```

---

# 🔐 Why This Matters in Cybersecurity
Filesystem knowledge is foundational to Linux security.
When working on a Linux system, security tasks may involve:

* Locating configuration files
* Searching for sensitive files
* Inspecting logs
* Checking file metadata
* Identifying unusual files
* Understanding where applications store data
* Investigating filesystem changes
* Preparing evidence for analysis

Later, concepts such as **permissions, privilege escalation, persistence, log analysis, and system enumeration** build directly on this foundation.
The important skill isn't simply knowing that `find` exists.

It's being able to think:
> **"I need to locate something. What information do I have about it, and which filesystem tool can use that information?"**

That's the mindset we're building.

---

# 📸 Evidence

### Terminal Walkthrough
The commands above were executed in a Linux environment and documented through terminal screenshots.

---

# 🧠 Key Takeaways

After this section, I should be able to:

* Navigate the Linux filesystem
* Create files and directories
* Read and edit files from the terminal
* Copy, move, rename, and remove files
* Search for files using different conditions
* Identify file types
* Inspect filesystem metadata
* Measure file contents
* Archive and compress data

### The core idea

> **Linux filesystem work is about knowing where things are, understanding what they are, and deliberately controlling what happens to them.**

