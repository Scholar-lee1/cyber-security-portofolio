# SQL Injection — Blind

## Overview

```
Imagine you ask a security guard:
“Is the password longer than 10 characters?”
The guard doesn't tell you the password.
He only answers:
“Yes” or “No.”
You can keep asking carefully chosen questions until you learn something about the hidden information.
That's the basic idea behind Blind SQL Injection.

What is it?
👉 Blind SQL Injection is a type of SQL Injection where the application is vulnerable, but it does not directly show us the database results or SQL errors.
Simple example:
A website asks for a User ID.
Normally, we might expect the application to show us information from the database.
But in a blind situation, we don't get the actual database result.
Instead, we observe something that tells us whether our SQL condition was TRUE or FALSE.

Why does it happen?
Because the application still places user input into an SQL query without properly separating the input from the SQL instructions.

What are we looking for?
We're basically checking:
“Can I ask the database a TRUE/FALSE question and detect the answer from the application's response?”

So remember:
Blind SQL Injection = We cannot directly see the data, so we learn it indirectly through the application's response.
```
So technically,
Blind SQL Injection occurs when an application is vulnerable to SQL injection, 
but the HTTP response does not contain the relevant query results or useful database errors.


---
## 1. Interface
<img width="919" height="421" alt="Screenshot 2026-09-20 090612" src="https://github.com/user-attachments/assets/3f57b2ad-8a7d-41c1-8c37-23cfbb7dd284" />


Brief: What the form does (The Blind SQL Injection form accepts a User ID as input and sends it to the application when we click Submit.
Unlike normal SQL Injection, the important thing is not whether the application displays database records.
Instead, we observe how the application responds to our input and use that response as a TRUE/FALSE signal.)

For example:
Question → Is the condition TRUE?

Application
     ↓
Different response
     ↓
TRUE / FALSE


---
## 2. Level: LOW

### Interface
The Low level provides a text input where we can enter a User ID.
The application then processes the value and gives us a response.
The important thing here is that the response can be used as a boolean indicator.

<img width="628" height="288" alt="Screenshot 2026-09-20 090623" src="https://github.com/user-attachments/assets/d396397f-bdca-4fbd-9845-6605bc4e724f" />
<img width="972" height="608" alt="Screenshot 2026-09-20 090658" src="https://github.com/user-attachments/assets/cb9003c4-029d-41b1-926b-47dbc6227d67" />

### Source Code
<img width="981" height="835" alt="Screenshot 2026-09-20 090710" src="https://github.com/user-attachments/assets/a811ee0d-09fb-4e22-ab76-c245eb55996e" />

What makes it exploitable: Reviewing the source code shows that the application directly places the user's input into an SQL query:

```text
SELECT first_name, last_name FROM users WHERE user_id = '$id';
```

The input is not properly parameterized, allowing the user to influence the SQL query's logic.
Unlike regular SQL Injection, 
Blind SQL Injection does not directly display the query results. 
Instead, we infer information from differences in the application's response, 
such as whether a condition is true or false.

<img width="981" height="835" alt="Screenshot 2026-09-20 090710" src="https://github.com/user-attachments/assets/2cd5901b-1806-41e2-bff5-61e9a5bb7647" />

### Exploitation
What happened: The `user_id` input is still incorporated into the SQL query, allowing us to manipulate its condition.

1st Payload
We can test whether a condition is true:

```text
1' AND 1=1 #
```

Because `1=1` is always true, the application responds as it normally would for a valid input.

<img width="644" height="270" alt="Screenshot 2026-09-20 090804" src="https://github.com/user-attachments/assets/33dd07c9-34e6-463e-9cd0-f9dfc76f6db1" />

2nd Payload
We can then test a false condition:

```text
1' AND 1=2 #
```
Since `1=2` is false, the application's response changes.

<img width="608" height="259" alt="Screenshot 2026-09-20 090834" src="https://github.com/user-attachments/assets/e29ad3dd-0e55-41f4-838a-bfc52c8e66eb" />

This difference allows us to infer information from the database without directly seeing the query results.

<img width="596" height="264" alt="Screenshot 2026-09-20 091009" src="https://github.com/user-attachments/assets/0d8da3c7-6fd3-4aba-b60e-4060e88f9537" />
<img width="613" height="268" alt="Screenshot 2026-09-20 091032" src="https://github.com/user-attachments/assets/d5216284-0cdb-4db4-9b19-7f0eab1dad4e" />

Key takeaway: Blind SQL Injection relies on observing differences in the application's response to determine whether injected SQL conditions are true or false.


---
## 3. Level: MEDIUM

### Interface
Here, there're a select with range (1 to 5) to set User ID.

<img width="641" height="286" alt="Screenshot 2026-09-20 091121" src="https://github.com/user-attachments/assets/e7c4057e-8d5f-436d-b9a7-ccb3029e2ce5" />

### Source Code

<img width="961" height="752" alt="Screenshot 2026-09-21 074603" src="https://github.com/user-attachments/assets/3f442df2-cd7f-452d-b969-543825941e03" />


## How it differs from Low
Additional protection has been added, and the `id` value is handled differently in the SQL query.

## Why Low's payload fails now
The Low-level payload relied on breaking out of quotes around the input. 
Since `$id` is no longer enclosed in quotes, that payload no longer fits the query structure.

