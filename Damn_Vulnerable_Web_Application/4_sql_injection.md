# SQL Injection

## Overview

```
Imagine you ask a librarian:
“Find the book called Harry Potter.”
The librarian is supposed to search for exactly that book.
But instead of treating your request as just a book name, the librarian blindly combines your words with the instructions used to search the library.
If someone adds extra instructions to their request, they may change what the librarian searches for or returns.
That's the basic idea behind SQL Injection.

What is it?
👉 Tricking a database query into doing something different from what the application originally intended.
Simple example:
A website asks:
“Enter your username.”
The application uses that username to search its database.
If the application doesn't properly handle the input, someone may enter specially crafted input that changes the logic of the database query.

Why does it happen?
Because the application combines user input with SQL commands without properly separating the data from the SQL instructions.

What are we looking for?
We're basically checking:
“Can I make my input change how the database query behaves?”

So remember:
SQL Injection = User input changes the intended logic of a database query.
```
So technically,
SQL Injection (SQLi) is a vulnerability where untrusted user input is incorporated into an SQL query in an unsafe way, allowing the input to alter the query's intended behavior.
Risk Level: High to Critical (depending on the application's data and permissions)


---
## 1. Interface
<img width="926" height="453" alt="Screenshot 2026-09-20 074302" src="https://github.com/user-attachments/assets/ee53f5ae-4b77-45c5-995f-97f8cf096f16" />

Brief: What the form does (The SQL Injection form accepts a User ID as text input and sends it to the application when we click Submit. 
The application then uses that ID to query the database and return the corresponding user information.)


---
## 2. Level: LOW

### Interface
We've an input type text that received an User ID in I by user and submit request using the Submit button:
<img width="915" height="504" alt="Screenshot 2026-09-20 074335" src="https://github.com/user-attachments/assets/4d051cc7-5ec2-4fa0-8428-c1680362b259" />

### Source Code
<img width="990" height="849" alt="Screenshot 2026-09-20 074311" src="https://github.com/user-attachments/assets/ff2dbea5-0ba5-494f-bf30-ebde3ea20cac" />

What makes it exploitable: Reviewing the source code shows that the application directly places the user's input into an SQL query:

```text
SELECT first_name, last_name FROM users WHERE user_id = '$id';
```

The input is not properly sanitized or parameterized. This means the database can interpret parts of the user's input as SQL instructions instead of treating everything as ordinary data.

<img width="998" height="859" alt="Screenshot 2026-09-20 074629" src="https://github.com/user-attachments/assets/82b7eabd-74d1-4922-b0eb-c6b0610b5f68" />

### Exploitation
What happened: Because the `user_id` input is directly inserted into the SQL query, we can manipulate the query's logic.

1st Payload
We can use a condition that is always true, such as `1=1`, and use `#` to comment out the remaining part of the query:

```text
' OR 1=1 #
```

This changes the query's logic and causes it to return multiple user records instead of only the requested ID.

<img width="404" height="417" alt="Screenshot 2026-09-20 083349" src="https://github.com/user-attachments/assets/524080e6-a968-468b-9dc2-1215741ea98a" />

#### 2nd Payload
We can also use the `UNION` operator to combine the original query with another query that retrieves additional columns from the `users` table:

```text
' UNION SELECT first_name,password FROM users #
```

This demonstrates how SQL Injection can expose data that the application did not intend to display.

<img width="897" height="597" alt="Screenshot 2026-09-20 083553" src="https://github.com/user-attachments/assets/260fd2a1-23ab-499e-a9b2-534515f37b42" />


Key takeaway: The vulnerability exists because user input is directly combined with the SQL query. Proper parameterized queries and input handling prevent the database from treating user input as SQL instructions.


---
## 3. Level: MEDIUM

### Interface
Here, there're a select with range (1 to 5) to set User ID.

<img width="524" height="239" alt="Screenshot 2026-09-20 083904" src="https://github.com/user-attachments/assets/8c3a1dcd-8dcc-4fb3-b728-4395e72a634a" />

### Source Code

<img width="976" height="797" alt="Screenshot 2026-09-20 083936" src="https://github.com/user-attachments/assets/2cea7f0f-9fee-4e7c-9d50-314fa1f38a3a" />

How it differs from Low:
Additional protection has been added. 
The application now applies an escaping function to the input, 
and the `$id` value is no longer enclosed in quotes in the SQL query.

Why Low's payload fails now:
The Low-level payload relied on breaking out of the quotes around the `$id` value. 
Since the value is no longer enclosed in quotes, 
that approach is no longer necessary and the Low payload does not fit the new query structure.

How we exploit it anyway:
We can modify the request using Burp Suite Repeater and place SQL conditions directly into the `id` parameter.
The query follows this structure:

```text
SELECT first_name, last_name FROM users WHERE user_id = $id;
```

### Exploitaton
What happened: Because the user_id input is still incorporated directly into the SQL query, 
we can manipulate the query's logic even though additional input handling has been added.

1st Payload
We can provide a condition that is always true:

```text 
1 OR 1=1 #
```

This changes the query to:

```text
SELECT first_name, last_name FROM users WHERE user_id = 1 OR 1=1 # ;
```

Because `1=1` is always true, the query can return multiple records.

<img width="997" height="849" alt="Screenshot 2026-09-20 084119" src="https://github.com/user-attachments/assets/36061ce1-eaf1-4020-a7ef-766da4c68b94" />
<img width="1360" height="862" alt="Screenshot 2026-09-20 084610" src="https://github.com/user-attachments/assets/268b29d1-5d82-475c-9e64-31f938ce6d7b" />


2nd Payload
We can also use `UNION` to combine the original query with another `SELECT` statement:

```text
1 UNION SELECT first_name,password FROM users #
```

This demonstrates that the SQL query can be manipulated to retrieve additional information from the database.
Note: Each `SELECT` statement used with `UNION` must return the same number of columns.

<img width="1338" height="857" alt="Screenshot 2026-09-20 084953" src="https://github.com/user-attachments/assets/e5d5364b-b700-4576-8029-6974a80d9b36" />

Key takeaway: Even with additional input handling, directly constructing SQL queries from user input can leave the application vulnerable. Parameterized queries are the proper defense.


---
## 4. Level: HIGH

### Interface
In this level clicking on first page, we obtain a redirect to a second page to submit effectively our Session ID:
<img width="566" height="415" alt="Screenshot 2026-09-20 085229" src="https://github.com/user-attachments/assets/63e429a0-9573-4220-ae30-4b6e74e8e06e" />

### Source Code

<img width="974" height="852" alt="Screenshot 2026-09-20 085306" src="https://github.com/user-attachments/assets/ba541997-7f59-41ee-9cfb-e206dd1eb559" />

How it differs from Medium
The application adds another layer of protection by separating the request into two stages:
a GET request loads the page, while a **POST request** submits the User ID. The input is also handled differently from the Medium level.

Why Medium's approach needs to be adjusted
The Medium-level payload was designed for the query structure at that level. Since the High level uses a different request flow and query structure, we need to send the payload through the appropriate request.

How we exploit it anyway
By intercepting the request with Burp Suite, 
we can modify the User ID in the POST request and test whether the SQL query can still be manipulated.

### Exploitation
What happened: Although the application uses a different request flow, the user_id input is still used unsafely in the SQL query. By modifying the relevant request parameter, 
we can manipulate the query's logic.

1st Payload

```text id="h6k2pd"
1' OR 1=1 #
```

This changes the query logic to:

```text id="z8r4qm"
SELECT first_name, last_name FROM users WHERE user_id = '1' OR 1=1 # ';
```

<img width="910" height="590" alt="Screenshot 2026-09-20 090012" src="https://github.com/user-attachments/assets/1c7ccc79-9b40-4bf8-9761-2813991c87aa" />


Because `1=1` is always true, the query can return multiple user records.

2nd Payload
We can also test a `UNION` query to see whether additional information can be returned:

```text id="m3x9va"
' UNION SELECT first_name,password FROM users #
```

This demonstrates that the SQL query remains manipulable despite the additional request handling.
Note: Every `SELECT` statement used with `UNION` must return the same number of columns.

<img width="561" height="614" alt="Screenshot 2026-09-20 090134" src="https://github.com/user-attachments/assets/ca11a361-9cfc-4e28-a4c6-7eed6075111f" />

Key takeaway: Splitting functionality between GET and POST does not by itself prevent SQL Injection. The important protection is using parameterized queries so user input is never interpreted as SQL code.


---
## 5. Level: IMPOSSIBLE

### Source Code

<img width="1911" height="844" alt="Screenshot 2026-09-20 090247" src="https://github.com/user-attachments/assets/17c1090b-3ab1-4aeb-a829-4f82aeec5367" />

## Why Nothing Works Here
Unlike the previous levels, the application properly separates SQL code from user input instead of directly combining them into the query.
The application uses a prepared statement with a bound parameter, meaning the User ID is treated as data, not as part of the SQL command.
So even if we enter SQL syntax such as:

```text
' OR 1=1 #
```

the database does not interpret it as SQL instructions. It treats the entire input as a value.
Because the application does not allow user input to modify the structure of the SQL query, the previous SQL Injection payloads no longer work.

The Proper Solution
The best approach is to use prepared statements with parameterized queries.
Instead of building a query like:

```text
SELECT ... WHERE user_id = '$id'
```

the application separates the SQL structure from the user's input.
The database receives:

```text
SQL code → SELECT ... WHERE user_id = ?
User input → 1
```

The `?` acts as a placeholder for the user's value.
The application can also validate that the User ID has the expected type, such as an integer, before sending it to the database.
Additional security controls, such as CSRF protection, can help protect state-changing requests, but CSRF is separate from SQL Injection.

Key Takeaway
SQL Injection is prevented most reliably by separating SQL instructions from user-controlled data. Prepared statements and parameterized queries make the database treat user input as data rather than executable SQL.


---

## 6. Summary Table

| Level      | Protection                                | Result                              |
| ---------- | ----------------------------------------- | ----------------------------------- |
| Low        | No protection                             | SQL Injection works                 |
| Medium     | Input escaping / different query handling | SQL Injection can still be possible |
| High       | Additional request/query handling         | SQL Injection can still be possible |
| Impossible | Prepared statements + parameter binding   | SQL Injection prevented             |







