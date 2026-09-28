# Linux Walkthrough — Environment Variables

> **Category 3: Configuration & Context**
Environment variables allow the shell and programs to share configuration and contextual information.
They influence how commands and applications behave, where programs are found, what directories are used, and how scripts receive information from their environment.

---
# 🧠 The Mental Model — The Shell's Backpack

Imagine you're a worker entering a building.
Before starting work, the worker carries a backpack containing useful information:
* **Where am I?**
* **Who am I?**
* **Where should I look for tools?**
* **What configuration am I using?**
* **What information should I pass to the next worker?**

The shell does something similar.
It maintains variables that contain information and configuration.
Some variables exist only inside the current shell.
Others are **environment variables**, meaning they can be inherited by programs launched from that shell.

For example:

```bash
echo $HOME
```

might produce:

```text
/home/abdullah
```

And:

```bash
echo $PATH
```

shows the directories the shell searches when looking for executable commands.
The goal isn't simply to memorize `$PATH`, `$HOME`, or `$USER`.
It's to understand:

> **What information does my shell know, where did it come from, and which programs can access it?**

---
# 1. Orientation Commands
Before changing anything, first understand what's already available.

## 3.1 `env` — See the Environment
`env` displays environment variables and their values.

```bash
env
```

You may see variables such as:

```text
HOME=/home/abdullah
USER=abdullah
SHELL=/bin/bash
PATH=/usr/local/bin:/usr/bin:/bin
```

### Why does it matter?
It gives you a snapshot of information currently being passed through your environment.

### Takeaway
**`env` = See the environment available to your processes.**

---
## 3.2 `printenv` — Read a Specific Variable

```bash
printenv PATH
```

Or:

```bash
printenv HOME
```

### Why does it matter?
Instead of displaying everything, you can quickly retrieve one variable.
### Takeaway
**`printenv` = Look up a specific environment variable.**

---
## 3.3 `echo $VAR` — Display a Variable

```bash
echo $HOME
```

Example:

```text
/home/abdullah
```

Other examples:

```bash
echo $USER
echo $SHELL
echo $PATH
```

### Why does it matter?
Variables can be referenced directly inside shell commands and scripts.

### Takeaway
**`echo $VAR` = Read a variable's value inside the shell.**

<img width="1012" height="593" alt="Screenshot 2026-09-20 031204" src="https://github.com/user-attachments/assets/f7705563-65b5-40cd-a779-76c222e5f80a" />
<img width="1210" height="719" alt="Screenshot 2026-09-20 031233" src="https://github.com/user-attachments/assets/110afdf9-f4e3-4466-9c46-b30760de3f0d" />
<img width="543" height="160" alt="Screenshot 2026-09-20 031311" src="https://github.com/user-attachments/assets/3d9d1bd8-6baa-44d0-82bd-6be169f425bf" />

---
# 2. Creation & Setting Commands
Now that we know how to inspect variables, we can create and modify them.

## 3.4 `export` — Create an Environment Variable

```bash
export MY_VAR="hello"
```

Check it:

```bash
echo $MY_VAR
```

Because it was exported, a child process can inherit it.

For example:

```bash
export MY_VAR="hello"
bash
echo $MY_VAR
```

### Why does it matter?
Environment variables are commonly used to pass configuration from a shell to programs and scripts.

### Takeaway
**`export` = Make a shell variable available to child processes.**

---
## 3.5 `unset` — Remove a Variable

```bash
unset MY_VAR
```

Check:

```bash
echo $MY_VAR
```

### Why does it matter?
Variables that are no longer needed can be removed from the current shell environment.

### Takeaway
**`unset` = Remove a variable from the current shell.**

---
## 3.6 `set` — Inspect or Configure the Shell

`set` has several uses.

For example:

```bash
set -e
```

causes a shell script to exit when a command returns a non-zero status.
You can also inspect shell variables:

```bash
set | grep MY_VAR
```

### Why does it matter?
`set` allows you to inspect shell state and configure shell behavior.

### Takeaway
**`set` = Work with shell variables and shell behavior.**

---
## 3.7 `alias` — Create Command Shortcuts

```bash
alias ll='ls -la'
```

Now:

```bash
ll
```

runs:

```bash
ls -la
```

View existing aliases:

```bash
alias
```

### Why does it matter?
Aliases can make frequently used commands faster to type.

### Takeaway
**`alias` = Give a command a convenient shortcut.**

