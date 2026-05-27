# 🧠 Exercise: From Memory to SOLID ☕️⚙️

## Goal

By the end of this exercise, you should be able to explain how a Java program moves from:

```text
memory
→ objects
→ dependencies
→ design problems
→ SOLID solutions
```

This is not just about writing code.

It is about learning how software systems are shaped.

---

# Part 1 — Memory Foundation 🧱

Look at this Java code:

```java
public class Main {

    public static void main(String[] args) {
        User user = new User("amina@example.com", "Amina");
        System.out.println(user.getEmail());
    }
}

class User {
    private String email;
    private String name;

    public User(String email, String name) {
        this.email = email;
        this.name = name;
    }

    public String getEmail() {
        return email;
    }
}
```

## Questions

1. Which parts of this code are likely stored on the **stack**?
2. Which object is created on the **heap**?
3. What does the variable `user` refer to?
4. When `main()` finishes, what can eventually happen to the `User` object?
5. In your own words, explain this line:

```text
An object is structured memory.
```

## Answers Q1
1. Stored on the stack: main, user constructor, getEmail(), email and name
2. Stored on the heap: User object
3. The variable user is storing the pointer in memory heap for the User object, the instance of the user object. 
4. When main finishes, the garbage collector removes the User object and deallocates the memory for this as it is no longer reachable.
5. An object is an allocated piece of memory for certain parts of code representing one responsiblity. Therefore, for each object that is created, memory is allocated for each of these on the heap, structuring it. 

---

# Part 2 — Objects and Responsibilities 🧩

Now look at this class:

```java
public class UserService {

    public void registerUser(String email, String name) {
        validateEmail(email);
        saveUser(email, name);
        sendWelcomeEmail(email);
        generateUserReport(email);
    }

    private void validateEmail(String email) {
        System.out.println("Validating email: " + email);
    }

    private void saveUser(String email, String name) {
        System.out.println("Saving user to database");
    }

    private void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email");
    }

    private void generateUserReport(String email) {
        System.out.println("Generating user report");
    }
}
```

## Questions

1. What is this class responsible for?
2. How many different reasons could this class change?
3. Which method relates to validation?
4. Which method relates to persistence/database work?
5. Which method relates to email?
6. Which method relates to reporting?
7. Why could this class become difficult to maintain as the system grows?


## Answers Part 2
1. This class is used for managing users, primarily when adding new users to this service.
2. This class could change for four different reasons as it has four methods which are designed to do separate tasks. 
3. validateEmail()
4. saveUser()
5. sendWelcomeEmail()
6. generateUserReport()
7. This class may become difficult to maintain if more methods are added for more functionality for this class. 

---

# Part 3 — Apply SRP 🎯

The Single Responsibility Principle says:

```text
A class should have one main reason to change.
```

Refactor the responsibilities from `UserService` into smaller classes.

You do not need to write full method bodies yet.

Just create the class names and method names.

## Starter Structure

```java
public class UserRegistrationService {
    public void UserRegistrationService(){}
}

public class EmailValidator {
    public void validateEmail(){}
}

public class UserRepository {
    public void saveUser(){}
}

public class WelcomeEmailSender {
    public void sendWelcomeEmail(){}
}

public class UserReportService {
    public void generateUserReport(){}
}
```

## Your Task

Fill in the missing methods for each class.

Think carefully:

```text
What should each class own?
```

---

# Part 4 — Interfaces as Capability Contracts 🔌

Now imagine your system sends welcome messages.

At first, it only sends email.

Later, the business says:

```text
We also want SMS messages.
```

Then later:

```text
We also want WhatsApp messages.
```

If your registration system directly depends on one concrete email class, it becomes rigid.

So we introduce an interface.

## Create this interface

```java
public interface MessageSender {
    void send(String to, String message);
}
```

## Then create two implementations

```java
public class EmailSender implements MessageSender {

}
```

```java
public class SmsSender implements MessageSender {

}
```

## Questions

1. What capability does `MessageSender` represent?
2. Why is `MessageSender` more flexible than depending directly on `EmailSender`?
3. Which SOLID principle does this help with?
4. How does this make the system easier to extend later?

## Answers
1. As MessageSender is an interface, it holds a contract of what it is able to do. This means that for every type of message, an implementation of MessageSender can be used.
2. As Message sender has no real implementation, this means that it is up to the implementation to add the functionality. This means that future messsaging services can be used. EmailSender is not flexible and only allows emails to be sent.
3. The solid principel that applies is interface segregation.
4. More messaging services can easily be added by implementing the MessageSender interface. 

---

# Part 5 — Remove the Hardcoded Dependency ⚠️

Here is a rigid design:

```java
public class UserRegistrationService {

    private EmailSender emailSender = new EmailSender();

    public void register(String email, String name) {
        System.out.println("Registering user");
        emailSender.send(email, "Welcome, " + name);
    }
}
```

## Problem

`UserRegistrationService` is directly locked to `EmailSender`.

If we want to use SMS, WhatsApp, or a fake sender for testing, we must change this class.

## Your Task

Refactor it so that `UserRegistrationService` depends on the interface instead:

