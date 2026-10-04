Where Clause SQLi: Retrieve Hidden Data



Platform: PortSwigger Web Security Academy

Difficulty: Apprentice

Topic: SQL Injection



Objective



The application lists products by category through a URL parameter. The goal is to make it return a product that is marked as unreleased and normally hidden from the product listing.



Vulnerability Overview



The category parameter from the URL is inserted directly into a SQL query without any sanitization or parameterization. The underlying query filters on both category and a released flag. Because the application trusts the input as plain text rather than treating it as a boundary-safe value, an attacker can inject SQL syntax that changes what the query actually checks for.



Methodology

Step 1: Confirm the injection point



I submitted a category value ending in a single quote and a SQL comment marker. This closed the open string early and commented out everything after it, including the released check. The response returned the hidden product, confirming that user input reaches the query unescaped.



Step 2: Expand the result set



To see the technique generalize past removing one filter, I added an always-true condition before the comment. This turned the WHERE clause into something that matches every row in the table, not just the Gifts category, proving the injection point can control the entire result set, not just bypass a single flag.



Payloads



Gifts'--

Gifts' OR 1=1--



Proof of Exploitation

images/where-clause-response.png



Root Cause and Remediation



Root cause: the application builds SQL queries by concatenating raw user input into the query string, so anything the user submits is interpreted as part of the query's logic rather than as inert data.



Fix: use parameterized queries (prepared statements) so user input is always bound as a literal value and can never alter the query's structure, regardless of what characters it contains.



Key Takeaway



Any field that reaches a database query unsanitized is a potential point where an attacker can rewrite the query's logic, not just inject extra data.

