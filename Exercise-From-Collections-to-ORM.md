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
   User class - > user object creating the user objects with an id email and name
2. What does the `users` table represent in the database?
   stores the user data in the database
3. What does one row in the `users` table correspond to in Java?
   to one user object 
4. What does one column in the table correspond to in Java?
   one field 
6. Complete this mapping:

| Database    | Java |
| ----------- | ---- |
| table       | class    |
| row         | object   |
| column      | field    |
| primary key | id field |

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
   @Entity 
2. Which annotation marks the primary key field?
   @Id
3. Why does JPA usually need a no-argument constructor?
   It creates the objects instanrlt when getting it from the rows from the database
4. In your own words, what is an entity?
  

Use this sentence starter:

```text
An entity is a Java object that is mapped to the database table so it can be stored and gathered from back from the database table
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
   In the memory
2. Where does the row live long-term?
   Database
3. What does ORM help translate between?
   Between the objects within the memory and the rows within the databse table

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
   List is a collection of user object
2. If the `users` table has 100 rows, what might `findAll()` return?
   100 of the users
3. Complete the flow:

```text
user table
        ↓
many rows
        ↓
ORM maps each row
        ↓
user objects
        ↓
List<User>
```

4. Why did we learn collections before ORM?
   Because we needed to understand how java stores many things in memory, thereofre ORM returns many databse rows, but a it needs a collection like List to hold them.
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
   Handles the data access of entities where you can update them
2. Should a repository send welcome emails? Why or why not?
   No it doesnt need data access to send a welcome email 
3. Should a repository calculate business discounts? Why or why not?
   Again no because it doesnt handle the data or persistence logic
4. Which SOLID principle helps explain this?
   Single  Responsibilites Principle as each of the class should have one specific job
6. Complete this sentence:

```text
Repository = boundary between business logic and data persistence 
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
   Email validation, SQL, database, saving users, sending the messages and generating reports
2. Why could this become difficult to maintain?
   One class is doing too many roles, if we wanted to update it, it may ruin or need to change the whole thing 
3.  Which part should belong to a validator?
   Email validator 
4.  Which part should belong to a repository?
   build sql queries
   connect to database
    insert user
5. Which part should belong to a message sender?
   send welcome email
6. Which part should belong to a report service?
   generate report

## Refactor Plan

Fill in the responsibility table:

| Responsibility            | Better Class |
| ------------------------- | ------------ |
| validate email            | EmailValidator           |
| save user                 | UserRepository           |
| send welcome email        | MessageSender     |
| generate user report      | UserReportService           |
| coordinate the whole flow | UserRegistrationService           |

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
            validator.validate(email);

        // 2. create User object
            User user = new User (email,name);

        // 3. save User through repository
            userRepository.save (user);

        // 4. send welcome message
            messageSender.send(email, "Welcome " + name);
    }
}
```

## Questions

1. What is the role of `UserRegistrationService`?
   To start and control the registration process for the users calling the classes in order
2. Does it directly know SQL?
   No as the repository deals with the data access
3. Does it directly know how messages are sent?
   No as the MessageSender deals with that
4. Which objects does it depend on?
   EmailValidator, UserRepository, MessageSender
5.  Is this composition? Explain.
   Yes because the UserRegistrationService has other objects and uses those to comeplete the full regstriation process. 

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
   Entity objects within the memory
2. Why is it useful for JPA to track entities in memory?
   To track if anything has changed
3. What does it mean for an entity to be “managed”?
   Managing the object 
4. Complete this sentence:

```text
The persistence context connects database identity to the object identity in memory 
```

---

# Part 9 — Entity Lifecycle 🔄

Entities can exist in different states.

Match each state to its meaning.

| State           | Meaning |
| --------------- | ------- |
| New / transient |B. Object exists in memory but has not been saved yet|
| Managed         |A. JPA is tracking the entity|
| Detached        |D. Object exists but JPA is no longer tracking it |
| Removed         |C. Entity is marked for deletion|

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
New / transient

2. What might happen after this?

```java
userRepository.save(user);
```
Managed

3. Why does entity lifecycle matter?
It when or if JPA is doing something tracking, new, managed, detached or removed an object
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
   Users name
2. What can JPA detect?
   Managed entites that has been changed 
3. What SQL might eventually happen?
   UPDATE the user information
4. Why can dirty checking be powerful?
   It can change the object and that would allo wthe JPA to update the database automatically
5. Why can it also be confusing?
   SQL might not update automatically

Complete the flow:

```text
Managed entity changes in memory
        ↓
JPA detects fields
        ↓
SQL update happens
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
   Order objects linking to the user
2. What does `orders.user_id` represent in the database?
   foreign key linking each order row to user row
3. What kind of relationship is this?
   1-many relationship
4. Complete the mapping:

```text
One User object
        ↓
Many order objects
        ↓
List<Order>
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
   User object
2. What might be loaded later?
   Orders when getOrders is called
3. Why can lazy loading improve performance?
   Avoids loading extra data that isnt need, only calls when needed
4. Why can lazy loading cause surprise queries?
   Can call a database query
5. Complete this sentence:

```text
ORM can make database access look like working with normal java objects, whiule the database queries behind the scene
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
   all 100 users
2. What might happen when `user.getOrders()` is called for each user?
   calls for each users 
3. How many total queries could happen in the bad case?
   would be 101 queries 
4. Why is this expensive?
   can make the system slow because it calls out so many queries to the database
5. Explain the N+1 problem in your own words.

Use this sentence starter:

```text
The N+1 problem happens when one query may load a list of objects but then the extar queries happen one by one when data is being accessed
```

---

# Part 14 — ORM and SOLID 🧱

Fill in the table.

| SOLID Principle | ORM Meaning |
| --------------- | ----------- |
| SRP             | repositories should not contain business logic   |
| OCP             | new persistence behavior should not break core logic   |
| LSP             | entity models should tell the truth|
| ISP             | repository interfaces should stay focused |
| DIP             | services should depend on repository abstractions|

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
   object relational mapping
2. Why do Java applications need ORM or something like it?
   As java works with objects but the databsases stores the data collection in tables so helps translate between the two
3. What is the relationship between a table and a class?
A table stores many records of one type and a class describes the type in java
4. What is the relationship between a row and an object?
    a row is with an objecy because one row in the table can become one java object in memory
5. What is the relationship between many rows and a collection?
   many database rows can become many Java onjects stored in the List
6. What does a repository do?
   Handles the database acess where you can save and update
7. What is the persistence context?
   managed in the memory area where tracks entities 
8. Why is lazy loading both useful and dangerous?
   Useful -> avoidings using data you do not need,
   Dangerous -> can create hidden queries and cause performance problems
9. Why does ORM not remove the need to understand databases?
    The queries and everything else still matter 
10. Explain this sentence:

```text
ORM is a system for synchronizing object state with database state over time.
```
The ORM keeps java objects and database rows same overtime 
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
   Customer, Order, OrderItem, Product
2. Decide which classes are repositories.
   CustomerRepository, OrderRepository
3. Decide which class coordinates the checkout flow.
   CheckoutService
4. Identify one one-to-many relationship.
   One customer can have many Orders
5. Identify one many-to-one relationship.
   Many Orders can belong to one customer 
6. Show where a `List<OrderItem>` might appear.
   May show in Order class 
7. Explain where lazy loading could happen.
   When an Order is loaded first but the OrderItem list only loaded when it is needed at the .getItems () is used 
8. Explain where the N+1 problem might appear.
When you load many orders and then it loops through each order and then calls order.getItems (), it would cause one query for the orders then many extra queries for the items
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
