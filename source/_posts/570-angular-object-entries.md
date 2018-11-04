---
title: 'Angular: "TypeError: Object.entries is not a function"'
date: 2018-10-25 07:20:18
tags:
---

I wanted to iterate over a map just like in my [previous article](/550-typescript-string-index) and used Object.entries in the following manner:

```typescript
myMap: { [key: string]: string; }  = {"1": "test", "2": "test2"};
  
printValues(idx: String): string {
    for(const [key, value] of Object.entries(this.myMap)){
        console.log(key, value);
    }
}

constructor() {
    console.log(this.printValues());
}
```

Sadly, old browsers do not support this ES7 feature, failing with the error `TypeError: Object.entries is not a function`. I did want to support these browsers however, rather than repeatedly repeatedly users on out intranet how to update their browser, or getting our IT to setup reliable automatic update.

One solution suggested by most Internet resources is to use `Object.keys` as follows:

```typescript
for(const key in Object.keys(this.myMap)){
    console.log(key, this.myMap[key]);
}
```

But I was used to being able to transpile modern code into ES5 with React and Babel and wanted to do the same with Typescript.

It turns out that Angular's solution to this problem is that it comes with the ability to inject polyfills. `ng create` already generates projects with `src/polyfills.ts` as shown [here](https://github.com/gdi2290/angular-starter/blob/master/src/polyfills.browser.ts).

What we need here is `core-js`'s ES7 Object polyfill. So we just need we add this line to `src/polyfills.ts`:

```typescript
import 'core-js/es7/object';
```

And it works!