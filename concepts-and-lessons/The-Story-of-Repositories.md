# 🗄️ The Story of Repositories — How Java Remembers Through Storage ☕️🧠

The Fridge2Meal backend now has a database.

The tables exist.

Flyway has created the schema.

The `User` entity gives Java a shape that matches the `users` table.

But the application still needs a way to ask the database questions.

```text
Can you save this user?

Does this email already exist?

Can you find this user by ID?

Can you return all users?
```

That is where repositories enter.

A repository is the storage doorway of the application.

It is where Java stops only holding objects in memory and starts reaching into long-term memory.

---

# 🧠 1️⃣ Where We Are in the Story

So far, we have built this chain:

```text
Memory = where Java works
Objects = structured memory
Collections = many objects organized together
ORM = objects connected to database rows
I/O = data entering and leaving the system
Controllers = HTTP boundary
Services = business use-case coordinators
Repositories = storage boundary
```

The setup task gave the project its foundation:

```text
PostgreSQL database
        ↓
Flyway schema
        ↓
JPA entities
        ↓
Repository interfaces
```

Now we need to understand what that means.

The database stores long-term data.

The entity represents that data as a Java object.

The repository is how Java asks the database questions.

```text
Database table
        ↕
Entity
        ↕
Repository
        ↕
Service
```

---

# 🧱 2️⃣ Entity vs Repository

An entity is a Java class mapped to a database table.

```java
@Entity
@Table(name = "users")
public class User {
    // fields mapped to columns
}
```

This means:

```text
User class ↔ users table
```

A repository is the interface used to access that table.

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

This means:

```text
UserRepository = database access boundary for User records
```

Simple distinction:

```text
Entity = stored thing
Repository = way to access stored things
```

---

# 🧬 3️⃣ JpaRepository: Prebuilt Persistence Capability

When you write:

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

You are telling Spring Data JPA:

```text
Create a repository for User entities,
where the ID type is Long.
```

Break it down:

```text
JpaRepository<User, Long>
              ↓     ↓
          Entity   ID type
```

So:

```java
JpaRepository<User, Long>
```

means:

```text
This repository manages User objects,
and each User is identified by a Long id.
```

That one line gives you many methods automatically.

---

# 🧰 4️⃣ What You Get for Free

Spring Data JPA gives you common persistence operations.

```text
save()
findById()
findAll()
deleteById()
existsById()
count()
```

Examples:

```java
userRepository.save(user);
```

```java
Optional<User> user = userRepository.findById(1L);
```

```java
List<User> users = userRepository.findAll();
```

This is powerful.

But remember:

```text
Repository methods still represent database access.
```

They are not magic.

They are Java methods that eventually cause database work.

---

# 🔍 5️⃣ Repository Methods Are Questions to Storage

A repository method usually asks the database a question or tells it to change something.

```java
findById(1L)
```

means:

```text
Find the user whose id is 1.
```

```java
existsByEmail("amina@example.com")
```

means:

```text
Does a user with this email already exist?
```

```java
save(user)
```

means:

```text
Store this user state.
```

So a repository is like a controlled memory access layer.

Not short-term memory.

Long-term storage.

```text
Repository = how Java remembers through the database
```

---

# 🧠 6️⃣ Optional: Maybe Found, Maybe Not

Look at this method:

```java
Optional<User> findByEmail(String email);
```

Why `Optional<User>`?

Because the user may exist.

Or the user may not exist.

```text
Optional<User> = maybe there is a User, maybe there is not
```

This is better than pretending a result will always exist.

Storage may or may not contain the thing.

The code must handle both.

---

# 🧩 7️⃣ Custom Repository Methods

Spring Data JPA can create queries from method names.

```java
public interface UserRepository extends JpaRepository<User, Long> {

    boolean existsByEmail(String email);

    Optional<User> findByEmail(String email);
}
```

Spring understands the method names.

```text
existsByEmail
        ↓
check whether a User exists with this email
```

```text
findByEmail
        ↓
find a User with this email
```

This works because `email` is a field on the `User` entity.

The repository method name connects to the entity field.

---

# ⚠️ 8️⃣ Repository Is Not Business Logic

