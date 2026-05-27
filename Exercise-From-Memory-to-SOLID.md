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

1. Which parts of this code are likely stored on the **stack**? main () is the stack frame, variable is user method getEmail()
2. Which object is created on the **heap**? user object
3. What does the variable `user` refer to? user object in the heap 
4. When `main()` finishes, what can eventually happen to the `User` object? the user reference is not used from the stack as a new user is there now, if it isnt used again it will go to the garabage collection. 
5. In your own words, explain this line:

```text
An object is structured memory.
```
It groups all the relevant data toegther, the user object would store the relevant data for the customer such as the email and name, and when needed to retrieve the data, the method getemail () will go back to the user data information. 
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

1. What is this class responsible for? Responsible for registering users, validating emails, saving users data, sending a welcome email and generating user reports
2. How many different reasons could this class change? 4 reasons stated above, example if the email validation changes, everything alongside it would change
3. Which method relates to validation? validatingEmail ()
4. Which method relates to persistence/database work? saveUser ()
5. Which method relates to email? sendWelcomeEmail ()
6. Which method relates to reporting?  generateUserReport()
7. Why could this class become difficult to maintain as the system grows? It is completing too many tasks making it messy and entrophy. If one method changes the whole class may be affected, it could lead to errors, harder to maintain, and developers would be scared to change the code.

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

ANSWER:
```java
public class UserRegistrationService {

    public void registerUser(String email, String name) {
        EmailValidator validator = new EmailValidator();
        UserRepository repository = new UserRepository();
        WelcomeEmailSender welcomeEmailSender = new WelcomeEmailSender();
        UserReportService reportService = new UserReportService();

        validator.validateEmail(email);
        repository.saveUser(email, name);
        welcomeEmailSender.sendWelcomeEmail(email);
        reportService.generateUserReport(email);
    }
}

class EmailValidator {
    public void validateEmail(String email) {
        System.out.println("Validating email: " + email);
    }
}

class UserRepository {
    public void saveUser(String email, String name) {
        System.out.println("Saving user data for " + name + " (" + email + ")");
    }
}

class WelcomeEmailSender {
    public void sendWelcomeEmail(String email) {
        System.out.println("Sending Welcome Email to " + email);
    }
}

class UserReportService {
    public void generateUserReport(String email) {
        System.out.println("Generating user report for " + email);
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
ANSWER:

public interface MessageSender {
    void send(String to, String message);
}

public class EmailSender implements MessageSender {
    public void send(String to, String message) {
        System.out.println("Sending Email to " + to + ": " + message);
    }
}

public class SmsSender implements MessageSender {
    public void send(String to, String message) {
        System.out.println("Sending SMS to " + to + ": " + message);
    }
}
``
## Questions

1. What capability does `MessageSender` represent? the ability to actually send the message to the user
2. Why is `MessageSender` more flexible than depending directly on `EmailSender`? It doesnt directly depend on the emailSender method, the messageSender can send the message in any different ways like sms, email or whatsapp, the core reason is to send the message to the user whatever way needed, so the emailSender restricts that however messageSender does not.
3. Which SOLID principle does this help with? Helps with dependency inversion as the registration depends on the messageSenders ineterface rather than a fixed class like EmailSender. Another principle is Open Clsosed as we can add new classess if we wanted to in the furture without changing the registration interface.
4. How does this make the system easier to extend later? It makes the system easier to extend because if we want to add another message type in the future by creating a new class that implements MessageSender.

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
        messageSender.send (email,"welcome, " + name);
    }
}
```

## Questions

1. What changed in the design? The UserRegistrationService doesnt create the EmailSender, it gets from the MessageSender which is outside 
2. What concrete class did we remove from `UserRegistrationService`? EmailSender
3. What abstraction does it now depend on? MessageSender 
4. Which principle is this? Dependency Inversion Principle
5. Why is this better? The class would be easier to change in the future if you wantto amened, test or even extend the class. 

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

1. Why does this design feel logical at first? Because the penguin is a bird
2. Why does it become a problem in code? Not all birds are able to fly such as the penguin 
3. What promise does `Bird` appear to make? It proimises that the penguin is able to fly though it cant
4. How does `Penguin` break that promise? It cant fly so therefore it would throw an error
5. Which SOLID principle is involved here? Liskov Subsiitution Principle because the child class should behvae correctly to the parent class, in this case the penguin breaks this because bird promises flying but penguin can not fly

