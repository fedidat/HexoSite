---
title: Git ignore a folder without a subfolder
date: 2019-04-01 08:12:22
tags:
---

Say you are using git and want to ignore an entire folder, save for a specific file or subfolder within it. For example, you want to exclude IntelliJ's `.idea` folder but include its `runConfigurations` subfolder so that your team can run your launchers.

Now let's take a nested example:

    project
    +-- .gitignore
    +-- afolder
    |   +-- afile.txt
    |   +-- bfolder
    |   |   +-- bfile.txt
    |   |   +-- cfolder
    |   |   |   +-- cfile.txt

Within the `project` folder, we want to ignore all but `afolder/bfolder/cfolder/`. So we expect `afolder/afile.txt` and `afolder/bfolder/bfile.txt` to be ignored.

Now you might expect gitignore to work this way:

```gitignore
afolder/ #ignore afolder
!afolder/bfolder/cfolder #un-ignore cfolder
```

This doesn't work and instead excludes all of `afolder`. As noted in the [commit](https://github.com/git/git/commit/59856de171397c355923ee6cd6debae89385c824) of git introducing the `!` pattern:

    It is not possible to re-include a file if a parent directory of that file is excluded. (*)
    (*: unless certain conditions are met in git 2.8+, see below)
    Git doesn't list excluded directories for performance reasons, so any patterns on contained files have no effect, no matter where they are defined.

In 2016, there were two attempts to allow this kind of recursive un-exclusion, but they led to regressions and were reverted. There has been no progress since. So the way to do it is with this `.gitignore`:

```gitignore
afolder/*
!afolder/bfolder/
afolder/bfolder/*
!afolder/bfolder/cfolder/
```

Note that, in order to exclude, we use `folder/*`: this only excludes the contents of the folder but not the folder itself, allowing git to apply un-exclusion patterns (`!`). If we write `folder/`, we tell git to unconditionally ignore all of the folder.

Then we check that the result is achieved:

    > git status -u --ignored=matching
    On branch master

    No commits yet

    Changes to be committed:
    (use "git rm --cached <file>..." to unstage)

        new file:   .gitignore

    Untracked files:
    (use "git add <file>..." to include in what will be committed)

        afolder/bfolder/cfolder/cfile.txt

    Ignored files:
    (use "git add -f <file>..." to include in what will be committed)

        afolder/afile.txt
        afolder/bfolder/bfile.txt


In a more general way, this repeating pattern of "excluding then un-excluding" is unavoidable as of git 2.21 (February 2019).