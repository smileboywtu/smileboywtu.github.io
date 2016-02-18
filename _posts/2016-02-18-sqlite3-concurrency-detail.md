---
layout: post
title:  sqlite3 concurrency detail
date:   2016-02-18 12:48:00 +0800
categories: articles
---

> source:
> [https://www.sqlite.org/faq.html#q5](https://www.sqlite.org/faq.html#q5)

Multiple processes can have the same database open at the same time. Multiple
processes can be doing a SELECT at the same time. But only one process can be
making changes to the database at any moment in time, however.

We are aware of no other embedded SQL database engine that supports as much
concurrency as SQLite. SQLite allows multiple processes to have the database
file open at once, and for multiple processes to read the database at once.
When any process wants to write, it must lock the entire database file for the
duration of its update. But that normally only takes a few milliseconds. Other
processes just wait on the writer to finish then continue about their business.
Other embedded SQL database engines typically only allow a single process to
connect to the database at once.

However, client/server database engines (such as PostgreSQL, MySQL, or Oracle)
usually support a higher level of concurrency and allow multiple processes to
be writing to the same database at the same time. This is possible in a client/server
database because there is always a single well-controlled server process available
to coordinate access. If your application has a need for a lot of concurrency,
then you should consider using a client/server database. But experience suggests
that most applications need much less concurrency than their designers imagine.

When SQLite tries to access a file that is locked by another process, the
default behavior is to return SQLITE_BUSY. You can adjust this behavior from
C code using the sqlite3_busy_handler() or sqlite3_busy_timeout() API functions.

Happy coding.
