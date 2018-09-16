---
title: MultipartException on AWS EC2 after a few days
date: 2018-09-16 17:36:08
tags:
---

*Full disclosure: This issue was revealed to me by a coworker as he was working on [his Android game, MinMax](https://play.google.com/store/apps/details?id=il.co.blackout.blackout). He just released his [second game](https://play.google.com/store/apps/details?id=com.white.black.nonogram) today, check it out.*

-------

After some days of runtime, your Spring Boot application with Embedded Tomcat might mysteriously partly stop working, with the following exception:

    org.springframework.web.multipart.MultipartException: 
        Could not parse multipart servlet request; 
        nested exception is java.io.IOException: The temporary upload location 
        [**/tmp/tomcat.1220970741172837513.8080/work/Tomcat/localhost/ROOT]** is not valid
	    at org.springframework.web.multipart.support.StandardMultipartHttpServletRequest.parseRequest(StandardMultipartHttpServletRequest.java:111)

If you have this issue, you may want to check whether you're using multipart requests, as the exception indicates. That is, receiving `MultiValueMap`s in your endpoints. Tomcat serves these as file uploads by storing the parts in a temporary folder: under `/tmp/tomcat*`.

It turns out that various services may clear the `/tmp` folder every fixed number of days, as there should be no harm in throwing out old files. In my case, that was 10 days on AWS EC2.

In fact, there is an [issue](https://github.com/spring-projects/spring-boot/issues/9616) about this, asking Spring Boot to recreate the embedded Tomcat file handles if the `tmp` folder gets cleared.

The only solution found so far, until Spring solves the issue, is to make sure /tmp does not get cleared, by making it larger or not on the root partition.