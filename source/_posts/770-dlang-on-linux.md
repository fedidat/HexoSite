---
title: 'Zero to Project: Dlang on Linux'
date: 2020-12-14 01:06:03
tags:
---

Continuing with how to setup languages, this time with Dlang through DMD.

## Choosing an installation

First, you have to [choose a compiler](https://dlang.org/download.html). DMD is most typical, although GDC (based on GCC) and LDC (based on LLVM) offer improved performance and more architectures if you need them. We'll consider DMD. You will also want to install DUB, Dlang's package and build management system.

Typically, you can choose between downloading binaries, setting up through a script or using your package manager. The package is actually the best option as it includes full binaries for editors and adds executables to PATH rather than setup activation scripts. It also makes package management easier.

    sudo wget https://netcologne.dl.sourceforge.net/project/d-apt/files/d-apt.list -O /etc/apt/sources.list.d/d-apt.list
    sudo apt-get update --allow-insecure-repositories
    sudo apt-get -y --allow-unauthenticated install --reinstall d-apt-keyring
    sudo apt-get update && sudo apt-get install dmd-compiler dub

After that, `dmd --version` and `dub --version` should work fine.

## Setting up

There are actually [many ways to develop with Dlang](https://tour.dlang.org/tour/en/welcome/install-d-locally) but this will focus on the more popular approaches on Linux: Visual Studio Code and IntelliJ. Of those two I recommend IntelliJ, although neither option is exactly good.

### Option 1: IntelliJ

- Make sure you have the right version of some IntelliJ IDE installed, for example IDEA Community or CLion. Check [here](https://plugins.jetbrains.com/plugin/8115-d-language/versions/stable). At the time of writing, IntelliJ is on version 2020.3 but the plugin only supports 2020.2.*. You can get older versions of IntelliJ [here](https://www.jetbrains.com/idea/download/other.html).
- Install the [D plugin](https://github.com/intellij-dlanguage/intellij-dlanguage), it should appear in the plugin marketplace if you type "D Lang" if your version is supported.
- Create a new Dlang project or import one. The plugin should prompt to setup an SDK. Provide it with a path to DMD (`/usr/bin/dmd` if you installed the apt package) and DUB, which it should be able to auto-detect.

That's it, now build the module and the plugin should automatically create run configurations to compile, run DUB and run D app. Note that executing one of the run targets in debug will prompt to configure the path to GDB. This may be the occasion to provide other paths such as DScanner, DBD, DFormat or DFix, depending how well you want to set up your workspace.

### Option 2: Visual Studio Code

- If you have not already installed VS Code go to https://code.visualstudio.com/docs/setup/linux
- Install the [D Programming Language](https://marketplace.visualstudio.com/items?itemName=webfreak.code-d) extension, the only currently active and de-facto standard extension for Dlang, known as code-d.
- Opening a `.d` file will automatically install serve-d, the companion language server. This should provide rudimentary autocompletion, linting and building facilities. The extensions docs should open automatically as well for more details. To see it do `Ctrl-Shift-P -> code-d: Open User Guide / Documentation`.
- To create a new project, do `Ctrl-Shift-P -> code-d: Create new Project`.

You can then do Ctrl-Shift-B to build or run your application, this runs `dub build` and `dub run` respectively. You can also run `./dlang` to run the produced binary.

To debug using the CodeLLDB extension, you can use my [launch.json and tasks.json](https://github.com/fedidat/AdventOfCode2020/tree/master/dlang/.vscode).

## Dependencies

Adding dependencies is very simple. For example to add [vibe-d](https://code.dlang.org/packages/vibe-d), a popular web, async and concurrency toolkit:

    dub add vibe-d

which adds the dependency to `dub.json`.

## Unit tests

Basic unit testing can be achieved by adding functions with the unittest headers to any `.d` file, e.g

    unittest
    {
        assert(1 + 1 == 2);
    }

The run `dub test`. But you get no control whatsoever beyond success/failure. For more, packages like [unit-threaded](https://code.dlang.org/packages/unit-threaded) or [dunit](https://code.dlang.org/packages/dunit) offer more advanced unit testing.

## I'm new to Dlang, what's next?

- Take the [language tour](https://tour.dlang.org/) to get a basic idea of how things work. It's very short and helpful. D is a small and concise language.
- From there on use the [the reference](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/) as needed. It looks rough at first but when you know what you're looking for, it's actually the resource you'll use the most until you're fluent.
- Whatever isn't covered in those two will inevitably be mentioned on the very active [community forums](https://forum.dlang.org/).
