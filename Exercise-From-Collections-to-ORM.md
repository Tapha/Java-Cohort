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
| table       | entity    |
| row         | object  |
| column      | property    |
| primary key | ID    |

```text
1. The user class reprsents a user object
2. The users table represents a table in the database
3. One row in the users table corresponds to one object in java
4. One column in the table corresponds to one field or property in java

```

---

# Part 2 — Turn a Class Into an Entity 🏷️

A normal Java class becomes a JPA entity when we tell JPA that it should be mapped to a database table.

Complete the missing annotations:

```java
@Entity
public class User {

    @Id
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

```text
1. @Entity
2. @Id
3. Provides neutral ground for JPA to map a row onto??
4. An entity is a Java object that maps to a database table
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
User user = new User(3, "sara@example.com", "Sara");
```

## Questions

1. Where does the `User` object live while the Java program is running?
2. Where does the row live long-term?
3. What does ORM help translate between?


```text
1. The User object lives on the heap during runtime
2. The row lives on the database long-term
3. The ORM helps translate between the Objects and the Rows
```
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
many rows
        ↓
ORM maps each row
        ↓
many User objects
        ↓
List<User>
```

4. Why did we learn collections before ORM?

```text
1. This means a list of user objects
2. findAll() would return all 100 rows as a list of 100 user objects
4. This is because there are usually many rows in a database, with each row being translated to an object, many rows becomes collections of objects.
```

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
Repository = boundary between buisness logic and data storage
```

```text
1. It's job is to access tha data on a database to build entities
2. No it shouldn't. This would violate SOLID principles and cause the repository to do too much
3. No it shouldn't. This also violates SOLID principles and would lead to a bloated class with too many reasons to change
4. SRP
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


 ```text
1. Email validation, database access, message sending and report generation are all mixed together
2. A change in one of this processes would result in having to change the whole class
3. Email validation should belong to the validator
4. Database access should belong to the repository
5. Message sending should belong to the message sender
report generation should belong to the report service
```
## Refactor Plan

Fill in the responsibility table:

| Responsibility            | Better Class |
| ------------------------- | ------------ |
| validate email            | EmailValidator            |
| save user                 | UserRepository            |
| send welcome email        | MessageSender            |
| generate user report      | ReportServices            |
| coordinate the whole flow | UserRegistrationService            |

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

```text
1. This functions as the coordinator for the other responsibilities
2. No it doesn't directly know SQL
3. No
4.It depends on the validator, userRepository and MessageSender objects
5. Yes this is composition as it brings together capabilities from other classes to achieve a given goal
```

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
The persistence context connects database identity to the entity
```
```text
1. It manages the entity during the current session
2. It maintains consistency as if the same object is used again in the same context, the same managed object is returned
3. It means the version of the table in java is tracked. This includes changes, whether it needs to be saved or whether the entity is being requested again
```
---

# Part 9 — Entity Lifecycle 🔄

Entities can exist in different states.

Match each state to its meaning.

| State           | Meaning |
| --------------- | ------- |
| New / transient | B       |
| Managed         | A       |
| Detached        | D       |
| Removed         | C       |

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

```text
1. New/transient
2. Managed
3. ORM is about the objects state over time. Understanding this helps to make more efficient code
```

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
```text
1. The users name is changed in memory
2. It can detect that a managed entity has changed
3. An SQL UPDATE can happen when the transaction commits
4. It automatically updates the database with information edited in Java
5. It changes the database after the transaction commits so sometimes changing an object changes the database way later
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
Many order objects
        ↓
List<Order>
```

```text
1. This represents a list of order objects
2. This represents a foreign key within the orders table
3.This is a One-to-Many relationship
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
ORM can make database access look like object access
```
```text
1. The user object
2. The orders associated with the user
3. It only loads related data when it's needed
4. This occurs when you access the required data further down the line and it makes the query then
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

```text
1. All the users
2. It would call it 100 times as there are 100 users so 1 for each user
3. 1+100
4. Because it appears simple but loads over 100 times
5. 
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
