---
title: Using Visual Studio Code with Java
date: 2018-07-07 22:23:46
tags:
---

Using Visual Studio Code with Java is easy but has a couple gotchas.

First of all, the first time you open or save a `.java` file, you will be suggested with THE extension for Java support, Microsoft's [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack), which includes some other extensions by Redhat and others to bring Java support from Eclipse.

After installing it, Eclipse projects will immediately be supported, using `.classpath` or `.project` files. If they are not present, you will see this error:

![Classpath is incomplete. Only syntax errors will be reported](/images/400-vscode-java/classpath.png)

What if you are you are starting from scratch, bringing some IntelliJ IDEA project or for some other reason are missing the `.project` or `.classpath` file? If you have a `pom.xml`  or `build.gradle` file in your path, VSCode will create the Eclipse files for you anyway based on the Maven or Gradle configuration, as well as bring all your dependencies and so on if necessary.

Note with Gradle: you may have to define your source sets more explicitly than with Maven, otherwise your source files will not be recognized and you will get the classpath error mentioned above.

Then, you can easily debug, with VSCode automatically creating some default debug configurations inside the usual `.vscode/launch.json`.

Note: If you need command-line input, you will want to define `"console": "internalTerminal"` in that `launch.json` file.

Finally, JUnit support is also built in the Java Extension Pack. Also, if you are using VSCode to edit Gradle files, I highly recommend the [Gradle Language Support](https://marketplace.visualstudio.com/items?itemName=naco-siren.gradle-language), bringing features like "Syntax Highlighting, Keyword Auto-completion Proposals and Duplication Validation".