<img width="910" height="610" alt="Screenshot 2026-09-20 032930" src="https://github.com/user-attachments/assets/6abc48ae-1fd2-4a4f-80c5-8a25600e8a95" />
<img width="908" height="602" alt="Screenshot 2026-09-20 032958" src="https://github.com/user-attachments/assets/8864dd64-54e2-4091-8e12-8e508c88b9a6" />
<img width="913" height="582" alt="Screenshot 2026-09-20 032944" src="https://github.com/user-attachments/assets/7d91f288-f520-4b08-a292-f8b18ae8fa86" />

---

# 3. Viewing & Inspecting Variables
Once variables exist, we need ways to inspect them.

## 3.8 `echo` with Variables — Combine Data

```bash
echo "Welcome to $HOME"
```

Example:

```text
Welcome to /home/abdullah
```

You can combine multiple variables:

```bash
echo "User: $USER | Shell: $SHELL"
```

### Why does it matter?
This is one of the simplest ways to use variables dynamically in shell commands and scripts.

### Takeaway
**Variables allow commands to work with changing information instead of hard-coded values.**

---
## 3.9 `env | grep` — Filter the Environment

```bash
env | grep PATH
```

Or:

```bash
env | grep HOME
```

Here, `|` sends the output of `env` into `grep`.

### Why does it matter?
Real environments can contain many variables. Filtering makes specific information easier to find.

### Takeaway
**`env | grep` = Search through environment variables.**

---
## 3.10 `compgen -v` — List Shell Variables

```bash
compgen -v
```

This can list variable names available to the current Bash shell.

### Why does it matter?
It provides another way to inspect the variables known to the shell.

### Takeaway
**`compgen -v` = Enumerate shell variable names.**

---
## 3.11 `declare -p` — Inspect Variable Definitions

```bash
declare -p
```

You can inspect a specific variable:

```bash
declare -p PATH
```

### Why does it matter?
It provides more detailed information about how a shell variable is defined.

### Takeaway
**`declare -p` = Inspect how shell variables are defined.**

<img width="912" height="603" alt="Screenshot 2026-09-20 033643" src="https://github.com/user-attachments/assets/97d31389-4a8b-40cc-b6b6-66d6f2728f89" />
<img width="918" height="606" alt="Screenshot 2026-09-20 033656" src="https://github.com/user-attachments/assets/c68be70c-cf63-4f0b-965b-652ca61de261" />

---
# 4. Manipulation Commands
Environment information becomes especially useful when you're configuring the shell or writing scripts.

## 3.12 `export PATH=...` — Modify the Command Search Path

```bash
export PATH="$PATH:/usr/local/bin"
```

Check the result:

```bash
echo $PATH
```

### Why does it matter?
When you type a command such as:

```bash
python
```

the shell searches directories listed in `PATH` to locate an executable.
Adding a directory can make programs available from anywhere in the shell.

### Takeaway
**`PATH` = The shell's search map for executable commands.**

---
## 3.13 `source` / `.` — Load Configuration

```bash
source ~/.bashrc
```

The shorter equivalent is:

```bash
. ~/.bashrc
```

### Why does it matter?
Changes made to shell configuration files don't automatically affect an already-running shell. `source` executes the file in the **current shell**, allowing those changes to take effect there.

### Takeaway
**`source` = Apply shell configuration to the current session.**

---
## 3.14 `read` — Capture User Input

```bash
read -p "Enter name: " name
```

Then:

```bash
echo "Hello, $name"
```

### Why does it matter?
Scripts often need information from the person running them.
`read` allows that input to be stored in a variable.

### Takeaway
**`read` = Bring user input into a shell variable.**

<img width="926" height="453" alt="Screenshot 2026-09-20 034446" src="https://github.com/user-attachments/assets/9c5111b3-ab93-4213-a785-5cbc89e1ba46" />

---
# 5. Analysis Commands
Commands themselves can also be affected by the environment, aliases, functions, and `PATH`.

## 3.15 `type` — Identify a Command

```bash
type echo
```

You may get:

```text
echo is a shell builtin
```

Try:

```bash
type ls
```

You may see a path or information about an alias/function.

### Why does it matter?
The thing you type isn't necessarily a standalone executable.
It could be:

* a shell builtin
* an alias
* a function
* an external executable

### Takeaway
**`type` = Find out what the shell thinks a command is.**

---
## 3.16 `which` — Find an Executable

```bash
which python
```

Possible output:

```text
/usr/bin/python
```

### Why does it matter?
It helps identify which executable is found through the current `PATH`.

### Important note
`which` is useful, but it isn't the best tool for every command because shell builtins and aliases may not behave the way you expect.

### Takeaway
**`which` = Locate an executable found through `PATH`.**

---
## 3.17 `whereis` — Locate Related Files

```bash
whereis ls
```

