---
title: ISO-8601 Timestamp missing seconds
date: 2018-06-25 08:16:49
tags:
---

One of our services at work returns a Java OffsetDateTime  to a legacy system that parses it. OffsetDateTime is the ubiquitous compliant with the [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) standard, used for example in JavaScript, for which the full standard is sadly paywalled. Other variants exist in Java 8+, such as ZonedDateTime (similar but the timezone is spelled out instead of appearing as a digital offset) or LocalDateTime (entirely foregoing the time zone).

During integration tests, a bizarre thing occurred. Some entity was marked with an OffsetDateTime that read similar to this: `2018-06-20T19:08+03:00`. Notice something missing? The seconds and milliseconds! The legacy system could not parse it, expecting a second colon after the minutes.

Sure enough, the following Kotlin code produced the same timestamp:

```kotlin
    println(OffsetDateTime.of(2018, 6, 20, 19, 8, 0, 0, ZoneOffset.ofHours(3)))
```

So what's up? It turns that not only milliseconds and seconds, but even minutes may be omitted if the next units are zero. Hours are however guaranteed to be shown. So make sure to support these cases.