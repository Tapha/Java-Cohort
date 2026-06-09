# 🧑‍💻 Fridge2Meal Team Build Process — How We Will Work From Here

## From guided setup to real team development

Up to this point, most of the work has been about getting the foundation in place.

You have been setting up:

```text
Java
Spring Boot
PostgreSQL
Flyway
Entities
Repositories
Project structure
Frontend/backend local setup
```

That foundation matters.

But now the project needs to move into a different mode.

We are no longer just following setup instructions.

We are going to start working like a real engineering team.

Fridge2Meal is the capstone project.

That means the goal is not only to build features.

The goal is to learn how developers contribute to a shared codebase.

---

# 🎯 The Big Shift

The project is moving from:

```text
Everyone follows the same guide
```

to:

```text
Everyone contributes to the same product
```

That means you will now start working through a professional development loop:

```text
Roadmap item
        ↓
Individual implementation
        ↓
Pull request
        ↓
Peer review
        ↓
Group review
        ↓
Best version merged
        ↓
Useful ideas from other PRs integrated
        ↓
Shared codebase improves
```

This is how many real software teams work.

You will not only learn how to write Java code.

You will learn how to:

* read other people’s code
* explain your own code
* review pull requests
* compare implementation choices
* improve code through feedback
* merge work into a shared codebase
* protect the quality of the main branch

This is the point where the capstone becomes a team simulation.

---

# 🧠 Why We Are Doing This

In real development, code does not usually enter the main branch just because it was written.

It has to be reviewed.

It has to be understood.

It has to fit the architecture.

It has to avoid breaking existing work.

It has to be good enough to become part of the shared system.

This process teaches four important skills:

| Skill | What It Means |
|---|---|
| Implementation | Can you build the feature? |
| Communication | Can others understand your code? |
| Review | Can you evaluate another developer’s work? |
| Integration | Can the team choose and improve the strongest solution? |

The aim is not to create competition for its own sake.

The aim is to create engineering judgement.

---

# 🏗️ How Each Feature Will Work

For each roadmap item, we will follow this process.

---

## 1️⃣ A feature is assigned

A feature will be chosen from the Fridge2Meal roadmap.

Examples:

```text
User Registration API
Global Exception Handling
Request Validation
GET Users Endpoint
GET User By ID Endpoint
Fridge Creation API
Ingredient Creation API
Meal Suggestion API
```

The feature will have a clear expected output.

Example:

```text
Build POST /api/users
```

Expected result:

```text
A user can be created through the API.
The user is saved in PostgreSQL.
The response does not return the password.
Duplicate emails are handled.
```

---

## 2️⃣ Everyone builds individually

Each person will implement the feature on their own branch.

Example branch name:

```text
feature/user-registration-your-name
```

The purpose of individual building is important.

It allows every learner to experience the full problem.

You are not just watching someone else solve it.

You are building your own version.

This creates real understanding.

---

## 3️⃣ Everyone opens a pull request

When your implementation is ready, you open a pull request.

A pull request is a proposal.

It says:

```text
Here is my implementation.
Please review it before it enters the shared codebase.
```

A good PR should explain:

* what was built
* which files were changed
* how to test it
* anything you are unsure about
* any trade-offs you made

The PR is not just a code dump.

It is a communication artifact.

---

# 📥 Pull Request Template

Use this structure when opening a PR.

```markdown
## What I built

Briefly explain the feature.

## Files changed

List the main files you created or changed.

## How to test

Explain how someone can test your work.

Example:
- Start backend
- Send POST request to /api/users
- Check response
- Check users table in PostgreSQL

## Design choices

Explain any important decisions.

## Questions / concerns

Mention anything you are unsure about.
```

---

# 🔍 4️⃣ Peer Review Begins

After PRs are opened, other team members will review them.

A review is not about attacking someone’s work.

A review is about improving the shared codebase.

When reviewing, look for:

```text
Does it work?
Is it understandable?
Does it follow the architecture?
Does it fit the project?
Could it break anything?
Can it be improved?
```

Good review comments are specific and respectful.

Bad review comments are vague or personal.

---

# 🧾 PR Review Rubric

Use this rubric when reviewing a pull request.

## Functionality

```text
Does the feature work?
Does the endpoint behave as expected?
Does the database save/read correctly?
Can the feature be tested?
```

## Architecture

```text
Is the controller thin?
Is the service handling business logic?
Is the repository only handling persistence?
Are DTOs separate from entities?
Are exceptions used meaningfully?
```

## Code Quality

```text
Are names clear?
Is the code readable?
Is there unnecessary duplication?
Are responsibilities separated?
Is the implementation simple enough?
```

## API Quality

```text
Does the request shape make sense?
Does the response shape make sense?
Is sensitive data excluded?
Are status codes appropriate?
Are errors handled clearly?
```

## Project Fit

```text
Does it match the existing package structure?
Does it match the database schema?
Does it follow the conventions we are using?
Can the rest of the team build on it?
```

---

# 🗣️ 5️⃣ Group Review

After peer review, we will review the PRs together.

The goal is to compare implementation choices.

