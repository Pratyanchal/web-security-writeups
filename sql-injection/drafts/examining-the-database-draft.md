Lab 1: SQL injection attack, querying the database type and version on Oracle (Apprentice), 2026-10-11

Payload 1: Gifts' UNION SELECT NULL,NULL FROM dual--

Payload 2: Gifts' UNION SELECT banner,NULL FROM v$version--

What happened: payload 1 confirmed 2 columns using NULLs, but errored on its own since Oracle requires every SELECT to reference a table, even one returning no real data. Payload 2 added v$version as that required table, pulling the Oracle version banner into the first column.

Why: Oracle's SQL syntax is stricter than most databases, it has no concept of a FROM-less SELECT, so UNION-based injection against Oracle needs a dummy table to query from. v$version is a built-in Oracle view that always exists and holds version info, making it the standard target for this. The NULL in column 2 stays as a type-safe placeholder, since only column 1 was confirmed text-compatible.

