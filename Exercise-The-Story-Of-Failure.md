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

---

# Part 2 — Expected Input / Expected Output

For each behaviour, fill in the expected result.

| Behaviour        | Input               | Expected Output |
| ---------------- | ------------------- | --------------- |
| Add numbers      | 2 and 3             | ?               |
| Multiply numbers | 4 and 5             | ?               |
| Validate email   | `amina@example.com` | ?               |
| Validate email   | `amina`             | ?               |
| Apply discount   | price = 100         | ?               |
| Apply discount   | price = 50          | ?               |

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
Arrange:
Act:
Assert:
```

Scenario:

```text
A user registers with a blank email.
```

Fill in:

```text
Arrange:
Act:
Assert:
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
