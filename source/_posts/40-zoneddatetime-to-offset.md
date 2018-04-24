---
title: Spring Data JPA and ZonedDateTime 	
date: 2018-04-16 07:56
updated: 2018-04-16 07:56
comments: true
tags:
- java
- date
categories:
- programming
permalink: zoneddatetime-to-offset
---

*I'll start (hopefully) making articles about issues I encounter in my job,
mostly Java server-side stuff,
and how I solve them. Perhaps it can help others, and I might be able
to document useful things as well.*

My database had a couple of `timestamp` columns that I wanted to 
display in [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) format, which is a very popular format for date/time, and in particular it is handled very easily in Javascript. This is especially interesting when considering REST APIs or, as in our case, GraphQL servers that must support an array of frameworks and clients. 

First of all, I have to say that
manipulating [java.sql.Timestamp](https://docs.oracle.com/javase/8/docs/api/java/sql/Timestamp.html)s is by far the easiest way to work 
with `timestamp` type columns and `spring-data-jpa` Specifications. Operations such as `CriteriaBuilder` `greatThanOrEqualTo` can be performed
right on the Hibernate entity attributes, as JPA knows to perform the proper conversions and produce proper SQL. If this stuff gets confusing I highly recommend [this guide](https://dreamix.eu/blog/java/java-8-for-constructing-jpa-criteria-queries).

Otherwise, even using simple native queries (using `JdbcTemplate`
or just `PreparedStatement`), using `Timestamp.from` is just as easy.

Then, converting to ZonedDateTime is easy. Let's print one while we're at it:

```java
ZonedDateTime t = timestamp.toInstant().atZone(ZonedId.systemDefault());
System.out.println(t.toString);
```

>2018-04-16T08:12:59.626+03:00[Asia/Jerusalem]

While this may be compliant with ISO8601, the presence of the
timezone name (`ZoneId`) may be redundant, even though the 
documentation states that the offset prevails in case of 
discrepancy.

Thankfully, there is another class, [OffsetDateTime](https://docs.oracle.com/javase/8/docs/api/java/time/OffsetDateTime.html), which, as its name indicates, represents a date and time with an offset, but without a ZoneId. If we wanted to reproduce the former output without the ZoneId we could do:

```java
ZonedDateTime t = timestamp.toInstant().atZone(ZonedId.systemDefault());
System.out.println(t.toOffsetDateTime().toString());
```

Or directly:

```java
OffsetDateTime t = OffsetDateTime.ofInstant(timestamp.toInstant(), ZonedId.systemDefault());
System.out.println(t.toString());
```

Note that you may lose the beauty of using `Instant.atZone`, but you get to use the object that you need.