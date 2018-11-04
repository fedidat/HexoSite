---
title: 'Typescript "Type String cannot be used as an index"'
date: 2018-10-28 07:12:21
tags:
---

While participating in a hackathon at my workplace, I made an Angular 6 app that acted as a UI for a service.  I tried to use a `String` variable as a key for a map, as follows:

```typescript
myMap: { [key: string]: string; }  = {"1": "test", "2": "test2"};
  
getValue(idx: String): string {
    return this.myMap[idx];
}

constructor() {
    console.log(this.getValue("1"));
}
```

This does work under `ng serve`, but the compiler would be very unhappy about it:

```
ERROR in src/app/app-routing.module.ts(15,25): error TS2538: Type 'String' cannot be used as an index type.
```

This meant that I could not run `ng build --prod` to get static files. After looking on the Internet, solutions were unclear.

Eventually, I realized that Typescript had a problem using a `String` as an index for Objects, but not the primitive `string`. So it was just a matter of **using the String::toString method on the index**:

```typescript
getValue(idx: String): string {
    return this.myMap[idx.toString()];
}
```

Of course, it's best to use the ES6+ Map class, which does accept `String`s (or any other Object) as indices. In our example:

```typescript
myMap: Map<String, String> = new Map([["1", "test"], ["2", "test2"]]);
  
getValue(idx: String): String {
    return this.myMap.get(idx);
}

constructor() {
    console.log(this.getValue("1"));
}
```

But I wanted to keep compatibility with older browsers, at least without polyfills, and I didn't actually need non-primitive (Object) types. I also was directly parsing simple but huge JSON files. So Javascript objects were just right.