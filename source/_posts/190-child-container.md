---
title: 'LifecycleException: A child container failed during start'
date: 2018-05-13 07:56:10
tags:
---

Recently at work, I was trying to move the application's JARs outside of the launch command's working directory. And I had an exception with Spring Boot mysteriously refusing to start.

My company is behind an intranet so I can't quite post the full stack trace. But save for the traces themselves noted as `(...)`, this was the log:

```
[org.springframework.boot.SpringApplication] Application startup failed
org.springframework.context.ApplicationContextException: Unable to start embedded container; nested exception is org.springframework.boot.context.embedded.EmbeddedServletContainerException: Unable to start embedded Tomcat
    (...)
Caused by: org.springframework.boot.context.embedded.EmbeddedServletContainerException: Unable to start embedded Tomcat
    (...)
Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardServer[-1]]
    (...)
Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardService[Tomcat]]
    (...)
Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardService[Tomcat]]
    (...)
Caused by: org.apache.catalina.LifecycleException: A child container failed during start
    (...)
```

Mysterious and says nothing really. After trying every method on the Internet (which basically said "your Maven dependencies aren't bringing the current embedded Tomcat version - not my issue), I started "binary search debugging" by moving the classpath around, removing code, etc... and got to the following conclusion: The application works when `tomcat-embed-core.jar` is inside a subfolder of the command's working directory, not when outside. Fair enough, the JAR contains the actual classes for embedded Tomcat, but it sounded insane: why would a JAR's location matter?

After looking closely with the engineer who wrote the present architecture with me, we found that an old JAR had stayed in the classpath during the migration to Spring Boot and interfered with some class by the same name in tomcat-embed-core.jar. Then, when moving that JAR within a subfolder of the working directory, it randomly took precedence over the previous archive.

The reason it had worked before is that we had used a `MANIFEST.mf` instead of a "*.jar" classpath.

Solutions include:

* Maintaining a generated `MANIFEST.mf`. 
* Writing a hardcoded classpath of `tomcat-embed-core.jar;*.jar` to establish the precedence of tomcat-embed-core.jar.
* Getting rid of the problematic JAR after trial-and-error to find it, if possible. Or fixing the JAR otherwise.

Morale of the story: when weird things happen outside your code, check your classpaths and don't even rely on how they're written.