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
<img width="848" height="686" alt="Screenshot 2026-09-21 091611" src="https://github.com/user-attachments/assets/78488780-5c5b-4712-aede-5f5978c89daa" />

What the form does (The DVWA CSRF form allows you to change the user's password by entering a new password and confirming it.)


---
## 2. Level: LOW

### Source Code
<img width="979" height="639" alt="Screenshot 2026-09-21 091704" src="https://github.com/user-attachments/assets/60e7235d-8898-45e5-809d-62dda38b2c08" />

What makes it exploitable: Reviewing the source code shows that the application accepts the new password and changes the admin password through a GET request.
The request does not require a CSRF token or another mechanism to verify that the request was intentionally made by the user.
Because the browser automatically sends the user's existing authentication information with the request, another page can potentially cause the authenticated browser to submit the password-change request.

<img width="1003" height="849" alt="Screenshot 2026-09-21 092108" src="https://github.com/user-attachments/assets/9afe1634-c40a-471c-8987-3f7340829b3e" />

### Exploitation

What happened: The password-change request can be triggered by supplying the required parameters in the URL:

```text
/DVWA/vulnerabilities/csrf/?password_new=password1&password_conf=password1&Change=Change
```

When an authenticated user visits this URL in the DVWA lab, the browser sends the request and the application processes the password change.
The important point is that no SQL or system command is being injected. The attack abuses the user's existing authenticated session to perform an unwanted state-changing action.

<img width="1916" height="893" alt="Screenshot 2026-09-21 092223" src="https://github.com/user-attachments/assets/f40bf5ab-44b0-44a3-9895-81186cf09c4a" />

Key takeaway: CSRF occurs when an application accepts a state-changing request without sufficiently verifying that it was intentionally made by the authenticated user. CSRF tokens and appropriate request protections help prevent this.


---
## 3. Level: MEDIUM

### Source Code

<img width="991" height="854" alt="Screenshot 2026-09-21 092306" src="https://github.com/user-attachments/assets/1cc20058-51f7-4e4f-b871-99210ec32624" />

#How it differs from Low: An additional check has been added. The application checks the **`HTTP_REFERER`** header to determine whether the request came from the expected server.

<img width="440" height="424" alt="Screenshot 2026-09-21 092513" src="https://github.com/user-attachments/assets/e4f2c99f-e1fd-4059-9ef5-b1c48fc66a74" />

Why Low's approach fails now: The Low-level request can fail because it does not satisfy the application's `HTTP_REFERER` check.
The password fields must also match before the password is changed.

<img width="994" height="456" alt="Screenshot 2026-09-21 092644" src="https://github.com/user-attachments/assets/b616e187-4d5e-4448-8668-1504bf851756" />


How we exploit it anyway: The `HTTP_REFERER` check is not a strong CSRF defense by itself. If another vulnerability, such as Reflected XSS, can cause a request to originate from the expected site, the check may be bypassed.

<img width="873" height="365" alt="Screenshot 2026-09-21 092716" src="https://github.com/user-attachments/assets/7da73865-d78d-4b2d-b04c-8d82dbf3aa63" />
<img width="447" height="152" alt="Screenshot 2026-09-21 092734" src="https://github.com/user-attachments/assets/99397ec5-0bfb-4943-ad61-bea25076b67f" />


In the DVWA lab, this demonstrates that relying only on `HTTP_REFERER` does not reliably prove that a state-changing request was intentionally made by the user.

### Exploitation
<img width="650" height="287" alt="Screenshot 2026-09-21 092803" src="https://github.com/user-attachments/assets/43884b7d-20f8-40e5-8df2-cd5fadbd52c7" />
<img width="997" height="506" alt="Screenshot 2026-09-21 092929" src="https://github.com/user-attachments/assets/df8c2b30-19b2-4c48-8d63-d00780f6eadc" />
<img width="965" height="434" alt="Screenshot 2026-09-21 093025" src="https://github.com/user-attachments/assets/b0be3262-e824-462f-bbbc-5a56c974ee85" />

Key takeaway: Checking `HTTP_REFERER` can provide an additional signal, but it is not a substitute for a proper CSRF token and secure request validation.


---
## 4. Level: HIGH

### Source Code

<img width="975" height="852" alt="Screenshot 2026-09-21 093110" src="https://github.com/user-attachments/assets/39f44ac2-96d3-4df9-ab7b-43c991b8fd6b" />

How it differs from Medium: The application now uses an anti-CSRF token (`user_token`) that is generated for the request and checked when the password is changed.
The password confirmation is also checked, and the input is properly escaped.

Why Medium's bypass fails now
The previous approach relied on triggering the password-change request without providing a valid CSRF token.
Because the High level requires a valid `user_token`, simply sending the previous request is no longer sufficient.

How we exploit it anyway
In the DVWA lab, the token can potentially be obtained through another vulnerability such as Reflected XSS and then included in a subsequent password-change request.

This demonstrates an important point: a CSRF token can protect the request itself, but if an attacker can execute JavaScript in the application's trusted origin, that JavaScript may be able to access the page and obtain the token.

Key takeaway: CSRF tokens are a strong defense against forged requests, but they do not replace protection against XSS. An XSS vulnerability in the same application can undermine CSRF protection.


---
## 5. Level: IMPOSSIBLE

### Source Code

<img width="1908" height="854" alt="Screenshot 2026-09-21 093811" src="https://github.com/user-attachments/assets/437cc9ad-1e58-4244-855b-127eedd8d96f" />

Why Nothing Works Here: Unlike the previous levels, the application requires the user's current password before allowing the password to be changed.
An attacker attempting to forge the request does not know this value, so they cannot provide all the information required to complete the password change.
The request therefore cannot be successfully forged using only the victim's existing authenticated session.

Key Takeaway
CSRF protections work by requiring information or validation that an attacker cannot simply reproduce.
In this level, requiring the current password adds an extra verification step that prevents the password-change action from being completed through a forged request.


---
## 6. Summary Table

| Level      | Protection                    | Result                                                |
| ---------- | ----------------------------- | ----------------------------------------------------- |
| Low        | No CSRF protection            | CSRF works                                            |
| Medium     | `HTTP_REFERER` validation     | CSRF may still be possible                            |
| High       | Anti-CSRF token               | Forged requests are blocked without a valid token     |
| Impossible | Current-password verification | Password change cannot be forged without the password |















































































