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
```text
1. The main method, Get email method and the user local variable
2. The User object is created on the heap
3. The variable user references the User object
4. The User object would get deleted as nothing else is using it
5. This is because an object along with its properties is given a location in memory. It's structured because it holds a set form which can be reference by other threads and stacks
``` 
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

```text
1. This class is reponsible for registering and saving new users. Essentially everything involved with registering a new user
2. It could change for 4 different reasons
3. validateEmail()
4. saveUser
5. They all relate to email in some way or form, depends on context
6. generateUserReport()
7. There are too many reasons for this class to change. A class should really only have one main reason to change. This is because as complexity grows, multiple reasons for change creates instability.
``` 

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
        EmailValidator validation = new EmailValidator();
        UserRepository save = new UserRepository();
        WelcomeEmailSender welcome = new WelcomeEmailSender();
        UserReportService report = new UserReportService();
        
        validation.validateEmail(email);
        save.saveUser(email, name);
        welcome.sendWelcomeEmail(email);
        report.generateUserReport(email);
}

public class EmailValidator {
    private void validateEmail(String email) {
        System.out.println("Validating email: " + email);
    }
}

public class UserRepository {
    private void saveUser(String email, String name) {
        System.out.println("Saving user to database");
    }
}

public class WelcomeEmailSender {
    private void sendWelcomeEmail(String email) {
        System.out.println("Sending welcome email");
    }
}

public class UserReportService {
    private void generateUserReport(String email) {
        System.out.println("Generating user report");
    }
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


```text
1. MessageSender represents the capability to send messages
2. This is because MessageSender has the core functionality to send messages regardless of the platform
3. This helps with OCP
4. If a new way to message beomes a route the business wants to go down, we can still make use of MessageSender to do that
```

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
        this.messageSender = messageSender;
    }

    public void register(String email, String name) {
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


```text
1. Now MessageSender is used to send the email as opposed to EmailSender. It relies on the capability as opposed to the concrete class
2. EmailSender
3. MessageSender
4. This is DIP
5. This will work for anything that needs the capability to send a message. Sending a message doesn't rely on something concrete like a platform but rather the abstract concept of sending a message. This avoid rigidity when using or changing the capability of sending a message
```

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

```text
1. This is because a penguin is a bird and so it makes sense that penguin extends bird
2. It becomes a problem when we come across a flightless bird
3. Bird promises that all birds can fly
4. Penguin is a bird that can't fly
5. LSP
```

## Better Design

Complete this improved design:

```java
interface Bird {

}

interface FlyingBird {
    void fly();
}

class Eagle implements Bird, FlyingBird {
    public void fly()
}

class Penguin implements Bird {

}
```

## Final Question

Explain this sentence:

```text
Inheritance should preserve truth.
```

```text
Inheritance should only be used when the child class truly behaves like the parent class withoiut partiality. Inherited classes should be behaviourilly consistent.
```

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

```text
1. EmailValidator, UserRepository, MessageSender
2. Composition
3. This would lead to too many reasons to change a class which violates the SRP principle
4. It allows us to coordinate small focused parts
5. Composition allows us to orchestrate smaller, focused pieces to work together to create a bigger system
```
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
```text
1. Memory is where data is stored to be worked on, Objects is this data structured and stored within memory
2. Objects is the structured memory and dependencies are what those objects depend on from other parts of memory
3. This is because a change in an object that is depended upon by others leads to a cascading effect where the objects that depend on it change as well which can be hard to keep track off with allot of dependencies
4. Solid helps to make these dependencies manageable and focused to avoid cascading changes
5. Memory is where the data used for the sofware is stored, Objects structure data in memory that can be referenced and SOLID allows said objects to retain their form through multiple changes
```


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


```java
public interface PaymentProcessor{
    void pay();
}

public class StripePaymentProcessor implements PaymentProcessor {
    public void pay() {
        System.out.println("Stripe Pay");
    }

public class PaypalPaymentProcessor implements PaymentProcessor {
    public void pay() {
        System.out.println("Paypal Pay");
    }

public class OrderRepository {
    private void saveOrder() {
        System.out.println("Saving order to database");
    }
}

public class ReportSender {
    private void sendReport() {
        System.out.println("Sending report");
    }
}

public class CheckoutService{
    private final PaymentProcessor processor;
    private final OrderRepository repo;
    private final ReportSender report;

    public CheckoutService(PaymentProcessor processor,OrderRepository repo, ReportSender report ){
        this.processor = processor;
        this.repo = repo;
        this.report = report;
    }

    public void checkout(){
        processor.pay();
        repo.saveOrder();
        report.sendReport();
    }
}

}
```

Then answer:

1. Which classes represent responsibilities?
2. Which interface represents a capability?
3. Where are you using composition?
4. Where are you applying DIP?
5. How could you add `ApplePayPaymentProcessor` without changing `CheckoutService`?

```text
1. StripePaymentProcessor, PaypalPaymentProcessor, OrderRepository, ReportSender, CheckoutService
2. PaymentProcessor
3. Within the CheckoutService class to make use of the other classes
4. When making PaymentProcessor the capability that the payment classes depend on
5. I would simply make a 'ApplePayPaymentProcessor' class that implements from PaymentProcessor
```

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
