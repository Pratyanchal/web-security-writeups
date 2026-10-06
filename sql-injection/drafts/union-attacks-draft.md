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

