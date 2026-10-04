\# Login Bypass via SQLi



\*\*Platform:\*\* PortSwigger Web Security Academy

\*\*Difficulty:\*\* Apprentice

\*\*Topic:\*\* SQL Injection



\## Objective

The application authenticates users with a username and password form. The goal is to log in as the administrator account without knowing its password.



\## Vulnerability Overview

The login form builds a SQL query by inserting the submitted username and password directly into a WHERE clause that checks both fields. Since the input is not parameterized, the structure of the query itself can be altered by the attacker. This means authentication can be defeated not by guessing credentials, but by rewriting the query so the password check never happens.



\## Methodology



\### Step 1: Identify the target account

I used the known username administrator as the injection target, since it is a predictable high-value account on most applications.



\### Step 2: Strip the password check

I submitted the username field as administrator followed by a closing quote and a comment marker, leaving the password field blank. This closed the username string early and commented out the rest of the query, including the AND password check. The query then only tested whether a user named administrator existed, which it did, so the application logged me in without ever validating a password.



\## Payloads

administrator'--



\## Proof of Exploitation

!\[login bypass request](images/login-bypass-request.png)



\## Root Cause and Remediation

\*\*Root cause:\*\* the authentication query concatenates raw user input into SQL, letting an attacker comment out the password check entirely rather than just supplying a wrong value.



\*\*Fix:\*\* use parameterized queries so the password check cannot be removed by input content, and never rely on a successful username match alone as proof of authentication.



\## Key Takeaway

A SQL injection in a login form is not just a data leak, it can bypass authentication entirely, so authentication queries deserve the same parameterization discipline as any other query.

