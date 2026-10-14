Lab 1: SQL injection attack, querying the database type and version on Oracle (Apprentice), 2026-10-11

Payload 1: Gifts' UNION SELECT NULL,NULL FROM dual--

Payload 2: Gifts' UNION SELECT banner,NULL FROM v$version--

What happened: payload 1 confirmed 2 columns using NULLs, but errored on its own since Oracle requires every SELECT to reference a table, even one returning no real data. Payload 2 added v$version as that required table, pulling the Oracle version banner into the first column.

Why: Oracle's SQL syntax is stricter than most databases, it has no concept of a FROM-less SELECT, so UNION-based injection against Oracle needs a dummy table to query from. v$version is a built-in Oracle view that always exists and holds version info, making it the standard target for this. The NULL in column 2 stays as a type-safe placeholder, since only column 1 was confirmed text-compatible.



Lab 2: SQL injection attack, querying the database type and version on MySQL and Microsoft (Apprentice), 2026-10-12

Payload: Gifts' UNION SELECT @@version,NULL--

What happened: the version string for the database engine came back directly in the first column, no second payload or placeholder table needed.

Why: unlike Oracle, MySQL and Microsoft SQL Server both support @@version as a built-in scalar system variable, meaning it returns a single value directly without needing to be selected from any table, so no FROM clause is required at all. This is a sharp contrast to Oracle's stricter syntax from the previous lab, where even a value like the version banner still had to come from a table, v$version. A trailing space after the comment marker was also required here, since MySQL's comment syntax needs at least one space or character after the double dash to be parsed correctly as a comment, unlike other databases that treat -- as a complete comment marker on its own.



Lab 3: SQL injection attack, listing the database contents on non-Oracle databases (Apprentice), 2026-10-13

Payload 1: Gifts' UNION SELECT @@version,NULL--

Payload 2: Gifts' UNION SELECT table\_name,NULL FROM information\_schema.tables--

Payload 3: Gifts' UNION SELECT column\_name,NULL FROM information\_schema.columns WHERE table\_name='users\_nppapk'--

Payload 4: Gifts' UNION SELECT password\_gouqkr,NULL FROM users\_nppapk WHERE username\_mvvchj='administrator'--

What happened: payload 1 fingerprinted the database as PostgreSQL. Payload 2 listed every table name in the schema, revealing a randomly named table, users\_nppapk, that wouldn't be guessable without this step. Payload 3 listed that table's column names, revealing randomized column names username\_mvvchj and password\_gouqkr. Payload 4 used those discovered names to pull the administrator's password directly.

Why: information\_schema is a standard metadata catalog built into most non-Oracle databases, listing every table and column that exists, regardless of which application created them. Since it's just another set of tables the database can SELECT from, the same UNION injection technique applies to it with no special syntax, it works exactly like querying any other table. This means table and column names don't need to be guessed or known in advance, even deliberately randomized names like users\_nppapk offer no real protection, since the database will readily list its own structure to anyone who can inject a query at all. This turns a single injection point into full schema enumeration before any data is even extracted.



Lab 4: SQL injection attack, listing the database contents on Oracle (Apprentice), 2026-10-14

Payload 1: Lifestyle' UNION SELECT table\_name,NULL FROM all\_tables--

Payload 2: Lifestyle' UNION SELECT column\_name,NULL FROM all\_tab\_columns WHERE table\_name='USERS\_WKNLBV'--

Payload 3: Lifestyle' UNION SELECT PASSWORD\_TXYKQI,NULL FROM USERS\_WKNLBV WHERE USERNAME\_ACINLQ='administrator'--

What happened: payload 1 listed every table in the schema using all\_tables, revealing a randomized table name, USERS\_WKNLBV. Payload 2 listed that table's columns using all\_tab\_columns, revealing PASSWORD\_TXYKQI and USERNAME\_ACINLQ. Payload 3 used those discovered names to pull the administrator's password directly.

Why: all\_tables and all\_tab\_columns are Oracle's own catalog views, serving the exact same purpose as information\_schema.tables and information\_schema.columns on non-Oracle databases from the previous lab, listing every table and column that exists regardless of which application owns it. No dual placeholder was needed here, since the catalog view itself already satisfies Oracle's mandatory FROM clause, unlike the earlier Oracle lab where a simple column count check against NULLs had nothing real to select from. This confirms that schema enumeration generalizes across database engines, each one just exposes its own metadata through a differently named system view, but the underlying technique, treating metadata as just another queryable table, stays the same.

