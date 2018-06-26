---
title: JDBC stops working after an hour (closed connection)
date: 2018-06-25 08:18:51
tags:
---

Back when starting a new project with JDBC and Spring Boot, we had a very hard time understanding why our connections consistently stopped working after an hour. Even narrowing it down to that time frame was tedious.

It turns out that idle connections are tossed out after an hour. But the fixes are easy, as noted [here](https://stackoverflow.com/a/35381306/9296017):

> **Option 1: Toss out broken connections from the pool.**
>
> Use these properties:
>
> ```yml
>    spring.datasource.test-on-borrow=true
>    spring.datasource.validation-query=SELECT 1;
>    spring.datasource.validation-interval=30000
> ```
>
> **Option 2: Keep connections in the pool alive.**
>
> Use these properties:
>
> ```yml
>    spring.datasource.test-while-idle=true
>    spring.datasource.validation-query=SELECT 1;
>    spring.datasource.time-between-eviction-runs-millis=60000
> ```
>
> **Option 3: Proactively toss out idle connections.**
> 
> Use these properties (Note: I was not able to find reliable documentation on this one for Spring Boot. Also the timeout is in seconds not milliseconds):
> 
> ```yml
>    spring.datasource.remove-abandoned=true
>    spring.datasource.remove-abandoned-timeout=60
> ```

This was very much non-obvious to us. We ended up going for the first option because it is the safest and cleanest.