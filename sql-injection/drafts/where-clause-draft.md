**Lab 1: SQLi in WHERE clause, retrieve hidden data (Apprentice), 03-10-2026**

Payload 1: Gifts'--

Payload 2: Gifts' OR 1=1--

What happened: payload 1 removed the released filter, payload 2 returned every product.

Why: comment strips the rest of the query, OR 1=1 makes the condition always true.