We may ask:

```text
Which implementation is clearest?

Which implementation fits the architecture best?

Which implementation is easiest to extend?

Which implementation handles errors best?

Which implementation should become the base?
```

This is where engineering judgement develops.

Sometimes two PRs both work.

But one may be easier to maintain.

Sometimes one PR has the best structure.

Another PR may have a better error message.

Another may have a better DTO design.

We can learn from all of them.

---

# 🧬 6️⃣ Merge the Strongest Base

We will usually choose one PR as the strongest base to merge.

This does not mean every other PR was useless.

A PR can be valuable even if it is not merged.

Useful ideas from other PRs can still be integrated.

For example:

```text
One PR has the cleanest service.
Another PR has better validation.
Another PR has clearer response DTOs.
Another PR has better naming.
```

We may merge one base PR, then bring in good ideas from others.

This mirrors real development.

The goal is not individual victory.

The goal is shared code quality.

---

# 🧠 Important Mindset

Do not think:

```text
My PR was not merged, so I failed.
```

Think:

```text
My implementation helped the team compare options.
My code created learning.
My ideas may still improve the final version.
```

In real teams, not every PR becomes the final form.

But every serious attempt can improve the final design.

The shared codebase is the product.

The learning is in the comparison.

---

# 🧱 What Makes a Strong PR?

A strong PR usually has:

* a working feature
* clear structure
* simple naming
* thin controller
* focused service
* clean repository use
* safe DTOs
* no leaked passwords or sensitive data
* meaningful errors
* easy testing steps
* minimal unnecessary complexity

A strong PR is not always the biggest PR.

Often, the strongest code is the clearest code.

```text
Clear code wins.
Working code matters.
Maintainable code lasts.
```

---

# 🚦 Example: User Registration API

For the User Registration API, everyone may build:

```text
POST /api/users
```

Required files may include:

```text
UserRequest
UserResponse
UserService
UserController
UserRepository custom methods
UserAlreadyExistsException
```

Expected flow:

```text
JSON request
        ↓
UserController
        ↓
UserRequest DTO
        ↓
UserService
        ↓
UserRepository
        ↓
users table
        ↓
UserResponse DTO
        ↓
JSON response
```

Review questions:

```text
Does POST /api/users work?
Does it save to PostgreSQL?
Does it prevent duplicate emails?
Does it avoid returning the password?
Is the controller thin?
Is the service responsible for the business rule?
Is the repository only handling database access?
```

---

# 🌿 Working With Branches

Each task should be built on a separate branch.

Example branch names:

```text
feature/user-registration-amina
feature/global-exception-handling-david
feature/user-validation-sara
```

General branch rules:

```text
main = stable shared code
feature branch = your individual work
pull request = proposal to merge your work
```

Do not commit directly to `main` unless instructed.

The main branch should stay clean.

---

# 🧪 Testing Your Work Before PR

Before opening a PR, check:

```text
Does the backend start?
Does the endpoint work?
Does the database update correctly?
Does the response look right?
Did I test the happy path?
Did I test at least one failure path?
Did I avoid returning sensitive data?
```

For API work, use:

```text
Postman
Bruno
Insomnia
curl
pgAdmin / DBeaver
```

A PR should not rely on hope.

It should be testable.

---

# 🧭 The Roadmap Loop

We will repeat this process across the roadmap.

```text
Teach concept
        ↓
Assign feature
        ↓
Build individually
        ↓
Open PR
        ↓
Peer review
        ↓
Group review
        ↓
Merge best base
        ↓
Integrate useful ideas
        ↓
Move to next feature
```

This loop will help us build Fridge2Meal step by step.

Each feature strengthens the codebase.

Each review strengthens your judgement.

Each merge turns individual learning into shared progress.

---

# 🗺️ Upcoming Feature Sequence

The likely sequence is:

```text
1. User Registration API
2. Global Exception Handling
3. Request Validation
4. GET Users
5. GET User by ID
6. Password Hashing
7. Fridge Creation API
8. Ingredient Creation API
9. Meal Suggestion API
10. Preference-Based Meal Suggestions
```

This may change as the project evolves.

But the pattern stays the same.

```text
Build.
Review.
Compare.
Merge.
Improve.
```

---

# 🧠 What You Are Really Learning

You are learning Java.

You are learning Spring Boot.

You are learning REST APIs.

You are learning databases.

But underneath all of that, you are learning something even more important:

```text
How to think and work like a software engineer inside a team.
```

That means:

* making decisions
* explaining trade-offs
* accepting feedback
* reviewing others fairly
* improving shared code
* protecting the main branch
* building features that fit a larger system

This is the professional layer.

---

# 🚀 Final Compression

```text
Feature = roadmap item
Branch = your workspace
PR = your proposal
Review = quality filter
Merge = shared decision
Main branch = team codebase
Best base = strongest implementation to build on
Unmerged PR = still a source of useful ideas
```

---

# 🌌 Ultimate Compression

```text
The capstone is no longer just an assignment.

It is a simulated engineering team.

The goal is not only to write code.

The goal is to learn how code earns its place in a shared system.
```