## How we exploit it anyway
We can use Burp Suite Repeater to modify the `id` parameter and test SQL conditions directly.
The query follows this structure:

```text
SELECT first_name, last_name FROM users WHERE user_id = $id;
```

<img width="1004" height="700" alt="Screenshot 2026-09-20 091236" src="https://github.com/user-attachments/assets/c3a9cbe2-9e32-4601-8ab4-981a954a50ed" />


## Exploitation
What happened: Because the `user_id` input is still incorporated directly into the SQL query, 
we can manipulate its logic and observe how the application responds.

1st Payload
We can provide a condition that is always true:

```text
1 OR 1=1 #
```

This changes the query to:

```text
SELECT first_name, last_name FROM users WHERE user_id = 1 OR 1=1 #;
```

Because `1=1` is always true, the application responds differently.

<img width="1342" height="692" alt="Screenshot 2026-09-20 091402" src="https://github.com/user-attachments/assets/3faa69c7-3055-4d00-9c25-0400e61147fc" />
<img width="1330" height="703" alt="Screenshot 2026-09-20 091421" src="https://github.com/user-attachments/assets/b2222476-e2b9-49d3-9f61-b8d139b20989" />

2nd Payload
We can then test a condition that is always false:

```text
1 AND 1=2 #
```

Since `1=2` is false, the application's response changes.
By comparing these responses, we can infer information from the database without directly seeing the query results.

<img width="1342" height="659" alt="Screenshot 2026-09-20 091716" src="https://github.com/user-attachments/assets/f3bc83f2-2eb9-4cff-927a-a7d9dcb6c64d" />

Key takeaway: Blind SQL Injection works by manipulating SQL conditions and observing differences in the application's responses. Parameterized queries are the proper defense.


---
## 4. Level: HIGH

### Interface
In this level clicking on first page, we obtain a redirect to a second page to submit effectively our Session ID:
<img width="633" height="283" alt="Screenshot 2026-09-20 092014" src="https://github.com/user-attachments/assets/ec787d9c-8eb6-434f-b375-8d384f339303" />

### Source Code

<img width="971" height="774" alt="Screenshot 2026-09-20 092055" src="https://github.com/user-attachments/assets/48572745-a96b-4044-baa8-6b01ae2816a6" />

How it differs from Medium
The application uses a different request flow: a **GET request** loads the page, while a **POST request** submits the User ID.

Why Medium's approach needs to be adjusted
The request structure is different, so the payload needs to be sent through the appropriate **POST parameter** instead of the Medium-level request.

How we exploit it anyway
Using Burp Suite, we can intercept the POST request and modify the User ID to test whether the SQL query can still be manipulated.


### Exploitation
What happened: Although the request flow is different, the `user_id` input is still incorporated unsafely into the SQL query, 
allowing us to test and manipulate its logic.

### Exploitation

1st Payload
We can test a condition that is always true:

```text
1' OR 1=1 #
```

This manipulates the query logic so the condition becomes true.

<img width="661" height="419" alt="Screenshot 2026-09-20 092526" src="https://github.com/user-attachments/assets/18b0d7f1-defa-4a99-b998-c2647fd70bb9" />


2nd Payload
We can then test a false condition:

```text
1' AND 1=2 --
```

Because it doesn't recognize the character `--` which is unusual it's outputted is false, the application's response should differ from the first test.
This difference helps us determine whether our injected condition was evaluated as true or false.

<img width="662" height="388" alt="Screenshot 2026-09-20 092446" src="https://github.com/user-attachments/assets/9261238e-492d-47c1-92bf-74cd7866762f" />


Key takeaway: Splitting functionality between GET and POST does not prevent Blind SQL Injection. The vulnerability remains if user input is still incorporated into SQL without parameterized queries.


---
## 5. Level: IMPOSSIBLE

### Source Code

<img width="961" height="752" alt="Screenshot 2026-09-21 074603" src="https://github.com/user-attachments/assets/7df20eb5-27ce-42be-a1e6-26b975ba7f10" />

## Why Nothing Works Here
Unlike the previous levels, the application properly separates SQL code from user input using a **prepared statement with a bound parameter**.
Even if we enter:

```text
' OR 1=1 #
```

the database treats the entire input as data rather than SQL instructions.
Because the input cannot change the structure of the SQL query, the previous Blind SQL Injection payloads no longer work.

## The Proper Solution
The proper defense is to use prepared statements and parameterized queries:

```text
SQL code    → SELECT ... WHERE user_id = ?
User input  → 1
```

The `?` is a placeholder, keeping the user's input separate from the SQL code.
Input/type validation can provide an additional layer of protection, such as ensuring `User ID` is an integer.
Key takeaway: Blind SQL Injection is prevented when user-controlled data is kept separate from SQL instructions.


---
## 6. Summary Table
`
| Level      | Protection                                | Result                              |
| ---------- | ----------------------------------------- | ----------------------------------- |
| Low        | No protection                             | SQL Injection works                 |
| Medium     | Input escaping / different query handling | SQL Injection can still be possible |
| High       | Additional request/query handling         | SQL Injection can still be possible |
| Impossible | Prepared statements + parameter binding   | SQL Injection prevented             |
`































