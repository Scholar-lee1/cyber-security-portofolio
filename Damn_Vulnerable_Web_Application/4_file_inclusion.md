# File Inclusion

## Overview

```
Imagine you have a folder with different documents, and you ask someone:
“Open this document for me.”
Instead of checking which document you're allowed to open, they simply open whatever file name you give them.
If you give them the path to a different file, they may open that one too.
That's the basic idea behind File Inclusion.

What is it?
👉 Tricking a web application into loading a file that it wasn't supposed to load.
Simple example:
A website has different pages, such as:
“Home | About | Contact”
Behind the scenes, the application may use a file parameter to decide which page to load.
If that parameter isn't properly checked, someone may change the input and make the application load another file.

Why does it happen?
Because the application trusts user-controlled input when deciding which file to include, without properly validating or restricting the file path.

What are we looking for?
We're basically checking:
“Can I make this application load a file that it wasn't intended to load?”

There are two main types:

LFI (Local File Inclusion)
👉 The application is tricked into loading a file that already exists on the local server.
RFI (Remote File Inclusion)
👉 The application is tricked into loading a file from another server.

So remember:
File Inclusion = User input tricks a web application into loading an unintended file.
```
So technically,
File Inclusion is a vulnerability where an application uses user-controlled input to determine which files it loads, 
without sufficient validation or access controls. 
This can potentially expose sensitive files or, in some cases, lead to code execution.

Risk Level: High to Critical (depending on the vulnerability and impact)


---
## 1. Interface
<img width="677" height="342" alt="Screenshot 2026-09-20 152023" src="https://github.com/user-attachments/assets/e902d653-bf64-4f77-9227-23b82af4ce0a" />

Brief: What the form does (The File Inclusion interface has three links, each displaying a different file from the web application. 
Clicking a link sends a GET request with a parameter that tells the application which file to display.)

file1.php
<img width="659" height="273" alt="Screenshot 2026-09-20 153936" src="https://github.com/user-attachments/assets/dc7c6154-7cca-4337-b01f-d04ddc6b8562" />

file2.php
<img width="645" height="289" alt="Screenshot 2026-09-20 154008" src="https://github.com/user-attachments/assets/6a50894e-8826-47ba-9b5a-57fc26d7859e" />

file3.php
<img width="662" height="333" alt="Screenshot 2026-09-20 154031" src="https://github.com/user-attachments/assets/a4b173ac-1881-4b18-bd2f-233350c4b821" />


---
## 2. Level: LOW

### Source Code
<img width="371" height="289" alt="Screenshot 2026-09-20 154138" src="https://github.com/user-attachments/assets/7c8d9c4d-29af-4e5e-ab15-e3301ce080a2" />

What makes it exploitable
Reviewing the PHP code shows that the application takes the value of the page parameter and uses it to determine which file to display. The input is not properly validated or restricted, 
meaning we can manipulate the filename and make the application load files that were not originally intended to be displayed.
Key point: The application trusts the user's page input too much, which makes the file inclusion possible.

### Exploitation
What happened
Seeing the vulnerability, we can manipulate the page parameter to make the application load a file that wasn't originally intended to be displayed. 
By using a path such as ../../../../../../etc/passwd, we can access the contents of the system's /etc/passwd file.

```text
http://localhost/DVWA/vulnerabilities/fi/?page=../../../../../../etc/passwd
```

<img width="919" height="753" alt="Screenshot 2026-09-20 154442" src="https://github.com/user-attachments/assets/b9076b88-dd6b-41ac-9b6b-6296d82b5820" />

For the second payload, we use PHP's php://filter wrapper to Base64-encode the contents of a PHP file before displaying it. 
This allows us to view the source code rather than having the PHP file execute normally.
After decoding the Base64 output, we can see the original PHP source code.

```text
http://localhost/DVWA/vulnerabilities/fi/?page=php://filter/convert.base64-encode/resource=../../../../../var/www/html/hackable/flags/fi.php
```

<img width="922" height="661" alt="Screenshot 2026-09-20 230042" src="https://github.com/user-attachments/assets/430da39c-2f79-4492-88d4-b7f34bcbeee0" />

Key takeaway: The vulnerability exists because the application trusts the page parameter without properly restricting which files can be loaded.


---
### 3. LEVEL: MEDIUM

### Source Code

