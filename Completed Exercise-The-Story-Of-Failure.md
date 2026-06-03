# 🧠 Exercise: How Do We Prove Code Works?

## Goal

Understand this chain:

```text
Code has behaviour
→ behaviour can break
→ tests describe expected behaviour
→ failure paths must be checked
→ dependencies must be controlled
→ test suites protect change over time
```

---

# Part 1 — Behaviour First

Look at this method conceptually:

```text
add two numbers
```

Questions:

1. What behaviour does this promise?          1. It promises to take two numbers and add them together.
2. What input would you give it?              2. Two numbers to add.
3. What output would prove it worked?         3. If it adds two numbers and gives you the correct answer.
4. What output would prove it failed?         4. If it didn't add two numbers or give you the correct result
5. Complete:

```text
A test is a way of proving that...  method or code runs and does what is should or is required to do. 
```

---

# Part 2 — Expected Input / Expected Output

For each behaviour, fill in the expected result.

| Behaviour        | Input               | Expected Output |
| ---------------- | ------------------- | --------------- |
| Add numbers      | 2 and 3             | ? 5               |
| Multiply numbers | 4 and 5             | ?  20             |
| Validate email   | `amina@example.com` | ?  valid email           |
| Validate email   | `amina`             | ? error invalid email              |
| Apply discount   | price = 100         | ? price = less than 100              |
| Apply discount   | price = 50          | ? price is less than 50              |

Core idea:

```text
Testing begins by making expectations explicit.
```

---

# Part 3 — Happy Path vs Failure Path

A registration flow:

```text
receive email and name
validate email
create user
save user
send welcome message
return success
```

Questions:

1. What is the happy path? Where everything works the first time. (return sucess.)
2. What could go wrong? Every step can go wrong. Email invalid, user already exists, error saying users loss of data, no welcome message.
3. What should happen if the email is invalid?  return error and do not create/save the user. 
4. What should happen if the user already exists? User exists, stop process to choose another username.
5. What should happen if saving fails? return error message, do not send welcome message.
6. What should happen if sending the welcome message fails? Return error, stop process do not return success message.

---

# Part 4 — Arrange / Act / Assert, Conceptually

Every test has three parts:

```text
Arrange = set up the situation
Act = perform the behaviour
Assert = check what should be true
```

Scenario:

```text
A user registers with a valid email.
```

Fill in:

```text
Arrange: allow user to register and enter their username.
Act: register user and save let them know they have registered succesfully.
Assert: check username is valid and it has been saved and verified.
```

Scenario:

```text
A user registers with a blank email.
```

Fill in:

```text
Arrange: User tries to enter email but it is blank.
Act: Tells user email is blank, add a valid email.
Assert: check if user has added a valid email, and has been notified that their email is blank. 
```

---

# Part 5 — What Are We Really Testing?

Given this service:

```text
UserRegistrationService
- validates email
- creates user
- saves user
- sends welcome message
```

Questions:

1. What behaviour belongs to the registration service?--- validate email, save user, send welcome message, create user after user inputs it.
2. What behaviour belongs to the validator?--- It verifies whether the email is valid or has already been used.
3. What behaviour belongs to the repository? --- To make sure the info is savede and stored correctly.
4. What behaviour belongs to the message sender? ---Sending the welcome message.
5. Why should we not test everything as one giant blob?--- It becomes harder to find bugs, understand failures, test individual responsibilities and maintain code.

Answer frame:

```text
We separate tests because we separate responsibilities.
```

---

# Part 6 — Dependencies

A service depends on:

```text
UserRepository
MessageSender
EmailValidator
```

Questions:

1. Which dependency touches storage?-------- User Repository.
2. Which dependency sends communication? -----------------Message Sender
3. Which dependency checks input?------------------- Email validator
4. Why might real storage make a test harder?--------- It may not always have the same data at all times, Can be slower as data has to be read from or written to storage, Could be connection issues with storage, 
5. Why might real email sending make a test dangerous? Could contain sensitive, confidential or private information.
6. What could we use instead of the real dependency? A mockup, to see everything, the code, tests, classes, objects before the real programme is built.

Core idea:

```text
To test one unit clearly, control its dependencies.
```

---

# Part 7 — Fake Dependencies

Imagine a fake repository.

It does not use a real database.

It only remembers what happened.

Questions:

1. Why is a fake repository useful? It can be used to test your code without using a real database or storage system. It is also faster and provides predicatable data without having to rely on a real database or storage system.
2. What behaviour can it help us observe? It lets us observe how the app will behave when data is stored or retrieved, without the need of a real database.
3. What does it protect us from? It can be used to make mistakes on, test, code, everything, protecting us from making them in the real app.
4. When would we need the real database instead? When you want to test that your actual application actually works with the database, to see if data is stored and retrieved correctly in a real environment.

Distinction:

```text
Fake dependency = controlled test double
Real dependency = integration with actual system
```

---

# Part 8 — Result vs Interaction

Some tests check returned results.

Example:

```text
2 + 3 returns 5
```

Some tests check interactions.

Example:

```text
When user registers,
repository should save the user,
message sender should send welcome message.
```

Questions:

1. Which example checks a result?
2. Which example checks an interaction?
3. Why do services often require interaction checks?
4. Why do pure calculation methods often require result checks?

---

# Part 9 — Failure as Behaviour

Failure is also behaviour.

Scenario:

```text
A user tries to register with an email that already exists.
```

Expected behaviour:

```text
System refuses registration.
User is not saved again.
Welcome message is not sent.
Clear error is returned.
```

Questions:

1. What should happen?
2. What should not happen?
3. Why is “not saving” part of the expected behaviour?
4. Why is “not sending message” part of the expected behaviour?

---

# Part 10 — Unit vs Integration

Fill in:

| Test Type        | Meaning | Example |
| ---------------- | ------- | ------- |
| Unit test        | ?       | ?       |
| Integration test | ?       | ?       |

Use these ideas:

```text
one class in isolation
multiple parts working together
controlled dependencies
real database/API/framework
fast feedback
higher realism
```

Simple compression:

```text
Unit test = behaviour in isolation.
Integration test = behaviour across boundaries.
```

---

# Part 11 — Testing and SOLID

Match the principle to testing value.

| SOLID Principle | Testing Benefit |
| --------------- | --------------- |
| SRP             | ?               |
| DIP             | ?               |
| ISP             | ?               |
| OCP             | ?               |
| LSP             | ?               |

Use:

```text
smaller classes are easier to test
interfaces allow fake dependencies
small contracts reduce test setup
new behaviour can be tested without breaking old behaviour
subtypes must preserve expected behaviour
```

---

# Final Reflection 🚀

Answer:

1. What is a test? A test is a procedure that allows you to check if your code, app, or program works before you run it., e.g Does this code produce the correct result.
2. What is behaviour?
3. Why do we test failure paths?
4. Why do dependencies make testing harder?
5. Why do interfaces make testing easier?
6. Why does good design make testing easier?
7. Complete:

```text
Testing is not just checking code.
Testing is...
```

---

# Final Compression 🧠

```text
Test = proof of expected behaviour
Happy path = expected success flow
Failure path = expected error flow
Arrange = setup
Act = behaviour
Assert = expectation
Dependency = outside collaborator
Fake = controlled replacement
Unit test = isolated proof
Integration test = boundary proof
Test suite = safety net over time
```
