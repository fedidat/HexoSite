---
title: ConcurrentModificationException 	
date: 2018-04-16 20:16
updated: 2018-04-16 20:16
comments: true
tags:
- java
categories:
- programming
---

Even though I have been a server-side Java developer for years, I still (re)discover things that should sound basic: it turns out that you 
can't modify an array within a "for-each" loop!

Of course, you could argue that this might make sense, but
it caught me off guard. So that's one to remember.

For example, this code throws a `ConcurrentModificationException`:

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3));
numbers.forEach(n -> {
    if(n == 2)
        numbers.remove(n);
});
```

Now there are several solutions, depending on your needs and
programming styles.

## 1. Efficiency

If you have Java 8 and you find the syntax elegant enough in your use-case, removeIf is by far the best method, as it is safe and efficient:

```java
numbers.removeIf(n -> n == 2);
```

Otherwise, Java iterators are very fast (if used correctly), but rather 
old-fashioned and not exactly the clearest or prettiest syntax.

```java
for(Iterator<Integer> numIt = numbers.iterator(); numIt.hasNext();) {
    Integer n = numIt.next();
    if(n == 2)
        numIt.remove();
}
```

**Note: using remove repeatedly on array types is a bad idea because it [shifts all following elements](https://stackoverflow.com/questions/33182102/difference-in-lambda-performances) for every call.**

## 2. Complexity

If you must perform complex operations and the removeIf method doesn't suit your needs, you should consider the accumulate-then-remove method:

```java
List<Integer> toRemove = new ArrayList<>();
numbers.forEach(n -> {
    if(n == 2) {
        toRemove.add(n);
    }
});
numbers.removeAll(toRemove);
```

However, this solution is less relevant if you must perform additional operations (as in my case, where I had to both remove the object from one list and add it to another), and want to save the overhead of the extra object.

## 3. Functionality

Literally! If functional programming is adapted to your needs, you could use stream filtering or mapping:

```java
numbers = numbers
        .stream()
        .filter(n -> n != 2)
        .collect(Collectors.toList());
```

# Conclusion

You may find other specific solutions such as `myMap.keySet().retainAll(myList)` when using Maps, manipulating arrays directly, 
or using java.util.Concurrent collections or others such as CopyOnWriteArrayList. But these are the solutions that I have found for general use-cases.