A common beginner mistake:

```text
Put all logic inside the repository.
```

Bad idea.

A repository should not decide:

* whether a user is allowed to register
* whether a meal is healthy
* whether a payment should proceed
* whether a preference should affect a recipe
* whether a business rule has passed

Those are business decisions.

They belong in services.

The repository should focus on persistence:

```text
save this
find this
check if this exists
delete this
```

Clean distinction:

```text
Repository = data access
Service = business decision
Controller = HTTP boundary
```

---

# 🎯 9️⃣ User Registration Example

When a user registers, we may need this logic:

```text
Receive registration request
        ↓
Check if email already exists
        ↓
If email exists, reject registration
        ↓
Create User
        ↓
Save User
        ↓
Return UserResponse
```

Which parts belong where?

| Step | Layer |
|---|---|
| receive JSON request | Controller |
| check email exists | Service uses Repository |
| decide duplicate email is not allowed | Service |
| save user | Repository |
| return response | Controller / DTO |

The repository does not own the whole use case.

It only answers storage questions.

---

# 🧱 1️⃣0️⃣ Repositories and SOLID

| Principle | Repository Meaning |
|---|---|
| SRP | repository owns data access, not business logic |
| OCP | new query methods can be added without rewriting services heavily |
| LSP | repository contracts should behave consistently |
| ISP | repositories should expose focused persistence operations |
| DIP | services depend on repository abstraction, not raw database details |

A service should not need to know SQL.

A controller should not need to know database access.

The repository hides persistence details behind a clean Java interface.

---

# 🔄 1️⃣1️⃣ Runtime Flow

When a service calls:

```java
userRepository.save(user);
```

the conceptual flow is:

```text
User object in memory
        ↓
UserRepository
        ↓
Spring Data JPA
        ↓
Hibernate / ORM
        ↓
SQL INSERT or UPDATE
        ↓
PostgreSQL users table
```

When a service calls:

```java
userRepository.findByEmail(email);
```

the conceptual flow is:

```text
Service asks repository
        ↓
Repository query method
        ↓
ORM creates/runs query
        ↓
Database returns row if found
        ↓
ORM maps row to User object
        ↓
Repository returns Optional<User>
```

This is the bridge:

```text
Object world ↔ database world
```

---

# 🧨 1️⃣2️⃣ Common Repository Errors

## Method field does not exist

```java
findByUsername(String username)
```

But `User` has no `username` field.

Spring cannot build the query.

## Entity does not match table

```java
@Column(name = "firstName")
```

But the database column is:

```text
first_name
```

Mismatch.

## Wrong ID type

```java
JpaRepository<User, Integer>
```

But `id` is `Long`.

Use:

```java
JpaRepository<User, Long>
```

## Business logic in repository

If your repository starts deciding business rules, stop.

Move that logic to a service.

---

# 🗺️ 1️⃣3️⃣ Fridge2Meal Repository Map

The Fridge2Meal setup includes tables like:

```text
users
fridges
recipes
meals
images
ingredients
recipe_ingredients
preferences
user_preferences
```

Each table should eventually have:

```text
Entity
        ↓
Repository
```

Example:

| Table | Entity | Repository |
|---|---|---|
| users | User | UserRepository |
| fridges | Fridge | FridgeRepository |
| recipes | Recipe | RecipeRepository |
| meals | Meal | MealRepository |
| ingredients | Ingredient | IngredientRepository |
| preferences | Preference | PreferenceRepository |

This gives Java controlled access to the persistent model.

---

# 🚀 Final Compression

```text
Database = long-term storage
Entity = Java object mapped to a table
Repository = persistence boundary
JpaRepository<Entity, ID> = prebuilt database access capability
save() = persist object state
findById() = retrieve by primary key
findAll() = retrieve many
existsByEmail() = ask storage a yes/no question
Optional<T> = maybe found, maybe not
Service = decides when repository should be used
```

---

# 🧠 Ultimate Compression

```text
Objects live in memory.

Databases remember after memory disappears.

Repositories are how Java asks the database to remember and retrieve.
```

A repository is not where the whole app thinks.

It is where the app reaches into long-term memory.
