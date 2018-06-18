---
title: Why the order of repositories in Gradle might matter
date: 2018-06-18 08:18:49
tags:
---

Gradle came after the war, trying to reconcile the antique Ant scripts and the verbose but reliable Maven into yet another pseudo-magical framework that "just works". This can provoke some (a lot) of quirks.

My coworker is migrating our old Ant projects to Gradle and came across something weird. Having defined multiple Ivy and Maven repos based on several Nexus servers and different organizational separations, it turned how that this:

```groovy
repositories {
    our-group it
    external-releases it
}
```

failed the `gradle dependencies`, while this:

```groovy
repositories {
    external-releases it
    our-group it
}
```

passed and built successfully. Something was up. After reading [the docs about Gradle dependency resolution](https://docs.gradle.org/current/userguide/introduction_dependency_management.html#sec:dependency_resolution), we made sure we didn't upload different artifacts of the same version in different repos. 

Looking more closely at the unclear log in the problematic ordering, Gradle complains that one of our artifacts depends on `commons-beanutils.jar`, which in turn depends on `org.apache:apache:7`, for which the POM is missing, even though this artifact seems to be empty of any JAR.

It turns out that this release was under the same version in both repository, but in one of them it appeared as Maven, and in the other as Ivy, for which the layout [is different](https://docs.gradle.org/current/userguide/repository_types.html#sec:defining_custom_pattern_layout_for_an_ivy_repository) (another reason to abandon Ivy for us).

So, the order of repositories may be important, and some surprises may occur when Gradle decides to look at the newest version of transitive dependencies. This is why architectural consistency is so desirable. Lesson learned.