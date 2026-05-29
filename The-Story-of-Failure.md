# ⚠️ The Story of Failure — Exceptions, Transactions, and Logging in Java ☕️🧱

So far, we have built the story like this:

```text
Memory = where software lives
Objects = structured memory
Collections = many objects organized together
ORM = objects connected to database rows
Repositories = persistence boundaries
Services = business flow coordinators
SOLID = rules for survivable change
```

Now we move to the next major question:

```text
What happens when something goes wrong?
```

Because real software does not run in perfect conditions.

Databases fail.

Files are missing.

Input is invalid.

APIs time out.

Users enter bad data.

Network calls break.

Transactions partially complete.

Objects are not found.

Permissions are denied.

So the next layer of Java is not about the happy path.

It is about failure.

And professional software is not software that never fails.

Professional software is software that fails clearly, safely, and observably.

---

# 🧠 1️⃣ Why Failure Matters

Beginner code often assumes everything works.

Example:

```java
User user = userRepository.findByEmail(email);
System.out.println(user.getName());
```

But what if:

* the user does not exist?
* the database is down?
* the email is invalid?
* the query times out?
* `user` is null?

Then the program may crash or behave unpredictably.

So Java gives us a structured way to represent failure:

```text
Exceptions.
```

An exception means:

```text
Something happened that interrupted the normal flow of the program.
```

---

# 🌊 2️⃣ Normal Flow vs Exceptional Flow

Normal flow:

```text
Receive input
        ↓
Validate input
        ↓
Create object
        ↓
Save object
        ↓
Return success
```

Exceptional flow:

```text
Receive input
        ↓
Validation fails
        ↓
Throw exception
        ↓
Handle error
        ↓
Return clear failure response
```

Or:

```text
Receive input
        ↓
Create object
        ↓
Database save fails
        ↓
Rollback transaction
        ↓
Log error
        ↓
Return failure response
```

Failure is not random noise.

Failure is another path through the system.

Good software designs that path.

---

# 🧨 3️⃣ What Is an Exception?

An exception is an object that represents a problem.

Example:

```java
throw new RuntimeException("User not found");
```

This creates an exception object and interrupts the normal execution flow.

The program does not continue as if everything is fine.

It jumps into error handling.

A simple example:

```java
public User findUser(String email) {
    User user = userRepository.findByEmail(email);

    if (user == null) {
        throw new RuntimeException("User not found");
    }

    return user;
}
```

The exception is saying:

```text
The normal path cannot continue safely.
```

---

# 🧱 4️⃣ try / catch

Java uses `try` and `catch` to handle exceptions.

Example:

```java
try {
    User user = userService.findUser("amina@example.com");
    System.out.println(user.getName());
} catch (RuntimeException ex) {
    System.out.println("Something went wrong: " + ex.getMessage());
}
```

Meaning:

```text
try = attempt risky operation
catch = handle failure if it happens
```

This prevents the program from crashing without explanation.

But catching everything everywhere is not good design.

Exception handling should happen at the right boundary.

---

# ⚖️ 5️⃣ Checked vs Unchecked Exceptions

Java has two broad categories of exceptions:

```text
Checked exceptions
Unchecked exceptions
```

## Checked Exceptions

Checked exceptions must be handled or declared.

Example:

```java
public void readFile() throws IOException {
    Files.readString(Path.of("data.txt"));
}
```

`IOException` is checked because file reading can fail for expected external reasons:

* file missing
* permission denied
* disk issue

Java forces you to acknowledge this risk.

## Unchecked Exceptions

Unchecked exceptions do not need to be declared.

Examples:

```text
NullPointerException
IllegalArgumentException
RuntimeException
IndexOutOfBoundsException
```

These often represent programming errors, invalid state, or business rule failures.

Example:

```java
if (email == null || email.isBlank()) {
    throw new IllegalArgumentException("Email is required");
}
```

---

# 🧠 6️⃣ The Better Mental Model

Do not memorize checked vs unchecked mechanically.

Think like this:

| Type      | Meaning                                                          |
| --------- | ---------------------------------------------------------------- |
| Checked   | external failure the caller must acknowledge                     |
| Unchecked | invalid state, invalid argument, programming or business failure |

Simple compression:

```text
Checked = Java forces handling
Unchecked = runtime failure path
```

---

# 🎯 7️⃣ Custom Exceptions

Generic exceptions are often too vague.

Bad:

```java
throw new RuntimeException("Error");
```

Better:

```java
throw new UserNotFoundException("User not found: " + email);
```