## Better Design

Complete this improved design:

```java
interface Bird {

}

interface FlyingBird {
    void fly();
}

class Eagle implements Bird, FlyingBird {
    public void fly (){
        System.out.println("Flying");
    }

}

class Penguin implements Bird {

}
```

## Final Question

Explain this sentence:

```text
Inheritance should preserve truth, this means the child class should be able to behave like the parent class, as the parent type here promises a specific behaviour the child class should do the same thing. 
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

1. Which objects does `UserRegistrationService` use? It uses EmailValidator, UserRepository, and MessageSender
2. Is this inheritance or composition? Composition 
3. Why is this better than putting all logic inside one class? It shows that each part has a clear job, the registration service is start of the process, while the other classess handles things like validating emails, saving data and sending message
4. What does composition allow us to do? To build bigger and grander things by adding small parts to one main interface making it easier for us to update code
5. Explain this sentence:

```text
Composition lets us build bigger systems from smaller, clearer pieces. That the complex structure becomes easier to manage when each of the object has a focused role and the bigger behvious connects all the object together within the interface. 
```

---

# Part 8 — Final Reflection 🚀

Answer these in your own words.

1. What is the relationship between memory and objects? Memory is where the data is stored in run time, object organises the memory into structures
2. What is the relationship between objects and dependencies? Object would depend on other object to complete the tasks
3. Why do dependencies make software harder to change? Changing one class can affect other classes within the interface, dependencies make the system not flexible for chnage leading to entrophy
4. What does SOLID help us control? Helps us control any changes as it keeps the responsibilties for each class clear and easier to maintain overall 
5. What does this sentence mean?

```text
Memory is where software lives.
Objects give memory shape.
SOLID keeps that shape coherent over time.
```
The programs run within the memory, the object organise the data within the memory, and the SOLID helps keep the object design clean as the system in the future would grow and change in requirements. 
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


public interface PaymentProcessor {
    void pay(double amount);
}

public class StripePaymentProcessor implements PaymentProcessor {

    public void pay(double amount) {
        System.out.println("Paying with Stripe: £" + amount);
    }
}

public class PaypalPaymentProcessor implements PaymentProcessor {

    public void pay(double amount) {
        System.out.println("Paying with PayPal: £" + amount);
    }
}

public class OrderRepository {

    public void saveOrder(String orderId) {
        System.out.println("Saving order: " + orderId);
    }
}

public class ReceiptSender {

    public void sendReceipt(String email) {
        System.out.println("Sending receipt to " + email);
    }
}

public class CheckoutService {

    private final PaymentProcessor paymentProcessor;
    private final OrderRepository orderRepository;
    private final ReceiptSender receiptSender;

    public CheckoutService(
        PaymentProcessor paymentProcessor,
        OrderRepository orderRepository,
        ReceiptSender receiptSender
    ) {
        this.paymentProcessor = paymentProcessor;
        this.orderRepository = orderRepository;
        this.receiptSender = receiptSender;
    }

    public void checkout(String orderId, String email, double amount) {
        paymentProcessor.pay(amount);
        orderRepository.saveOrder(orderId);
        receiptSender.sendReceipt(email);
    }
}
Then answer:

1. Which classes represent responsibilities? CheckoutService, OrderRepository, and ReceiptSender. StripePaymentProcessor and PaypalPaymentProcessor are responsible for payment methods
2. Which interface represents a capability? PaymentProcessor interface represents the capability to process a payment 
3. Where are you using composition? in CheckoutService because it uses PaymentProcessor, OrderRepository, and ReceiptSender
4. Where are you applying DIP? in the CheckoutService as it depends on the PaymentProcessor interface but not the others
5. How could you add `ApplePayPaymentProcessor` without changing `CheckoutService`? Create a new class that would implement PaymentProcessor
like this:
public class ApplePayPaymentProcessor implements PaymentProcessor {

    public void pay(double amount) {
        System.out.println("Paying with Apple Pay: £" + amount);
    }
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
