---
title: JUnit 4 and 5
date: 2019-09-16 07:48:31
tags:
---

Junit 4 has been released in 2006 and Junit 5 in 2017. Despite that, Junit 4 usually remains the default today. Here is a short and intuitive summary of the major reasons to migrate to Junit 5.

## 1. Lambdas 

Lambdas allow you to write more intuitive assertions and assumptions (although old-style syntax is fully supported). You can also group assertions. This:

```java
Assume.assumeTrue(testMessage.getProtocol().equals("HTTP"));
Assert.assertThat(testMessage.getFrom(), startsWith("http://"));
Assert.assertThat(testMessage.getTo(), startsWith("http://"));
```

may become:

```java
Assumptions.assumingThat(testMessage.getProtocol().equals(("HTTP"),
        () -> Assertions.assertAll(() -> {
            Assertions.assertTrue(testMessage.getFrom().startsWith("http://"),
                    "HTTP message From does not start with http prefix");
            Assertions.assertTrue(testMessage.getTo().startsWith("http://"),
                    "HTTP message To does not start with http prefix");
        })
);
```

## 2. @Tag

@Tag replaces @Category, saving the need to define interfaces:

```java
public interface OnlyRunWithIntegrationTests {
}
public class Tests {
    @Test
    @Category(IntegrationTest.class)
    public void testUpdateRemoteDatabase() {
        //Integration-related code
    }
}
```

becomes

```java
public class TestsJunit5 {
    @Test
    @Tag("OnlyRunWithIntegrationTests")
    public void testUpdateRemoteDatabase() {
        //Integration-related code
    }
}
```

Both can for example be used through command line parameters or IDE integration. This allows granular control in defining test groups, which can be useful in particular in integration test suites.

## 3. @Nested:

This annotation allows you to write logically grouped tests and define shared logic or attributes for these groups. For example:

```java
public class DBTests {
    Connection connection; //This attribute can be used by nested tests

    @BeforeEach
    public void beforeEach() {
        //Setup shared by ALL tests
        this.connection = DataSource.getConnection();
    }

    @Nested
    class WhereMethods {
        public static final SELECT_TEMPLATE = "SELECT count(*) FROM users WHERE id=?";
        
        @Test
        void test_WhereX() {
            //Test select where X
        }
        @Test
        void test_WhereY() {
            //Test select where Y
        }
    }

    @Nested
    class UpdateMethods {
        public static final UPDATE_TEMPLATE = "UPDATE users SET name=? WHERE id=?";
        @Test
        void test_UpdateX() {
            //Test update with X
        }
        @Test
        void test_UpdateY() {
            //Test update with Y
        }
    }
}
```

## Conclusion

There are [other future-proofing reasons](https://developer.ibm.com/dwblog/2017/top-five-reasons-to-use-junit-5-java/) to use Junit 5 over 4, and projects can mix between them so it's better to get used to the future testing platform.

With that said, the advantages of Junit 5 are minor unless you need them, which justifies its relatively slow adoption. But thanks to the retro-compatibility of Junit 5, a migration is not necessary or it can be done progressively and painlessly while bringing immediate benefits.
