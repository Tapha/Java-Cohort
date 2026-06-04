# 🗄️ Exercise: From Collections to ORM ☕️⚙️

## Goal

By the end of this exercise, you should be able to explain how Java moves between:

```text
objects in memory
↔
collections of objects
↔
database tables
↔
ORM/JPA entities
```

This exercise is not just about memorizing annotations.

It is about understanding the bridge between Java memory and database storage.

---

# Part 1 — Objects vs Rows 🧠

Look at this Java class:

```java
public class User {
    private Long id;
    private String email;
    private String name;

    public User(Long id, String email, String name) {
        this.id = id;
        this.email = email;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public String getEmail() {
        return email;
    }

    public String getName() {
        return name;
    }
}
```

And this database table:

```text
users
+----+-------------------+-------+
| id | email             | name  |
+----+-------------------+-------+
| 1  | amina@example.com | Amina |
| 2  | david@example.com | David |
+----+-------------------+-------+
```

## Questions

1. What does the `User` class represent in Java?
2. What does the `users` table represent in the database?
3. What does one row in the `users` table correspond to in Java?
4. What does one column in the table correspond to in Java?
5. Complete this mapping:

| Database    | Java |
| ----------- | ---- |
| table       | ?    |
| row         | ?    |
| column      | ?    |
| primary key | ?    |

---

# Part 2 — Turn a Class Into an Entity 🏷️

A normal Java class becomes a JPA entity when we tell JPA that it should be mapped to a database table.

Complete the missing annotations:

```java
// Add annotation here
public class User {

    // Add annotation here
    private Long id;

    private String email;
    private String name;

    public User() {
    }

    public User(String email, String name) {
        this.email = email;
        this.name = name;
    }
}
```

## Questions

1. Which annotation marks a class as an entity?
2. Which annotation marks the primary key field?
3. Why does JPA usually need a no-argument constructor?
4. In your own words, what is an entity?

Use this sentence starter:

```text
An entity is a Java object that...
```

---

# Part 3 — One Row, One Object 🧱

Imagine this database row:

```text
+----+-------------------+-------+
| id | email             | name  |
+----+-------------------+-------+
| 3  | sara@example.com  | Sara  |
+----+-------------------+-------+
```

## Task

Write the Java object that represents this row.

```java
User user = new User(?, ?, ?);
```

## Questions

1. Where does the `User` object live while the Java program is running?
2. Where does the row live long-term?
3. What does ORM help translate between?

---

# Part 4 — Many Rows, Collection of Objects 📚

A database table can contain many rows.

JPA can return many rows as a collection of entity objects.

Example:

```java
List<User> users = userRepository.findAll();
```

## Questions

1. What does `List<User>` mean?
2. If the `users` table has 100 rows, what might `findAll()` return?
3. Complete the flow:

```text
users table
        ↓
? rows
        ↓
ORM maps each row
        ↓
? objects
        ↓
List<?>
```

4. Why did we learn collections before ORM?

---

# Part 5 — Repository as Persistence Boundary 🔎

A repository is responsible for data access.

Complete this interface:

```java
public interface UserRepository {

    User findByEmail(String email);

    List<User> findAll();

    User save(User user);
}
```

## Questions

1. What is the job of a repository?
2. Should a repository send welcome emails? Why or why not?
3. Should a repository calculate business discounts? Why or why not?
4. Which SOLID principle helps explain this?
5. Complete this sentence:

```text
Repository = boundary between ? and ?
```

---

# Part 6 — Service vs Repository ⚖️

Look at this design:

```java
public class UserService {

    public void registerUser(String email, String name) {
        // validate email
        // build SQL query
        // connect to database
        // insert user
        // send welcome email
        // generate report
    }
}
```

## Questions

1. What responsibilities are mixed together here?
2. Why could this become difficult to maintain?
3. Which part should belong to a validator?
4. Which part should belong to a repository?
5. Which part should belong to a message sender?
6. Which part should belong to a report service?

## Refactor Plan

Fill in the responsibility table:

| Responsibility            | Better Class |
| ------------------------- | ------------ |
| validate email            | ?            |
| save user                 | ?            |
| send welcome email        | ?            |
| generate user report      | ?            |
| coordinate the whole flow | ?            |

---

# Part 7 — Registration Flow With ORM 🧩

Complete the missing code:

```java
public class UserRegistrationService {

    private final EmailValidator validator;
    private final UserRepository userRepository;
    private final MessageSender messageSender;

    public UserRegistrationService(
        EmailValidator validator,
        UserRepository userRepository,
        MessageSender messageSender
    ) {
        this.validator = validator;
        this.userRepository = userRepository;
        this.messageSender = messageSender;
    }

    public void register(String email, String name) {
        // 1. validate email

        // 2. create User object

        // 3. save User through repository

        // 4. send welcome message
    }
}
```

## Questions

1. What is the role of `UserRegistrationService`?
2. Does it directly know SQL?
3. Does it directly know how messages are sent?
4. Which objects does it depend on?
5. Is this composition? Explain.

---

# Part 8 — Persistence Context 🧠

JPA has something called the persistence context.

A simple definition:

