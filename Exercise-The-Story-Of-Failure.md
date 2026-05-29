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
   Add two numbers together 
2. What input would you give it?
   two numbers for example 1 + 1
3. What output would prove it worked?
   if it is 2
4. What output would prove it failed?
   Anything other then 2 
5. Complete:

```text
A test is a way of proving that the behvaiour does as it is expected correctly 
```

---

# Part 2 — Expected Input / Expected Output

For each behaviour, fill in the expected result.

| Behaviour        | Input               | Expected Output |
| ---------------- | ------------------- | --------------- |
| Add numbers      | 2 and 3             | 5               |
| Multiply numbers | 4 and 5             | 20              |
| Validate email   | `amina@example.com` | valid           |
| Validate email   | `amina`             | invalid         |
| Apply discount   | price = 100         | Discount given               |
| Apply discount   | price = 50          | Discount given               |

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
   It works as expected so this case would be that the user registers successfully and gets a welcome message
2. What could go wrong?
   If the user gives an invalid email, or user already exists or saving fails or messages doesnt send
3. What should happen if the email is invalid?
   Returns an error and stops the registration process
4. What should happen if the user already exists?
   Should refuse registration and returns an error
5. What should happen if saving fails?
   Returns an error and rollback changes
6. What should happen if sending the welcome message fails?
   Returns an error and the logs that there was a failure 

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
Arrange: create user with valid email
Act: registers the user
Assert: user information is saved and proceeds successfully
```

Scenario:

```text
A user registers with a blank email.
```

Fill in:

```text
Arrange: creates a user with a blank email
Act: register user
Assert: error is returned and user is not saved and registeration fails 
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
   Process of regstriation  
2. What behaviour belongs to the validator?
   Checks of the email is valid
3. What behaviour belongs to the repository?
   Users data information
4. What behaviour belongs to the message sender?
   Sends message to user if the process is successfully 
5. Why should we not test everything as one giant blob?


Answer frame:

```text
We separate tests because we separate responsibilities. Each function has its own job so it should be tested separately because of something fails we would know the specific function that needs amending 
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
   It may break the system which is bad for the business
5. Why might real email sending make a test dangerous?
    It may send real emails accidently 
6. What could we use instead of the real dependency?
    Mocking dependency 

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
   It gives us control to test everything but not at the expense of the real system, for controlled environment 
2. What behaviour can it help us observe?
   If it saves function was called correctly 
3. What does it protect us from?
   Any database failures
4. When would we need the real database instead?
At intergration testing   
   

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
   2 + 3 returns 5

2. Which example checks an interaction?
   repository should save the user, and message sender should send welcome message
   
3. Why do services often require interaction checks?
   To see if the system works with other components correctly 
  
4. Why do pure calculation methods often require result checks?
   To check if the return value is correct 

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
   The user registration doesnt work and it throws an error 
2. What should not happen?
   The user information is not saved and no message is sent to theuser 
3. Why is “not saving” part of the expected behaviour?
   Bceause if there is a user already it wouldnt save therefore preventing duplicate user
4. Why is “not sending message” part of the expected behaviour?
   The process of user registration somehow failed 

---

# Part 10 — Unit vs Integration

Fill in:

| Test Type        | Meaning                              | Example |
| ---------------- | -------                                | ------- |
| Unit test        | one class in isolation
                    controlled dependencies                | Testing using fake repository     |

                    
| Integration test | multiple parts working together 
                      real database/API/framework            | System saves to a real databe    |

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
| SRP             | smaller classes are easier to test               |
| DIP             | interfaces allow fake dependencies               |
| ISP             | small contracts reduce test setup               |
| OCP             | new behaviour can be tested without breaking old behaviour               |
| LSP             | subtypes must preserve expected behaviour              |

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
Test = proof of expected behaviour

2. What is behaviour?
   What code is expected to do 
3. Why do we test failure paths?
   It is an expected behaviour 
4. Why do dependencies make testing harder?
   Has external APIS system which makes it harder to control 
5. Why do interfaces make testing easier?
    Using fake dependencies
6. Why does good design make testing easier?
    To know what the separate responsibilities are and easier to see what function is throwing the error 
7. Complete:

```text
Testing is not just checking code.
Testing is showing and proving the expected behaviour functions as it should 
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
