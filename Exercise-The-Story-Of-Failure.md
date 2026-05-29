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

1. What behaviour does this promise?

It will output the total of 2 numbers.

2. What input would you give it?

2 integers

3. What output would prove it worked?

an integer which is the total of the 2 integers that are inputted.

4. What output would prove it failed?

an integer which isnt the total of the 2 inputs

5. Complete:

```text
A test is a way of proving that the code works as intended
```

---

# Part 2 — Expected Input / Expected Output

For each behaviour, fill in the expected result.

| Behaviour        | Input               | Expected Output |
| ---------------- | ------------------- | --------------- |
| Add numbers      | 2 and 3             | 5               |
| Multiply numbers | 4 and 5             | 20              |
| Validate email   | `amina@example.com` | True            |
| Validate email   | `amina`             | False           |
| Apply discount   | price = 100         | 50              |
| Apply discount   | price = 50          | 25              |

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

1. What is the happy path?

This is the path where all inputs are correct, and there are no failures or errors, so it flows from start to finish

2. What could go wrong?

Many things, the email could be invalid, the email and name inputs could be the wrong type, the database may not be connected, there could be a user with the name and email already in the database.

3. What should happen if the email is invalid?

The user should be told to input a valid email.

4. What should happen if the user already exists?

The user should be told that they already exist on the system

5. What should happen if saving fails?

All potential changes to the database should be rolled back, and the user should be told that it didnt save and they should try again

6. What should happen if sending the welcome message fails?

Error message should be produced, if the user tries again they will get the earlier error saying they already are in the database.


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
Arrange: start on the register page
Act: input name as "amina" and email as "amina@hotmail.co.uk"
Assert: user registers
```

Scenario:

```text
A user registers with a blank email.
```

Fill in:

```text
Arrange: start on register page
Act: input name as "amina" and email as "aminaAhotmail.co.uk"
Assert: registration fails and error message is presented
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

1. What behaviour belongs to the registration service?

validating emails, creating users, saving users and sending a welcome message

2. What behaviour belongs to the validator?

checking if the email is valid

3. What behaviour belongs to the repository?
4. What behaviour belongs to the message sender?
5. Why should we not test everything as one giant blob?

When testing we want to ensure that if a test fails, we can easily find the point of failure and change it. If you have on test for lots of logic, it will be difficult for you to pinpoint what caused the failure.

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

1. Which dependency touches storage?

UserRepository

2. Which dependency sends communication?

MessageSender

3. Which dependency checks input?

EmailValidator

4. Why might real storage make a test harder?

you want a test to be repeatable, so if your test adds information to the database, if you add it again you may have issues.

5. Why might real email sending make a test dangerous?
6. What could we use instead of the real dependency?

Mocks

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

1. Why is a fake repository useful?

We only want to test logic, however using real databases can cause failures that are outside of the logic we want to test.

2. What behaviour can it help us observe?

It allows us to observe logic and logic alone in our test

3. What does it protect us from?

Factors causing a test failure that isnt due to logic.

4. When would we need the real database instead?

In end to end testing and in integration tests potentially if we are testing the databases intergration with the code.

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

The first

2. Which example checks an interaction?

The second

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

the user shouldnt be registered, and the user should be told that the email is already in use.

2. What should not happen?

The user should not be registered

3. Why is “not saving” part of the expected behaviour?

Saving is incorrect in the flow of the system, and can cause bigger issues down the line.

4. Why is “not sending message” part of the expected behaviour?

Sending a message implying success would confuse the user


---

# Part 10 — Unit vs Integration

Fill in:

| Test Type        | Meaning | Example |
| ---------------- | ------- | ------- |
| Unit test        | Testing one method       | ?       |
| Integration test | testing multiple parts working together       | ?       |

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

1. What is a test?
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