```java
public class UserRegistrationService {

    private final MessageSender messageSender;

    public UserRegistrationService(MessageSender messageSender) {
        // your code here

        this.messageSender = messageSender;
    }

    public void register(String email, String name) {
        // your code here

        System.out.println("Registering user");
        messageSender.send(email, "Welcome, " + name);
    }
}
```

## Questions

1. What changed in the design?
2. What concrete class did we remove from `UserRegistrationService`?
3. What abstraction does it now depend on?
4. Which principle is this?
5. Why is this better?

## Answers
1. The registering user is no longer locked to using email to register users.
2. EmailSender was removed.
3. messageSender is the abstraction.
4. Dependency Inversion Principle
5. We are no longer depending on a concrete class which was EmailSender, we are now using an abstraction which allows any type of message to be sent which is more flexible.
---

# Part 6 — Inheritance Truth Check 🧬

Look at this code:

```java
class Bird {
    public void fly() {
        System.out.println("Flying");
    }
}

class Penguin extends Bird {
    public void fly() {
        throw new UnsupportedOperationException("Penguins cannot fly");
    }
}
```

## Questions

1. Why does this design feel logical at first?
2. Why does it become a problem in code?
3. What promise does `Bird` appear to make?
4. How does `Penguin` break that promise?
5. Which SOLID principle is involved here?

# Answers
1. This feels logical because a Penguin is a bird so the inhertance makes sense to inherit from the bird class.
2. It is a problem because Penguins can't fly so throws an exception if used.
3. The promise that the Bird class makes is that every class which is inherited from this has to use the fly() method.
4. The Penguin class breaks this promise because Penguins are not able to fly and are unable to fullfill the function of the method.
5. Liskov Substitution Principle

## Better Design

Complete this improved design:

```java
interface Bird {

}

interface FlyingBird {
    void fly();
}

class Eagle implements Bird, FlyingBird {
    public void fly() {
        System.out.println("Flying");
    }
}

class Penguin implements Bird {

}
```

## Final Question

Explain this sentence:

```text
Inheritance should preserve truth.
```

If a class is inheriting a method from the superclass, it should be able to implement it correctly without errors and 'make sense' to implement it for this subclass.

---

# Part 7 — Composition: Building With Parts 🧩

Instead of making one class do everything, we can build a class from smaller parts.

Example:

```java
public class UserRegistrationService {

    private final EmailValidator validator;
    private final UserRepository repository;
    private final MessageSender messageSender;

    public UserRegistrationService(
        EmailValidator validator,
        UserRepository repository,
        MessageSender messageSender
    ) {
        this.validator = validator;
        this.repository = repository;
        this.messageSender = messageSender;
    }
}
```

## Questions

1. Which objects does `UserRegistrationService` use?
2. Is this inheritance or composition?
3. Why is this better than putting all logic inside one class?
4. What does composition allow us to do?
5. Explain this sentence:

```text
Composition lets us build bigger systems from smaller, clearer pieces.
```
## Answers
1. EmailValidator, UserRepository, MessageSender
2. Composition
3. It stops the class becoming overwhelmed with all the requried implementation, which makes it easier to maintain and read.
4. Composition allows for more readable and maintainable code by creating a management area of what things should happen where.
5. Composition allows us to build more complex code bases for systems which lots of functionality yet still be able to maintain the code and understand what each part it doing. This is because responsibility is split up between the classes. 
---

# Part 8 — Final Reflection 🚀

Answer these in your own words.

1. What is the relationship between memory and objects?
2. What is the relationship between objects and dependencies?
3. Why do dependencies make software harder to change?
4. What does SOLID help us control?
5. What does this sentence mean?

```text
Memory is where software lives.
Objects give memory shape.
SOLID keeps that shape coherent over time.
```
## Answers
1. The relationship between memory and objects is that objects are able to organise the memory into sections for particular responsibility.
2. When objects are created, they can depend on each other as some objects need others to do a particular task.
3. Dependencies make software harder to change because when there are many objects depending on each other, it can create dependency graphs. This means that if one changes, this can have a rolling impact on other parts of the code which is dependent on the changed code.
4. SOLID helps us control objects to create code which is better written and more maintainable, especially in larger systems.
5. This means that software is stored in the memory and then objects are able to partition and create order to this software in memory as each object has a particular responsibility. SOLID is able to manage the way the objects are organised over the design over the lifetime of the software. 
---

# Stretch Challenge 🌟

Design a simple checkout system using the ideas from this lesson.

Your system should include:

* `CheckoutService`
* `PaymentProcessor` interface
* `StripePaymentProcessor`
* `PaypalPaymentProcessor`
* `OrderRepository`
* `ReceiptSender`

Then answer:

1. Which classes represent responsibilities?
2. Which interface represents a capability?
3. Where are you using composition?
4. Where are you applying DIP?
5. How could you add `ApplePayPaymentProcessor` without changing `CheckoutService`?

---

# Final Compression 🧠

```text
Stack = method execution
Heap = object storage
Objects = structured memory
Classes = responsibility boundaries
Interfaces = capability contracts
Composition = controlled assembly
SOLID = survivable change
```

Use this as your map.