```text
Persistence Context = a managed in-memory space for entities
```

Imagine this flow:

```java
User user1 = userRepository.findById(1L);
User user2 = userRepository.findById(1L);
```

Inside the same persistence context, JPA may return the same managed entity instance for the same database identity.

## Questions

1. What does the persistence context manage?
2. Why is it useful for JPA to track entities in memory?
3. What does it mean for an entity to be “managed”?
4. Complete this sentence:

```text
The persistence context connects database identity to...
```

---

# Part 9 — Entity Lifecycle 🔄

Entities can exist in different states.

Match each state to its meaning.

| State           | Meaning |
| --------------- | ------- |
| New / transient | ?       |
| Managed         | ?       |
| Detached        | ?       |
| Removed         | ?       |

Meanings:

```text
A. JPA is tracking the entity
B. Object exists in memory but has not been saved yet
C. Entity is marked for deletion
D. Object exists but JPA is no longer tracking it
```

## Questions

1. What state is this object probably in?

```java
User user = new User("amina@example.com", "Amina");
```

2. What might happen after this?

```java
userRepository.save(user);
```

3. Why does entity lifecycle matter?

---

# Part 10 — Dirty Checking 🧼

Dirty checking means JPA can detect that a managed entity has changed.

Look at this code:

```java
User user = userRepository.findById(1L);
user.changeName("Amina S");
```

Assume this happens inside a transaction and the entity is managed.

## Questions

1. What changed in memory?
2. What can JPA detect?
3. What SQL might eventually happen?
4. Why can dirty checking be powerful?
5. Why can it also be confusing?

Complete the flow:

```text
Managed entity changes in memory
        ↓
JPA detects ?
        ↓
SQL ? happens
        ↓
Database row changes
```

---

# Part 11 — Relationships 🕸️

A user can have many orders.

In Java, this may look like:

```java
public class User {
    private List<Order> orders;
}
```

In the database, it may look like:

```text
users table
orders table
orders.user_id → users.id
```

## Questions

1. What does `List<Order>` represent in Java?
2. What does `orders.user_id` represent in the database?
3. What kind of relationship is this?
4. Complete the mapping:

```text
One User object
        ↓
Many ? objects
        ↓
List<?>
```

---

# Part 12 — Lazy Loading ⚠️

Lazy loading means related data is not loaded until it is needed.

Example:

```java
User user = userRepository.findById(1L);

List<Order> orders = user.getOrders();
```

## Questions

1. What might be loaded first?
2. What might be loaded later?
3. Why can lazy loading improve performance?
4. Why can lazy loading cause surprise queries?
5. Complete this sentence:

```text
ORM can make database access look like...
```

---

# Part 13 — The N+1 Problem 🧨

Look at this code:

```java
List<User> users = userRepository.findAll();

for (User user : users) {
    System.out.println(user.getOrders().size());
}
```

Assume there are 100 users.

## Questions

1. What is the first query likely to load?
2. What might happen when `user.getOrders()` is called for each user?
3. How many total queries could happen in the bad case?
4. Why is this expensive?
5. Explain the N+1 problem in your own words.

Use this sentence starter:

```text
The N+1 problem happens when...
```

---

# Part 14 — ORM and SOLID 🧱

Fill in the table.

| SOLID Principle | ORM Meaning |
| --------------- | ----------- |
| SRP             | ?           |
| OCP             | ?           |
| LSP             | ?           |
| ISP             | ?           |
| DIP             | ?           |

Use these ideas:

```text
repositories should not contain business logic
services should depend on repository abstractions
entity models should tell the truth
repository interfaces should stay focused
new persistence behavior should not break core logic
```

---

# Part 15 — Final Reflection 🚀

Answer these in your own words.

1. What is ORM?
2. Why do Java applications need ORM or something like it?
3. What is the relationship between a table and a class?
4. What is the relationship between a row and an object?
5. What is the relationship between many rows and a collection?
6. What does a repository do?
7. What is the persistence context?
8. Why is lazy loading both useful and dangerous?
9. Why does ORM not remove the need to understand databases?
10. Explain this sentence:

```text
ORM is a system for synchronizing object state with database state over time.
```

---

# Stretch Challenge 🌟

Design a small ORM-style model for an online shop.

You need:

* `Customer`
* `Order`
* `OrderItem`
* `Product`
* `CustomerRepository`
* `OrderRepository`
* `CheckoutService`

## Tasks

1. Decide which classes are entities.
2. Decide which classes are repositories.
3. Decide which class coordinates the checkout flow.
4. Identify one one-to-many relationship.
5. Identify one many-to-one relationship.
6. Show where a `List<OrderItem>` might appear.
7. Explain where lazy loading could happen.
8. Explain where the N+1 problem might appear.

---

# Final Compression 🧠

```text
Object = one structured thing in memory
Collection = many objects in memory
Table = many records in storage
Row = one record in storage
Entity = Java object mapped to a table
Repository = persistence boundary
ORM = object world ↔ database world
Persistence Context = managed entity memory zone
Dirty Checking = detects changed objects
Lazy Loading = loads related data later
N+1 = hidden query explosion
```

Use this as your map.
