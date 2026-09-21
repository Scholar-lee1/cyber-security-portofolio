# Cross Site Request Forgery

## Overview

```
Imagine you are logged into your bank account, and someone tricks you into clicking a button that secretly tells the bank:
“Transfer money.”
Because you are already logged in, the bank may think the request really came from you.
That's the basic idea behind CSRF.

What is it?
👉 Tricking a logged-in user into sending an unwanted request to a website without realizing it.
Simple example:
A website allows you to change your email address.
You are already logged in, and another website tricks your browser into sending a request to change that email.

Why does it happen?
Because the application trusts requests from an authenticated user without properly checking whether the request was actually intended by that user.

What are we looking for?
We're basically checking:
“Can I make a logged-in user's browser perform an action they didn't intentionally request?”

So remember:
CSRF = Tricking a user's browser into performing an unwanted action on a website where they're already logged in.
```

So technically,
Cross-Site Request Forgery (CSRF) is a vulnerability where an attacker tricks an authenticated user's browser into sending an 
unintended request to a web application. If the application doesn't properly verify the request's origin or intent, 
the action may be carried out using the user's existing session.


---
## 1. Interface
<img width="928" height="858" alt="Screenshot 2026-09-19 112546" src="https://github.com/user-attachments/assets/d8cbb32e-c6ed-4655-aeff-91a1b4b37925" />




















