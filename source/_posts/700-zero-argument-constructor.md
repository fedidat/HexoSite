---
title: 'JUnit: Test class should have exactly one public zero-argument constructor'
date: 2019-05-13 07:20:10
tags:
---

I was changing some tests in our codebase today. My existing test was parameterized and looked something like this:

```java
@RunWith(Parameterized.class)
public class FibonacciTests {
    @Parameterized.Parameters(name= "{index}: fib[{0}]={1}")
    public static Iterable<Object[]> data() {
        return Arrays.asList(new Object[][] { { 0, 0 }, { 1, 1 }, { 2, 1 },
                { 3, 2 }, { 4, 3 }, { 5, 5 }, { 6, 8 } });
    }

    private int fInput, fExpected;
    public FibonacciTests(int input, int expected) {
        fInput= input;
        fExpected= expected;
    }

    @Test
    public void test() {
        assertEquals(fExpected, Fibonacci.compute(fInput));
    }
}

```

I wanted to one-off batch-process different data that I extracted from production DB to a file. I removed the `@Parameters` annotation and a matching `@RunWith(Parameters.class)` at the test suite caller, because I no longer wanted the test to be parameterized, then I got lazy and left the rest of the test (constructors, other tests, etc), like this:

```java
public class FibonacciTests {
    private int fInput, fExpected;
    public FibonacciTests(int input, int expected) {
        fInput= input;
        fExpected= expected;
    }

    @Test
    public void test() {
        assertEquals(fExpected, Fibonacci.compute(fInput));
    }

    @Test
    public void testFile() throws IOException {
        Files.lines(Paths.get("input.txt")).forEach(l -> {
            String[] pair = l.split(" ");
            int input = Integer.valueOf(pair[0]);
            int expected = Integer.valueOf(pair[1]);
            assertEquals(expected, Fibonacci.compute(input));
        });
    }
}
```

I ran the test and was greeted with the following failure:

    run-test:
    [junit] Testsuite: demo.TestSuite
    [junit] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 0.077 sec
    [junit]
    [junit] Testcase: initializationError took 0.014 sec
    [junit] Caused an ERROR
    [junit] Test class should have exactly one public zero-argument constructor
    [junit] java.lang.Exception: Test class should have exactly one public zero-argument constructor
    [junit] at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
    [junit] at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
    [junit]
    [junit] Test demo.TestSuite FAILED

It took me a good 5 minutes to understand what was wrong. Removing the `@Parameters`, along with `@RunWith(Parameters.class)`, meant that JUnit no longer relied on the constructor with arguments, and instead tried to initialize the test class with a zero-argument constructor.

But as the [Java documentation](https://docs.oracle.com/javase/tutorial/java/javaOO/constructors.html) says, the Java compiler provides a default constructor to classes when no constructors exist. In my case, since the test class had just one constructor with arguments, and no explicit default constructor, there is no longer an implicit default constructor, so the class can no longer be constructed by JUnit. 

In retrospect, this seems obvious. But that message was not clear to me.

So what's the solution? Depends on the case. In mine, I only wanted to run the batch test so I had to remove the parameterized constructor, which meant I once again had a default constructor provided by the compiler. In another situation, I might have had to add my own explicit default constructor.

In a more general manner, **check the existence of a parameterized constructors without an explicit default constructor**.