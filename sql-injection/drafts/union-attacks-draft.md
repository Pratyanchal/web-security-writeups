Lab 1: SQL injection UNION attack, determining number of columns (Apprentice), 2026-10-05

Payload 1: Gifts'+ORDER+BY+4--

Payload 2: Gifts'+UNION+SELECT+NULL,NULL,NULL--

What happened: payload 1 errored at column 4, confirming 3 columns is the breakpoint ceiling.
Payload 2 succeeded with 3 NULLs, cross-confirming 3 columns.

Why: ORDER BY references column position by number, so referencing a position beyond
what the query selects throws a database error, letting me binary search the column count.
UNION SELECT with NULLs confirms the same count without type-mismatch errors, since NULL is valid for any column type.



Lab 2: SQL injection UNION attack, finding a column containing text (Apprentice), 2026-10-06

Payload 1: Gifts'+UNION+SELECT+'alrxe8',NULL,NULL--

Payload 2: Gifts'+UNION+SELECT+NULL,'alrxe8',NULL--

Payload 3: Gifts'+UNION+SELECT+NULL,NULL,'alrxe8'--

What happened: payloads 1 and 3 errored, column 2 succeeded
and the test string rendered in the product listing.

Why: each column has an underlying data type, and UNION requires every column across both queries to match type.
Substituting a string into each position one at a time isolates which column accepts strings,
since only that column's type permits it without error.
Column 2 accepting and displaying the string confirms it as the slot to use for exfiltrating text data in later labs.



Lab 3: SQL injection UNION attack, retrieving data from other tables (Apprentice), 2026-10-07

Payload 1: Gifts'+UNION+SELECT+NULL,NULL--

Payload 2: Gifts'+UNION+SELECT+'a','a'--

Payload 3: Gifts'+UNION+SELECT+username,password+FROM+users--

What happened: this lab had 2 columns instead of 3, both string-compatible. Payload 1 confirmed column count, payload 2 confirmed both columns accept strings, payload 3 pulled every row from the users table directly into the product listing, username and password rendered in place of product name and description. Used the leaked administrator credentials to log in.

Why: UNION SELECT doesn't require the second query to come from the same table as the first, only that the column count and types line up. Since both columns here were string-compatible, I could select straight from an unrelated table, users, and have the database return its data in the same response the page already renders. This works because the database account powering the app query has read access to tables beyond what the app itself needs.

Lab 4: SQL injection UNION attack, retrieving multiple values in a single column (Apprentice), 2026-10-08

Payload 1: Gifts'+UNION+SELECT+NULL,NULL--

Payload 2: Gifts'+UNION+SELECT+'a','a'--

Payload 3: Gifts'+UNION+SELECT+NULL,username||'\~'||password+FROM+users--

What happened: column count was 2, but only column 2 accepted strings this time, column 1 errored. With only one usable column, I concatenated username and password together with a separator into that single column, instead of relying on two separate columns like the previous lab.

Why: a UNION attack is limited to as many output slots as the original query has usable columns, but concatenation lets a single column carry multiple pieces of data at once, joined with a separator character that's unlikely to appear in the real data, so the combined string can be split back apart by eye once it's on the page. This matters because real-world queries often expose far fewer usable columns than the number of fields you actually need to exfiltrate.

