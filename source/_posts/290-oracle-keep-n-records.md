---
title: "'Keep N records, delete the rest' in Oracle SQL"
date: 2018-06-18 08:14:51
tags:
---

For the purpose of purging a quickly growing table with transient records (such as pagination or history), some sensible triggers may be by timestamp (purge records older than X) or by number of records (keep at most N records).

I had trouble defining the latter, for example keep the latest 10000 records by timestamp and delete the later ones.

[Looking at this StackOverflow question](https://stackoverflow.com/questions/25869436/keep-first-n-number-of-rows-and-delete-the-rest), the first answer suggests the most intuitive way:

```sql
delete from history where id not in
    (select id from history where rownum < 10000 order by date)
```

This is a very inefficient method, benchmarked at 17.8 seconds in PL/SQL Developer, with less than 17000 total records.

But the second answer is a step in the right direction, it just needs to be given the right operator and ordering for our goal. This is what I ended up doing:

```sql
delete from history where id <
    (select min(id) from history where rownum < 10000 order by date)
```

This ran in only 1.2 second! Not quite intuitive, as our DBA team started recommending using in-memory tables or indexing techniques instead of coming up with this more efficient and elegant way... We now run this periodically in production.