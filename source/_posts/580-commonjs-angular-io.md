---
title: Using CommonJS modules with Angular 6
date: 2018-11-01 07:20:35
tags:
---

I wanted to use Jos de Jong's outstanding [JSON editor](https://github.com/josdejong/jsoneditor) module for a project and had no access to any Angular wrapper module. I also didn't have the time to write a module for it by myself.

So how do we use CommonJS Node modules (or other non-Angular modules) with Angular 2+?

1. Make sure the package actually exists in the project. Let's assume it is under `node_modules`.

2. The first thing to do is to head over the `angular.json` file (or `angular-cli.json` for Angular 5 and under) and add the script. For example with the `jsoneditor` and `bootstrap` packages under `node_modules`:

    ```json
    {
    "projects": {
        "ngTest": {
        "architect": {
            "build": {
            "options": {
                "styles": [
                "src/styles.scss",
                "node_modules/bootstrap/dist/css/bootstrap.min.css",
                "node_modules/jsoneditor/dist/jsoneditor.min.css"
                ],
                "scripts": [
                "node_modules/jsoneditor/dist/jsoneditor.min.js"
                ]
    ```

    I just printed the relevant hierarchy here. So pay attention not to insert the script to the `test` configuration like I stupidly did. And keep in mind that the path is relevant to the project's `sourceRoot`.

3. Try using the class in your Angular module. Because the script was added in `angular.json`, the code should work, but the compiler will complain that the class is unknown. And for good reason, since there is no way to import it yet.

4. Now create typings.d.ts if it does not exist. And add a declaration for your class, for example with `jsoneditor`'s class called `JSONEditor`:

    ```typescript
       declare var ClassName: any;
    ```

    Much like a C/C++ header, this is a way of telling the compiler "Don't worry, I'll provide an implementation of that class at runtime".

Now you should be able to import the class in your module, service or other without problem.