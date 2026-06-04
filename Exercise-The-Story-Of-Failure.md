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
2. What input would you give it?
3. What output would prove it worked?
4. What output would prove it failed?
5. Complete:

```text
A test is a way of proving that...
```
## Answers
1. The method will add two numbers together
2. Input numbers: 1, 1
3. 2
4. 3
5. A test is a way of proving that a method or piece of software works as intended.
---

# Part 2 — Expected Input / Expected Output

For each behaviour, fill in the expected result.

| Behaviour        | Input               | Expected Output                                                                |
| ---------------- | ------------------- | ------------------------------------------------------------------------------ |
| Add numbers      | 2 and 3             | 5                                                                              |
| Multiply numbers | 4 and 5             | 20                                                                             |
| Validate email   | `amina@example.com` | Valid                                                                          |
| Validate email   | `amina`             | Not Valid                                                                      |
| Apply discount   | price = 100         | Discount applied to price e.g. if discount was 10% then the output would be 90 |
| Apply discount   | price = 50          | Discount applied to price e.g. if discount was 10% then the output would be 45 |

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
2. What could go wrong?
3. What should happen if the email is invalid?
4. What should happen if the user already exists?
5. What should happen if saving fails?
6. What should happen if sending the welcome message fails?


## Answers
1. Registration returns success.
2. Email is not valid, user cannot be created, user cannot be saved into the database or welcome message cannot be sent.
3. An exception happens (software fails) and alerts user about the email not being valid.
4. An exception happens and the software alerts the user that the user already exists in the system.
5. There is an exception alerting the user that the new user cannot be saved and the database transaction does a rollback to prevent changes.
6. There should be an exception which alerts the user that the welcome message could be sent.
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
Arrange: User starts the registration on the system. 
Act: User enters valid email and system checks validity.
Assert: System registers correctly with the email provided, confirmation message is sent. 
```

Scenario:

```text
A user registers with a blank email.
```

Fill in:

```text
Arrange: User starts the registration on the system. 
Act: User enters a blank email and system checks if email is valid. 
Assert: System returns correct error message for that situation. 
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
2. What behaviour belongs to the validator?
3. What behaviour belongs to the repository?
4. What behaviour belongs to the message sender?
5. Why should we not test everything as one giant blob?

Answer frame:

```text
We separate tests because we separate responsibilities.
```
## Answers
1. UserRegistrationService
2. validates email
3. saves user
4. sends welcome message
5. As there are many dependencies, testing as one big unit will not allow us to test the dependencies fully, which will mean we have holes in our tests and cannot be fully confident that testing is complete and correct. 
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
2. Which dependency sends communication?
3. Which dependency checks input?
4. Why might real storage make a test harder?
5. Why might real email sending make a test dangerous?
6. What could we use instead of the real dependency?

Core idea:

```text
To test one unit clearly, control its dependencies.
```
## Answers
1. UserRepository
2. MessageSender
3. EmailValidator
4. To test, we would need to interact with the storage system so mocking may need to be used to do this if access is not available.
5. Real email addresses are personal so shouldn't be used for this purpose.
6. Use mocking to create a fake dependency that we can use to test. 
---

# Part 7 — Fake Dependencies

Imagine a fake repository.

It does not use a real database.

It only remembers what happened.

Questions:

1. Why is a fake repository useful?
2. What behaviour can it help us observe?
3. What does it protect us from?
4. When would we need the real database instead?

Distinction:

```text
Fake dependency = controlled test double
Real dependency = integration with actual system
```
## Answers
1. It allows us to test functionality without having the use the real endpoint. This allows us to test without using paid tokens or other restrictions.
2. It can help us observe interaction behaviour because the repository will record what happens e.g. connection requests
3. It protects us from modifying or corrupting data in real systems while testing and also protects us from wasting API tokens.
4. We would need the real database during the final end to end test to ensure that the whole system works. However, we need to test that the new code can be 'trusted' and is fully correct before using the real database.
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


## Answers
1. 2+3 returns 5
2. When user registers, repository should save the user, message sender should send welcome message.
3. Interaction checks ensure that external services and APIs are working correctly and receiving or sending data.
4. Calculation methods require result checks because the output is a result and we need to check if the result obtained is what we expect. 
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


## Answer
1. The above expected behaviour is followed, so user isn't saved with no welcome message and an error is returned to the user.
2. The registration completes, with the user being saved and a welcome message being sent.
3. The user should not be saved because the user already exists so it is an entry which exists in the database.
4. This is expected behaviour because if the user already exists, then they do not require another welcome email as they have already received this on the original registration attempt. 
---

# Part 10 — Unit vs Integration

Fill in:

| Test Type        | Meaning                                               | Example                       |
| ---------------- | ----------------------------------------------------- | ------------------------------|
| Unit test        | one class in isolation, fast feedback                 | real database/API/framework   |
| Integration test | multiple parts working together, higher realism       | controlled dependencies       |

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

| SOLID Principle | Testing Benefit                                             |
| --------------- | ----------------------------------------------------------- |
| SRP             | smaller classes are easier to test                          |
| DIP             | small contracts reduce test setup                           |
| ISP             | interfaces allow fake dependencies                          |
| OCP             | new behaviour can be tested without breaking old behaviour  |
| LSP             | subtypes must preserve expected behaviour                   |

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