<img width="564" height="377" alt="Screenshot 2026-09-20 230235" src="https://github.com/user-attachments/assets/6a19d6d6-cf06-4932-a537-6e08c454bf25" />

How it differs from Low:
A filter has now been added. It removes `http://` and `https://` to reduce the possibility of Remote File Inclusion (RFI), 
and it also removes `../` and `..\` to prevent directory traversal.

### Why Low's payload fails now
Our Low-level payload used `../` to move through directories and reach files outside the intended location. 
Since the application now removes `../`, that straightforward path no longer works.

### How we exploit it anyway
The filter only removes the pattern once and does not check the input recursively. 
By using a repeated pattern such as:

```text
....//....//....//....//....//etc/passwd
```

the filter removes the inner `../` patterns, leaving:

```text
../../../../../etc/passwd
```

This allows us to bypass the filter and reach the intended file.
We can also use the PHP filter technique from the Low level with this bypass to read PHP source code in Base64 format.

### Exploitation
<img width="910" height="437" alt="Screenshot 2026-09-20 230346" src="https://github.com/user-attachments/assets/b5014ac7-65c9-4ca4-8b96-1b08fc5dd1ae" />
<img width="905" height="416" alt="Screenshot 2026-09-20 230435" src="https://github.com/user-attachments/assets/2e157906-f7e4-4f13-9dad-c95c7e8f920a" />

**Key takeaway:** The filter attempts to block directory traversal, but because it is not applied recursively, the input can still be manipulated to produce a valid traversal path.


---
## 4. Level: HIGH

### Source Code
<img width="516" height="393" alt="Screenshot 2026-09-20 230531" src="https://github.com/user-attachments/assets/558a86ac-346d-48ad-b4bb-6c7c96c78899" />

How it differs from Medium:
The filter is stronger. Instead of simply removing certain characters, 
the application now checks whether the requested file follows a specific pattern. 
It uses `fnmatch()` to check whether the filename starts with `file`, while also treating `include.php` as a special case.

Why Medium's bypass fails now
The Medium-level bypass relied on manipulating `../` to get around the filter. 
At this level, the application performs additional checks on the filename, 
so that technique no longer works in the same way.

How we exploit it anyway
The validation only checks the **beginning** of the filename. 
It does not properly restrict what comes after `file`.

For example:

```text
http://localhost/DVWA/vulnerabilities/fi/?page=file/../../../../../../../etc/passwd
```

The input begins with `file`, so it passes the check, while the rest of the path allows the application to reach another file.
We can also use the `file://` protocol:

```text
http://localhost/DVWA/vulnerabilities/fi/?page=file:///etc/passwd
```

This shows that even with stronger validation, incomplete input restrictions can still leave the application vulnerable to File Inclusion.

### Exploitation

<img width="906" height="595" alt="Screenshot 2026-09-20 230610" src="https://github.com/user-attachments/assets/1cd96516-0fd5-4a1b-9d00-6adeebd76176" />
<img width="916" height="583" alt="Screenshot 2026-09-20 230705" src="https://github.com/user-attachments/assets/2170a194-4d1f-461d-b459-e9f12151eb59" />
<img width="920" height="425" alt="Screenshot 2026-09-20 230733" src="https://github.com/user-attachments/assets/72421987-96e5-4d09-bf02-317e051d3452" />

**Key takeaway:** The filter checks how the filename starts, but does not sufficiently control the complete file path.


---
## 5. Level: IMPOSSIBLE

### Source Code

<img width="398" height="496" alt="Screenshot 2026-09-20 230923" src="https://github.com/user-attachments/assets/96191ebd-aabf-488a-b42f-22325fce149b" />

Why nothing works here
Unlike the previous levels, the application uses an **allow-list**. Instead of trying to block dangerous input, it only accepts specific, legitimate page values.

Because the `page` parameter can only contain expected values, we cannot manipulate it to load an unintended file. 
Therefore, the application is **not vulnerable to File Inclusion** at this level.


---
## 6. Summary Table
```
| Level      | Protection                                     | Bypass                        |
| ---------- | ---------------------------------------------- | ----------------------------- |
| Low        | No input validation                            | Direct path traversal         |
| Medium     | Blocks `http://`, `https://`, `../`, and `..\` | Non-recursive filter bypass   |
| High       | Checks that the filename starts with `file`    | Path manipulation / `file://` |
| Impossible | Allow-list of legitimate page values           | None                          |
```