It can report locations associated with a command, such as its binary and manual page.

### Why does it matter?
It can help you discover where a program and its documentation are located.

### Takeaway
**`whereis` = Find related program files and documentation.**

---
## 3.18 `command -v` — Resolve a Command

```bash
command -v ls
```

You can also try:

```bash
command -v cd
```

Unlike simply looking for files, this asks the shell how it would resolve the command.

### Why does it matter?
It's useful in shell scripts when you need to determine whether a command is available and how the shell resolves it.

### Takeaway
**`command -v` = Ask the shell how it resolves a command.**

<img width="560" height="437" alt="Screenshot 2026-09-20 034607" src="https://github.com/user-attachments/assets/2911bbd9-67d3-4559-8a93-9aaffceb7b6a" />

---
# 6. Advanced Commands
These commands demonstrate how shell behavior can become dynamic.

## 3.19 `eval` — Evaluate a Command

```bash
VAR='echo Hello'
eval "$VAR"
```

This causes the contents of the variable to be interpreted as shell code.

### Why does it matter?
`eval` enables dynamic command construction.
However, it should be used carefully because treating variable contents as shell code can create **command-injection risks** when untrusted input reaches it.

### Security takeaway
> **Never treat untrusted input as shell code simply because `eval` makes it possible.**
**`eval` = Ask the shell to interpret a string as code.**

---
## 3.20 `trap` — React to Signals and Events

```bash
trap 'echo "Cleaning up..."' EXIT
```

When the shell exits, the command runs.
Another example:

```bash
trap 'echo "Interrupted"' INT
```

### Why does it matter?
Scripts can use `trap` to perform cleanup, logging, or controlled responses when certain signals or shell events occur.

### Takeaway
**`trap` = Tell the shell what to do when a specific event occurs.**

<img width="596" height="535" alt="Screenshot 2026-09-20 035011" src="https://github.com/user-attachments/assets/141cacd5-1f19-48f0-b1bd-03d6d4369e3d" />
<img width="408" height="200" alt="Screenshot 2026-09-20 035245" src="https://github.com/user-attachments/assets/843113be-b85c-4bf0-bdd4-a55a1177b3e1" />

---

# 🔬 Practical Environment Lab
Now connect the concepts together.

```bash
echo $HOME
echo $USER
echo $SHELL

env | grep -E '^(HOME|USER|SHELL|PATH)='

export MY_PROJECT="linux-walkthrough"
echo $MY_PROJECT

bash -c 'echo "Child process sees: $MY_PROJECT"'

unset MY_PROJECT

alias ll='ls -la'
type ll

echo $PATH
command -v ls
which ls
whereis ls

read -p "Enter your name: " name
echo "Hello, $name"
```

Then practice configuration loading:

```bash
echo 'export LAB_NAME="Linux Walkthrough"' >> ~/.bashrc
source ~/.bashrc
echo $LAB_NAME
```

Remove the test configuration afterward if you don't want it permanently in your shell configuration:

```bash
sed -i '/export LAB_NAME="Linux Walkthrough"/d' ~/.bashrc
```

---

# 🔐 Why This Matters in Cybersecurity

Environment variables are more than shell conveniences.

They can influence how programs behave and where they find resources.

During security analysis, you may need to investigate:

* `PATH` configuration
* Shell configuration files
* Environment inherited by processes
* Application configuration
* Secrets accidentally placed in environment variables
* Command resolution
* Dangerous shell behavior
* Variables used by scripts and automation

For example, if a privileged process searches for an executable using an unsafe `PATH`, the environment can become security-relevant.

This leads to an important security mindset:

> **Configuration is part of the attack surface.**

A program can be perfectly written in isolation and still behave unexpectedly because of the environment surrounding it.

---

# 📸 Evidence

### Terminal Walkthrough

Document the practical lab with readable screenshots showing:

1. Inspecting environment variables
2. Creating and exporting variables
3. Modifying `PATH`
4. Resolving commands
5. Using variables inside a script or shell session

![Environment Variables Walkthrough](screenshots/03-environment-variables.png)

---

# 🧠 Key Takeaways

After this section, I should be able to:

* Understand shell and environment variables
* Inspect environment variables
* Create and remove variables
* Export variables to child processes
* Understand the purpose of `PATH`
* Modify shell configuration
* Use variables inside commands and scripts
* Capture user input
* Resolve commands through the shell
* Understand aliases and shell builtins
* Recognize why `eval` can be dangerous
* Use `trap` for shell-event handling

### The core idea

> **Environment variables provide context that the shell and its child processes can use to determine how commands and programs behave.**

