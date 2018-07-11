---
title: Find all the permutations of a String in Java
date: 2018-07-11 19:11:51
tags:
---

I am doing some challenges on Advent of Code 2017. I highly recommend it if you're looking for cool and short puzzles.

On one of the challenges I was looking for ways to find all the permutations of a word, also known as its anagrams. I found some interesting solutions on StackOverflow as usual, but nothing that caught my eye for elegance. Most solutions involved blind deep recursion with prefixes.

So I got sitting and thought about it, looking for an elegant, clear and concise iterative implementation. So how do you sit and find the possible combinations of an ensemble? Let's say `[red, blue, green]`.

* **Step 1**: You probably start by fetching some member. Let's say `[blue]`. That's your working set for now.
* **Step 2**: Now you get another member, for example `red` and try the different possibilities for inserting it in the existing set: either before `blue` or after it. At this point you have `[[red, blue], [blue, red]]`.
* **Step 3**: Now you get working with the set resulting from Step 2, and try inserting yet another member: `green`. You can do so either at the beginning, in the middle or at the end. That's:
    1. `[[green, red, blue], [red, green, blue], [red, blue, green]]` for the first member of Step 2,
    2. `[[green, blue, red], [blue, green, red], [blue, red, green]]` for the second.

And so on until we run out of members. Note that there is no Step 0 since it doesn't matter which member we start with.

So let's formalize, generalize and recap with pseudocode:

```
anagrama(s)
    permutations = [first char of s]
    1. for each character 'c' in s after the first
        1.1. newPermutations = []
        1.2. for each existing permutation 'word'
            1.2.1. for each possible insertion point 'i'
                1.2.1.1. newWord = 'word' with 'c' inserted at 'i'
                1.2.1.2. newPermutations.add(newWord)
        1.3. permutations = newPermutations
    return permutations
```

And this is my Java implementation:

```java
public List<String> anagrams(String s) {
    List<String> permutations = new ArrayList<>();
    permutations.add(String.valueOf(s.charAt(0)));
    for(char c : s.substring(1).toCharArray()) {
        List<String> longerPerms = new ArrayList<>();
        for(String p : permutations) {
            for(int i=0; i<=p.length(); i++)
                longerPerms.add(p.substring(0, i) + c + p.substring(i));
        }
        permutations = longerPerms;
    }
    return permutations;
}
```

A note on runtime: each character creates 1,2,...,n additional string each time, with the addition operation taking O(1), resulting in total O(n!) runtime.

There is also no need for Sets since this efficient algorithm may produce no duplicates as long as each character only appears once. A HashSet may be used otherwise, or a TreeSet if the results need to be sorted, at the price of O(logn) operations rather than amortized constant runtime.

And of course, this algorithm works just as well to iterate over the combinations of any kind of collection.