**Lab 1: SQLi login bypass (Apprentice), 2026-10-04**

Payload: administrator'--

What happened: logged in as administrator without a password.

Why: comment strips the password check from the query, username match alone is enough.

