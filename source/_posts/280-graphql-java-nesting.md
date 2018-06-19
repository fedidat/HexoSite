---
title: Organizing GraphQL-Java Mutations
date: 2018-06-17 19:27:51
tags:
---

## GraphQL-Java

[graphql-java](https://github.com/graphql-java/graphql-java) is a straightforward implementation of Facebook's [GraphQL](https://graphql.org/) in Java. "Straightforward" is a relative term here, given the simplicity of getting started with GraphQL in Javascript [using graphql-js](https://graphql.org/graphql-js/).

And yet, using the [Spring Boot starter project](https://github.com/graphql-java/graphql-spring-boot), and making great use of graphql-annotations, it is possible to come up with an elegant design. This allows JPA repositories to wire with ORMs like Hibernate, resulting in a transparent flow from the query to the database and back to the response.

## Organizing mutations

And yet, queries are only one of the four basic CRUD functions (create, *read*, update and delete). GraphQL blends the rest of the operations under the name of "mutations". This makes sense because GraphQL primarily aims to solve the problem of API discovery universally, and leaves the backend logic to the underlying implementations.

But any large organization knows you can't just throw thousands and thousands of operations together in one flat API. You expect some kind of hierarchy right? **[Wrong](https://github.com/graphql/graphql-js/issues/221)**. GraphQL officially does not support hierarchical design of mutations, and warns that nested mutations may not execute in a predictable order. 

This is particularly a problem for monolithic applications that can't easily be broken up into many microservices. Which leads to comical situations as GraphQL can represent a modern way to export data while migrating to microservices.

Now, you can disregard the spec and do it anyway, as did Jeff Lowery in JavaScript in this [article](https://medium.freecodecamp.org/organizing-graphql-mutations-653306699f3d), while underlining the issue of execution order. And I did the same in Java.

## The solution

As I mentioned earlier, I think GraphQL implementations should leverage graphql-annotation to provide a dynamic schema, conforming with the wise tradition of configuration-as-code.

I started with [Baeldung's tutorial for GraphQL with Java](http://www.baeldung.com/graphql), which uses this annotation schema, and will extend it with a hierarchical mutation.

So this is my original UserMutation:

```java UserMutation.java
package com.baeldung.graphql.mutation;

import graphql.annotations.GraphQLField;
import graphql.annotations.GraphQLName;
import graphql.schema.DataFetchingEnvironment;

import javax.validation.constraints.NotNull;

import com.baeldung.graphql.entity.User;
import com.baeldung.graphql.utils.SchemaUtils;

import java.util.List;
import java.util.Optional;

import static com.baeldung.graphql.handler.UserHandler.getUsers;

@GraphQLName(SchemaUtils.MUTATION)
public class UserMutation {
    @GraphQLField
    public static User createUser(final DataFetchingEnvironment env, @NotNull @GraphQLName(SchemaUtils.NAME) final String name, @NotNull @GraphQLName(SchemaUtils.EMAIL) final String email, @NotNull @GraphQLName(SchemaUtils.AGE) final String age) {
        List<User> users = getUsers(env);
        final User user = new User(name, email, Integer.valueOf(age));
        users.add(user);
        return user;
    }

    /*** More mutations... ***/
}
```

From there on, I create a RootMutation which will be the departing point of GraphQL's mutations in the application. It contains the user mutations as one field:

```java MutationRoot.java
package com.baeldung.graphql.mutation;

import com.baeldung.graphql.utils.SchemaUtils;
import graphql.annotations.GraphQLField;
import graphql.annotations.GraphQLName;

@GraphQLName(SchemaUtils.MUTATION)
public class RootMutation {
    static UserMutation userMutation = new UserMutation();

    @GraphQLField
    public static UserMutation user() {
        return userMutation;
    }
}
```

```diff UserSchema.java
     public UserSchema() throws IllegalAccessException, NoSuchMethodException, InstantiationException {
         schema = newSchema().query(GraphQLAnnotations.object(UserQuery.class))
-            .mutation(GraphQLAnnotations.object(UserMutation.class))
+            .mutation(GraphQLAnnotations.object(RootMutation.class))
             .build();
     }
```

Then this:

```json createUser.json
mutation($name: String! $email: String! $age: String! ){
    createUser ( name: $name email: $email age: $age) { 
        id 
        name
    } 
}
```

may become this:


```json createUserNested.json
mutation($name: String! $email: String! $age: String! ){
    user {
        create ( name: $name email: $email age: $age) { 
            id 
            name
        } 
    }
}
```

This is, in my eyes, a no-brainer in terms of layout, as well as in terms of consistency as it falls in line with the hierarchic structure of queries. I still can't find a strong enough reason for the spec not to support it in any manner.

As for the issue of hierarchical mutation ordering: I honestly don't know the internals of graphql-java but I know mutations don't run in different threads and I doubt that the iteration over them would execute as something else than DFS for field traversal. But it that's a concern to you I would suggest implementing another way of getting feedback about the order of execution, such as a sequence or reliable timestamp in the response.

[Link to the project](https://github.com/fedidat/graphql-java-hierarchical-mutation)