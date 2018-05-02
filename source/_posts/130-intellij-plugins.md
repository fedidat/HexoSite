---
title: 5 IntelliJ IDEA plugins for productivity
date: 2018-04-30 22:20:32
tags:
---

Here are 5 great but lesser known IntelliJ IDEA plugins, especially when starting out:

## 1. [IDE Features Trainer](https://plugins.jetbrains.com/plugin/8554-ide-features-trainer)

This lets you learn IntelliJ features and shortcuts interactively. No more StackOverflow "best shortcuts" or boring talks. Learn on the job.

![IDE Features Trainer demo](/images/130-intellij-plugins/features-trainer.png)

## 2. [GsonFormat](https://plugins.jetbrains.com/plugin/7654-gsonformat)

You input a JSON String and this plugin automatically makes a class out of it, with inner classes, serialized other entities, etc. You can tune it just before generating to neatly tune the fields.

It will be available in the `Code > Generate` menu, or directly under the `Alt+S` shortcut.

![GsonFormat demo](/images/130-intellij-plugins/gson-format.gif)

## 3. [RegexpTester](https://plugins.jetbrains.com/plugin/2917-regexptester)

The equivalent of the dozens of websites for testing regular expressions, but ready for Java and with hover explanations. Compatible with find, match, split and replace, with suggestions for flags and such.

![Regexp Tester demo](/images/130-intellij-plugins/regexp-tester.png)

## 4. [Key Promoter](https://plugins.jetbrains.com/plugin/4455-key-promoter) or [Key Promoter X](https://plugins.jetbrains.com/plugin/9792-key-promoter-x)

Key Promoter follows your mouse activity and shows "top missed shortcuts" so you can learn how to do what you do more efficiently. Updated in 2017 from a fork.

Key Promoter X is a superior version that creates notifications whenever you "miss" a keyboard and lets you dismiss them selectively. It also proposes to create shortcuts and has the regular "Key Promoter" side panel.

![Key Promoter X demo1](/images/130-intellij-plugins/key-promoter-x.png)

![Key Promoter X demo2](/images/130-intellij-plugins/key-promoter-x2.png)

## 5. [Math folding](https://plugins.jetbrains.com/plugin/9293-math-folding)

Represents math formulas when folding lines it detects, for example:

```java
Math.sqrt(Math.abs(10 - a) + Math.cbrt(5 + Math.pow(5, 3))) 
```

becomes

    √(|10 - a| + ∛(5 + 5³)) 

![Math folding](/images/130-intellij-plugins/math-folding.png)