Custom exception:

```java
public class UserNotFoundException extends RuntimeException {

    public UserNotFoundException(String message) {
        super(message);
    }
}
```

Now the failure has a clear meaning.

This improves:

* readability
* debugging
* API responses
* logging
* error handling

A custom exception gives failure a name.

---

# 🧩 8️⃣ Exceptions and SOLID

Exceptions are not separate from design.

They connect directly to SOLID.

| SOLID Principle | Failure Design Meaning                                          |
| --------------- | --------------------------------------------------------------- |
| SRP             | each layer handles the failures it owns                         |
| OCP             | new error types can be added without rewriting everything       |
| LSP             | subclasses should not create surprising failure behavior        |
| ISP             | interfaces should not force irrelevant exception handling       |
| DIP             | high-level logic should not depend on low-level failure details |

Example:

A service should understand:

```text
User not found
Payment failed
Invalid order
```

But it should not be deeply tied to:

```text
specific SQL driver error codes
low-level database internals
filesystem implementation details
```

The lower layer can translate technical failure into meaningful application failure.

---

# 🗄️ 9️⃣ Exceptions in ORM

ORM introduces new failure paths.

For example:

* entity not found
* database connection fails
* constraint violation
* transaction fails
* lazy loading fails
* duplicate key error
* invalid mapping

Example:

```text
User tries to register
        ↓
Email already exists
        ↓
Database unique constraint fails
        ↓
Application should return clear error
```

Bad response:

```text
500 Internal Server Error
```

Better response:

```text
Email already exists
```

The application should translate technical failure into meaningful failure.

---

# 🔁 1️⃣0️⃣ Transactions — Keeping Data Changes Safe

A transaction is a unit of work that should succeed or fail as one whole operation.

Imagine a checkout flow:

```text
Create order
Reduce stock
Take payment
Send receipt
```

What if the stock is reduced, but payment fails?

Now the system is inconsistent.

Transactions exist to prevent partial database changes.

Core idea:

```text
All succeed
or all rollback
```

---

# 🧱 1️⃣1️⃣ Transaction Example

Imagine this flow:

```java
public void checkout(Order order) {
    orderRepository.save(order);
    inventoryService.reduceStock(order);
    paymentService.charge(order);
}
```

If payment fails after the order is saved and stock is reduced, we may need to undo the database changes.

In Spring, transaction control often looks like:

```java
@Transactional
public void checkout(Order order) {
    orderRepository.save(order);
    inventoryService.reduceStock(order);
    paymentService.charge(order);
}
```

Conceptually:

```text
Begin transaction
        ↓
Save order
        ↓
Reduce stock
        ↓
Charge payment
        ↓
If all succeeds → commit
If failure happens → rollback
```

Transaction = consistency boundary.

---

# 🧠 1️⃣2️⃣ Transactions and ORM

ORM works closely with transactions.

Inside a transaction:

* entities may be loaded
* entities become managed
* object changes can be tracked
* dirty checking can detect changes
* SQL may be flushed before commit
* changes commit or rollback together

Flow:

```text
Transaction starts
        ↓
JPA loads managed entity
        ↓
Object changes in memory
        ↓
JPA detects changes
        ↓
Transaction commits
        ↓
Database updates
```

If an exception happens:

```text
Exception thrown
        ↓
Transaction rolls back
        ↓
Database remains safe
```

This is why exceptions and transactions belong together.

Exceptions are often the signal that a transaction should not continue.

---

# ⚠️ 1️⃣3️⃣ Commit vs Rollback

## Commit

Commit means:

```text
Make the transaction's changes permanent.
```

## Rollback

Rollback means:

```text
Undo the transaction's changes.
```

A useful mental model:

```text
Transaction = temporary workspace for database change
Commit = accept changes
Rollback = cancel changes
```

This protects the database from half-finished operations.

---

# 🧾 1️⃣4️⃣ Logging — Making Failure Visible

Exceptions tell the program something went wrong.

Logging tells humans what happened.

A log is a record of application activity.

Examples:

```text
User registration started
User registration failed: email already exists
Payment provider timed out
Order checkout completed
Database connection failed
```

Without logs, production systems become invisible.

You cannot fix what you cannot see.

---

# 🔍 1️⃣5️⃣ System.out.println vs Logging

Beginners often use:

```java
System.out.println("Something happened");
```

This is okay for tiny experiments.

But real applications use logging frameworks.

Common Java logging tools:

```text
SLF4J
Logback
Log4j
```

Example:

