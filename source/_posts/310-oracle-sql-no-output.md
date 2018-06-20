---
title: Benchmarking Oracle SQL by suppressing the output
date: 2018-06-20 19:18:43
tags:
---

Recently I wanted to benchmark a specific `SELECT` query. It returned hundreds of thousands of rows and ran for way too long in either my application or sqlplus for it to be a reliable measurement.

Of course, benchmarking an SQL query is never entirely accurate, with the extreme amounts of caching and optimization performed by Oracle. And yet it's a good and surprisingly reliable way of getting a baseline for the minimal runtime of a particular operation.

I tried all sorts of things - spooling, `set termout off`, scripts, ... - but nothing helped. In one way or another, the output would mess my measurements.

Then I came across [this article](https://blog.jooq.org/2017/03/29/how-to-benchmark-alternative-sql-queries-to-find-the-fastest-query/) by jOOQ, a formidable database-agnostic tool that aims to replace traditional and messy Java ORMs like JPA and Hibernate by a close-to-SQL approach. By the way, I recommend looking at their guide for [benchmarking SQL](https://www.jooq.org/benchmark).

Based on that article, here is a good PL/SQL program to benchmark SQL queries:

```sql
SET SERVEROUTPUT ON
DECLARE
  i NUMBER := 0;
BEGIN 
  -- Repeat the whole benchmark several times to avoid warmup penalty
  FOR r IN 1..5 LOOP
    v_ts := SYSTIMESTAMP;
    FOR i IN 1..v_repeat LOOP
      FOR rec IN (
        SELECT first_name, last_name, count(fa.actor_id) AS c
        FROM actor a
        LEFT JOIN film_actor fa
        ON a.actor_id = fa.actor_id
        WHERE last_name LIKE 'A%'
        GROUP BY a.actor_id, first_name, last_name
        ORDER BY c DESC
      ) LOOP
        i := i + 1;
      END LOOP;
    END LOOP;
       
    dbms_output.put_line('Run ' || r || ', Statement 1 : ' || (SYSTIMESTAMP - v_ts) || ', Records: ' || i);
    v_ts := SYSTIMESTAMP;
  END LOOP;
END;
```

Since it's basic PL/SQL, it can just be run in any Oracle SQL tool: PL/SQL Developer, Oracle SQL Developer, sqlplus, you name it. And it works for all types SQL queries, and from the simplest to the heaviest.