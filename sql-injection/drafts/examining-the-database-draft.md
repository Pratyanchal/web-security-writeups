Lab 1: SQL injection attack, querying the database type and version on Oracle (Apprentice), 2026-10-11

Payload 1: Gifts' UNION SELECT NULL,NULL FROM dual--

Payload 2: Gifts' UNION SELECT banner,NULL FROM v$version--

What happened: payload 1 confirmed 2 columns using NULLs, but errored on its own since Oracle requires every SELECT to reference a table, even one returning no real data. Payload 2 added v$version as that required table, pulling the Oracle version banner into the first column.

Why: Oracle's SQL syntax is stricter than most databases, it has no concept of a FROM-less SELECT, so UNION-based injection against Oracle needs a dummy table to query from. v$version is a built-in Oracle view that always exists and holds version info, making it the standard target for this. The NULL in column 2 stays as a type-safe placeholder, since only column 1 was confirmed text-compatible.

Lab 2: SQL injection attack, querying the database type and version on MySQL and Microsoft (Apprentice), 2026-10-12

Payload: Gifts' UNION SELECT @@version,NULL-- 

What happened: the version string for the database engine came back directly in the first column, no second payload or placeholder table needed.

Why: unlike Oracle, MySQL and Microsoft SQL Server both support @@version as a built-in scalar system variable, meaning it returns a single value directly without needing to be selected from any table, so no FROM clause is required at all. This is a sharp contrast to Oracle's stricter syntax from the previous lab, where even a value like the version banner still had to come from a table, v$version. A trailing space after the comment marker was also required here, since MySQL's comment syntax needs at least one space or character after the double dash to be parsed correctly as a comment, unlike other databases that treat -- as a complete comment marker on its own.

