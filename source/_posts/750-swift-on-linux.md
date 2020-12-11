---
title: 'Zero to Project: Swift on Linux'
date: 2020-12-07 00:39:32
tags:
---

As I'm doing [Advent of Code 2020](https://adventofcode.com/2020), I wanted to try out server-side Swift on Ubuntu, knowing it would be limited without XCode. This turned out to take many searches to get right.

Visual Studio Code is the best option for a pseudo-IDE since IntelliJ AppCode is still limited ot Mac OSX only.

## Installing

- Installing Swift is easy enough: install the listed dependencies and download a release [here](https://swift.org/download/#releases), untar wherever you want and add it to PATH through `/etc/environment` (or put it in `/usr/bin`) and make sure `swift --version` works. For reference I get 5.3.1 as of writing this.
- In VSCode, install [this extension](https://marketplace.visualstudio.com/items?itemName=vknabel.vscode-swift-development-environment) (the only one with some level of maintaining)
- For autocompletion, the Swift toolchain also comes with its language server under `swift-5.3.1-RELEASE-ubuntu18.04/usr/bin/sourcekit-lsp`. In VSCode, set
  
  ```json
  "sde.languageServerMode": "sourcekit-lsp",
  "sourcekit-lsp.serverPath": "[path-to-swift]/usr/bin/sourcekit-lsp"
  ```

  If you're using the settings UI, these are under `Extensions > Swift Development Environment Configuration`.
  
  Note that while autocompletion is excellent, jumping to documentation or implementation is unavailable.
- To set up debugging with LLDB, follow [this article](https://www.vknabel.com/pages/Debugging-Swift-in-VS-Code/). It's fairly limited however, as you currently cannot see variables, but breakpoints and step-by-step debugging work just fine.
- SwiftLint is available [here]([SwiftLint](https://github.com/realm/SwiftLint#installation)) but difficult to setup on Linux.

## Creating a project

Basic setup (longer version [here](https://swift.org/getting-started/#using-the-package-manager)):

```bash
mkdir myproject
cd myproject
swift package init --type executable
swift build
swift test
swift run myproject
.build/x86_64-unknown-linux-gnu/debug/swift #prints "Hello, world!"
```

My resulting project is [here](https://github.com/fedidat/AdventOfCode2020/tree/master/swift). This includes some unit tests so it makes a good starter project as well.

## I'm new to Swift, what's next?

Now you can:
- Launch [the REPL](https://swift.org/getting-started/#using-the-repl) (just run `swift` in the terminal)
- Take the [language tour](https://docs.swift.org/swift-book/GuidedTour/GuidedTour.html) to get a basic idea of how things work
- Read on [the package manager](https://swift.org/package-manager/): declaring and importing dependencies, building configurations...
- From there on start using [the basic reference](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html) as needed.

I hope this helps!