```java
private static final Logger log = LoggerFactory.getLogger(UserService.class);

log.info("Registering user with email {}", email);
log.warn("User already exists: {}", email);
log.error("Failed to register user", ex);
```

Logging gives levels.

---

# 📊 1️⃣6️⃣ Logging Levels

| Level | Meaning                              |
| ----- | ------------------------------------ |
| TRACE | very detailed diagnostic information |
| DEBUG | useful while debugging               |
| INFO  | normal important application events  |
| WARN  | something unusual but not fatal      |
| ERROR | something failed and needs attention |

Simple version:

```text
INFO = what happened
WARN = something suspicious
ERROR = something broke
DEBUG = details for developers
```

---

# 🧠 1️⃣7️⃣ What Should We Log?

Good logs answer:

```text
What happened?
Where did it happen?
Why did it happen?
Which request/user/order was involved?
```

Good examples:

```text
Order checkout started for orderId=123
Payment failed for orderId=123 reason=insufficient_funds
User registration failed email=amina@example.com reason=email_exists
```

Bad examples:

```text
error
failed
something went wrong
here
```

Logs should carry useful context.

---

# 🔐 1️⃣8️⃣ What Should We NOT Log?

Never casually log sensitive data.

Avoid logging:

* passwords
* full card numbers
* secret keys
* tokens
* private credentials
* unnecessary personal data

Good logging balances visibility with safety.

Professional logging is not just about seeing more.

It is about seeing the right things.

---

# 🧭 1️⃣9️⃣ Exception Handling by Layer

Different layers should handle different responsibilities.

| Layer          | Responsibility                    |
| -------------- | --------------------------------- |
| Controller/API | convert failure into response     |
| Service        | enforce business rules            |
| Repository     | data access failures              |
| Database       | constraints and persistence rules |
| Logger         | record what happened              |

Example flow:

```text
Controller receives request
        ↓
Service validates business rule
        ↓
Repository saves entity
        ↓
Database rejects duplicate email
        ↓
Exception thrown
        ↓
Service/API translates to meaningful error
        ↓
Logger records context
```

This is how failure becomes structured.

---

# 🧱 2️⃣0️⃣ Good Failure Design

Good failure design means:

* invalid input is rejected clearly
* missing data is handled clearly
* database failures do not corrupt state
* transactions rollback when needed
* errors are logged with context
* users receive meaningful messages
* developers get enough detail to debug

Bad failure design means:

* silent failures
* vague errors
* corrupted data
* swallowed exceptions
* random crashes
* no logs
* too much sensitive logging

---

# 🚫 2️⃣1️⃣ Common Mistake: Swallowing Exceptions

Bad:

```java
try {
    userRepository.save(user);
} catch (Exception ex) {
    // do nothing
}
```

This is dangerous.

The program hides the failure.

The user may think the action succeeded.

The database may not have changed.

Developers may have no logs.

Do not swallow exceptions silently.

If failure matters, make it visible or handle it properly.

---

# ⚠️ 2️⃣2️⃣ Common Mistake: Catching Too Broadly

Bad:

```java
try {
    checkout(order);
} catch (Exception ex) {
    System.out.println("Error");
}
```

This catches everything but explains almost nothing.

Better:

```java
try {
    checkout(order);
} catch (PaymentFailedException ex) {
    log.warn("Payment failed for orderId={}", order.getId(), ex);
} catch (OrderValidationException ex) {
    log.warn("Invalid order: {}", ex.getMessage());
}
```

Specific failures are easier to understand and handle.

---

# 🔄 2️⃣3️⃣ The Full Runtime Story

Here is how this connects to everything we have learned:

```text
Request enters application
        ↓
Controller receives input
        ↓
Service coordinates business flow
        ↓
Objects are created in memory
        ↓
Repository saves entities through ORM
        ↓
Transaction protects database consistency
        ↓
Exception interrupts flow if something fails
        ↓
Rollback prevents partial change
        ↓
Logging records what happened
        ↓
Application returns clear response
```

That is professional backend behavior.

---

# 🚀 Final Compression

```text
Exception = object representing failure
try = attempt risky operation
catch = handle failure
checked exception = must be handled or declared
unchecked exception = runtime failure path
custom exception = named failure meaning
transaction = consistency boundary
commit = accept changes
rollback = cancel changes
logging = visibility into runtime behavior
```

---

# 🧠 Final Thought

A beginner writes code for the happy path.

A professional designs the failure path.

Because real systems survive not by avoiding failure entirely,

but by containing failure clearly, safely, and visibly.
