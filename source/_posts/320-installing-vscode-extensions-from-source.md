---
title: Installing VSCode extensions from source (.vsixmanifest)
date: 2018-06-25 08:12:17
tags:
---

As I've mentioned in the past, my organization is behind an intranet. So sometimes all we have is the source repo of a Visual Studio Code extension. These have a .vsixmanifest in the root folder and an `extensions/package.json` file. And I had struggled a bit with installing these easily.

The first option is to package the extensions. Just zip the folder and rename the archive to .vsix, then you can install it under `Extensions > ... > Install from VSIX...`.

The second option, which I recommend, is simply copy/pasting the folder as-is into `~/.vscode/extensions` (or `C:\Users\[user]\extensions` in Windows, a.k.a `%USERPROFILE%\extensions`), then reload VS Code.