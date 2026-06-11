# 🧭 The Story of Agile — How Teams Control Change Through Tickets 🎟️⚙️

The Fridge2Meal project is now becoming real.

The backend has a structure.  
The database exists.  
Entities and repositories are being created.  
Controllers, services, DTOs, and APIs are starting to take shape.

Now a new problem appears:

```text
How do multiple people work on a changing system
without turning the project into chaos?
```

That is where Agile enters.

Agile is not just stand-ups.

Agile is not just Jira.

Agile is not just “move fast.”

The deeper story is:

```text
Agile is a system for controlling change
while a product is still being discovered.
```

And tickets are the small units of that change.

---

# 🌊 1️⃣ The Real Problem: Software Changes

Software is never finished in one clean moment.

Requirements change.

Users ask for new things.

Bugs appear.

Technical problems surface.

The business changes direction.

Developers discover hidden complexity.

The database model evolves.

The API shape changes.

The frontend needs different data.

So the question becomes:

```text
How do we keep moving
without losing control?
```

This is the same problem we saw in SOLID.

SOLID asks:

```text
How do we stop code changes from breaking everything?
```

Agile asks:

```text
How do we stop product/team changes from becoming chaos?
```

Same law.

Different layer.

---

# 🧠 2️⃣ The Deep Connection: Agile and SOLID

SOLID lives inside the code.

Agile lives around the work.

But both are trying to solve the same core problem:

```text
Change must be contained.
```

| Layer | Tool | Purpose |
|---|---|---|
| Code | SOLID | control change inside software structure |
| Team | Agile | control change inside delivery process |
| Product | Tickets | break change into understandable units |
| Project | Sprint | timebox focused delivery |
| Architecture | Boundaries | stop complexity spreading |

So when we say:

```text
A class should have one reason to change.
```

That is SRP.

When we say:

```text
A ticket should describe one clear piece of work.
```

That is Agile doing the same thing at the task level.

---

# 🧱 3️⃣ Agile Is Architecture for Work

Think of a software project as a living system.

If work is vague, oversized, hidden, or tangled, the team slows down.

Bad work structure creates:

* confusion 😵‍💫
* duplicated effort 🔁
* blocked developers 🚧
* unfinished tasks 🕳️
* weak accountability 🌫️
* broken features 🧨
* “I thought you meant…” moments 😬

Agile exists to give work a shape.

```text
Agile = how teams give change a manageable shape
```

Tickets are the objects of Agile.

Sprints are the containers.

Boards are the memory of the team.

Stand-ups are synchronization points.

Retrospectives are feedback loops.

---

# 🎟️ 4️⃣ What Is a Ticket?

A ticket is a small, trackable unit of work.

It should answer:

```text
What needs to change?
Why does it matter?
What should be true when it is done?
```

A good ticket gives a developer enough clarity to move.

Not infinite detail.

Not vague intention.

Enough shape.

Example:

```text
Create User Registration API
```

This sounds okay, but it is not enough by itself.

A better ticket explains:

* the user need
* the endpoint
* the request shape
* the response shape
* the acceptance criteria
* the affected layers
* the expected behaviour
* the failure cases

A ticket is not just a title.

A ticket is a small contract for change.

---

# 🧩 5️⃣ The Anatomy of a Good Ticket

A strong ticket usually has:

| Part | Purpose |
|---|---|
| Title | quick summary |
| Context | why this work exists |
| User story / goal | who needs what |
| Scope | what is included |
| Out of scope | what is not included |
| Technical notes | useful implementation guidance |
| Acceptance criteria | how we know it is done |
| Test notes | how to verify it |
| Dependencies | what it relies on |
| Links / references | related docs, designs, tickets |

The most important part is acceptance criteria.

Because acceptance criteria define the finish line.

```text
Without acceptance criteria,
“done” becomes subjective.
```

---

# 🧠 6️⃣ User Story Format

A common Agile format is:

```text
As a [type of user],
I want [some capability],
so that [some benefit].
```

Example:

```text
As a Fridge2Meal user,
I want to register an account,
so that I can save my fridge data and meal preferences.
```

This format helps because it connects work to purpose.

But user stories are not magic.

A bad story can still be vague.

Good stories need clear acceptance criteria.

---

# ✅ 7️⃣ Acceptance Criteria

Acceptance criteria define what must be true for the ticket to be complete.

Example ticket:

```text
Create User Registration API
```

Good acceptance criteria:

```text
Given a valid registration request,
when the client sends POST /api/users,
then a new user is saved in the database.

Given the user is created successfully,
when the response is returned,
then the API returns 201 Created.

Given the response is returned,
then it includes id, firstName, lastName, and email.

Given the response is returned,
then it does not include password.

Given the email already exists,
when the client sends the same email again,
then the API returns a clear duplicate email error.
```

Acceptance criteria are the behaviour contract.

They connect directly to testing.

```text
Ticket acceptance criteria
        ↓
Expected behaviour
        ↓
Tests / manual checks
        ↓
Definition of done
```

---

# 🧪 8️⃣ Good Tickets Are Testable

A good ticket can be tested.

If nobody can verify it, the ticket is weak.

Bad:

```text
Improve user registration.
```

Why bad?

Improve how?

For whom?

What counts as success?

Better:

```text
Create POST /api/users endpoint that saves a new user and returns 201 Created without exposing password.
```

Now we can test it.

```text
Send request
Check status code
Check response body
Check database row
Check password is not returned
Check duplicate email behaviour
```

Good tickets create good tests.

Bad tickets create arguments.

---

# ⚠️ 9️⃣ The Signs of a Bad Ticket

A bad ticket often has one or more of these problems:

## Too vague

```text
Fix backend.
```

No clear action.

## Too large

```text
Build authentication, user profiles, fridge upload, image recognition, and meal suggestions.
```

Too many responsibilities.

## Too technical without context

```text
Add service and repo stuff.
```

No product purpose.

## Too product-focused without technical shape

```text
Users should have a better experience.
```

No implementation direction.

## No acceptance criteria

Nobody knows when it is done.

## Hidden dependencies

The ticket depends on work not mentioned.

## Mixed responsibilities

One ticket contains frontend, backend, database, security, and deployment all tangled together.

Bad ticket smell:

```text
If the ticket has many reasons to change,
it is probably too big.
```

That is SRP again.

---

# 🧱 1️⃣0️⃣ Ticket SRP: One Ticket, One Main Change

Just like a class should have one main reason to change, a ticket should represent one coherent unit of work.

Bad ticket:

```text
Build user registration, login, password hashing, email verification, user profile page, and admin dashboard.
```

This ticket has many reasons to change.

Better split:

```text
Create User Registration API
Add password hashing to registration
Create Login API
Create User Profile API
Create frontend registration screen
Add email verification flow
Create admin user list endpoint
```

Each ticket is now smaller, clearer, and easier to finish.

```text
Good tickets isolate change pressure.
```

That is Agile SRP.

---

# 🔌 1️⃣1️⃣ Ticket Boundaries and Software Boundaries

Good tickets often align with architecture boundaries.

For Fridge2Meal, the main backend layers are:

```text
Controller
DTO
Service
Repository
Entity
Exception
Validation
```

A ticket might focus on one vertical slice:

```text
POST /api/users
```

This touches multiple layers, but for one clear use case.

Or a ticket might focus on one horizontal concern:

```text
Add global exception handling
```

This affects error handling across endpoints.

Both can be valid.

The key is clarity.

Ask:

```text
Is this ticket a coherent change?
Can it be explained simply?
Can it be tested?
Can it be completed without swallowing half the project?
```

---

# 🧭 1️⃣2️⃣ Vertical Slice vs Horizontal Ticket

## Vertical Slice

A vertical slice delivers user-visible behaviour across layers.

Example:

```text
Create User Registration API
```

It may include:

```text
DTO
Controller
Service
Repository method
Exception
Postman test
```

This is good when the goal is a working feature.

## Horizontal Ticket

A horizontal ticket improves a layer or system concern.

Example:

```text
Add Global Exception Handler
```

It may include:

```text
ApiError DTO
ControllerAdvice
Exception handler methods
Consistent error response
```

This is good when the goal is infrastructure or shared behaviour.

Both are useful.

But do not confuse them.

```text
Vertical slice = feature flow
Horizontal ticket = shared layer/capability
```

---

# 🎯 1️⃣3️⃣ Agile and the Fridge2Meal Capstone

Fridge2Meal is the capstone.

That means tickets should connect to real product movement.

A useful capstone ticket should help the app become more real.

Example ticket sequence:

```text
Create User Registration API
Add Global Exception Handling
Add Request Validation
Create Get Users Endpoint
Create Get User By ID Endpoint
Create Fridge Entity + Repository
Create Fridge Creation API
Create Ingredient Entity + Repository
Create Ingredient API
Create Meal Suggestion API
```

This sequence turns the capstone into incremental progress.

Each ticket should move the system forward.

---

# 📌 1️⃣4️⃣ Example: Bad Ticket → Good Ticket

## Bad Ticket

```text
Do users.
```

Problems:

* vague
* too broad
* no endpoint
* no acceptance criteria
* no test path
* no clear layer responsibility

## Better Ticket

```text
Create User Registration API
```

## Good Ticket

```markdown
## Title

Create User Registration API

## Context

Fridge2Meal needs users so that fridge data, preferences, and meal suggestions can eventually belong to an account.

## User Story

As a Fridge2Meal user,
I want to register an account,
so that my fridge and meal data can be saved.

## Scope

Create a backend endpoint:

POST /api/users

The endpoint should accept first name, last name, email, and password.

The endpoint should save the user in PostgreSQL.

The response should return user id, first name, last name, and email.

The response must not return password.

## Out of Scope

Login is not included.
Password hashing is not included in this ticket unless instructed.
Email verification is not included.
Frontend registration screen is not included.

## Technical Notes

Use:
- UserRequest DTO
- UserResponse DTO
- UserService
- UserRepository
- UserController
- UserAlreadyExistsException

Add repository method:
boolean existsByEmail(String email)

## Acceptance Criteria

- Given a valid request, when POST /api/users is called, then a user is saved.
- Given the user is saved, then the API returns 201 Created.
- Given the response is returned, then it includes id, firstName, lastName, and email.
- Given the response is returned, then it does not include password.
- Given the email already exists, then registration is rejected with a clear error.
- Given the request is tested in Postman/Bruno, then the saved user appears in PostgreSQL.

## Verification

Test with Postman/Bruno.
Confirm row in PostgreSQL using pgAdmin/DBeaver.
Check backend logs for errors.
```

This is a strong ticket.

It gives the work shape.

---

# 🧨 1️⃣5️⃣ How to Read a Ticket

When you receive a ticket, do not immediately start coding.

First, read it like a developer.

Ask:

```text
What is the user or system goal?

What exact behaviour is expected?

What layers are affected?

What data enters?

What data leaves?

What should happen on success?

What should happen on failure?

How will I know it is done?

What is out of scope?

What dependencies exist?
```

A good developer does not just obey the ticket.

A good developer clarifies the ticket.

---

# 🧠 1️⃣6️⃣ Ticket Reading Checklist

Use this checklist before starting:

```text
1. Do I understand the goal?
2. Do I understand the user/system value?
3. Do I know the affected endpoint or feature?
4. Do I know the input shape?
5. Do I know the output shape?
6. Do I know the success behaviour?
7. Do I know the failure behaviour?
8. Do I know which layers are involved?
9. Do I know what is out of scope?
10. Do I know how to verify completion?
```

If too many answers are “no,” the ticket is not ready.

---

# 🚦 1️⃣7️⃣ Ready vs Not Ready

A ticket is ready when:

```text
The goal is clear.
The scope is clear.
The acceptance criteria are clear.
The dependencies are known.
The work is small enough to start.
The finish line is testable.
```

A ticket is not ready when:

```text
The wording is vague.
The scope is huge.
The acceptance criteria are missing.
The dependencies are hidden.
The technical direction is unclear.
Nobody can say how to test it.
```

This is called “ready for development.”

---

# 🧱 1️⃣8️⃣ Definition of Done

Definition of Done is the team’s shared agreement for completion.

For this course, a ticket might be done when:

```text
Code is implemented.
Backend starts successfully.
Endpoint works in Postman/Bruno.
Database state is confirmed where relevant.
Failure path is handled or noted.
No password or sensitive data is leaked.
Code is committed to Git.
README or notes updated if needed.
```

Acceptance criteria are specific to one ticket.

Definition of Done is the general team standard.

---

# 🔄 1️⃣9️⃣ Agile Ceremonies as Feedback Loops

Agile ceremonies are not theatre.

They are feedback loops.

## Stand-up

Purpose:

```text
Synchronize the team.
Reveal blockers.
Expose progress.
```

Not a performance.

## Sprint Planning

Purpose:

```text
Choose what change we will attempt next.
```

## Sprint Review

Purpose:

```text
Show what actually works.
```

## Retrospective

Purpose:

```text
Improve how the team works.
```

These ceremonies exist because software work is uncertain.

The team needs frequent correction.

---

# 📊 2️⃣0️⃣ Agile Board: Team Memory

An Agile board is the team’s working memory.

Common columns:

```text
Backlog
Ready
In Progress
Code Review
Testing
Done
```

The board shows:

```text
What we might do
What we committed to
What is being worked on
What is blocked
What is finished
```

Bad board:

```text
Everything stuck in progress.
No clear owners.
No clear done criteria.
Huge tickets everywhere.
```

Good board:

```text
Small tickets.
Clear status.
Visible blockers.
Work moving steadily.
```

The board is not admin.

It is the shared memory of delivery.

---

# ⚙️ 2️⃣1️⃣ Agile and Technical Debt

Sometimes a ticket creates technical debt.

Technical debt means:

```text
A shortcut taken now
that creates cost later.
```

Not all debt is evil.

Sometimes debt is strategic.

But hidden debt is dangerous.

Examples:

```text
Store passwords as plain text temporarily.
Return generic 500 errors temporarily.
Skip validation temporarily.
Use simple IDs before relationships.
```

These may be acceptable only if they are tracked.

Bad:

```text
We forgot this was temporary.
```

Good:

```text
Create follow-up ticket:
Add password hashing to registration.
```

Agile helps by making debt visible.

---

# 🧠 2️⃣2️⃣ The Deep Compression

Agile is not separate from architecture.

It is the architecture of work.

SOLID controls code change.

Tickets control task change.

Sprints control time.

Boards control visibility.

Reviews control feedback.

Retrospectives control team improvement.

```text
Good software needs good code boundaries.

Good teams need good work boundaries.
```

That is the connection.

---

# 🚀 Final Compression

```text
Agile = controlled change under uncertainty
Ticket = small unit of change
User story = purpose of the change
Acceptance criteria = proof of completion
Definition of Done = team standard for finished work
Backlog = possible future work
Sprint = timeboxed delivery container
Board = team working memory
Stand-up = synchronization loop
Retrospective = improvement loop
Technical debt = future cost created by present shortcut
```

---

# 🧠 Ultimate Compression

```text
SOLID teaches code how to survive change.

Agile teaches teams how to survive change.

Tickets are the boundary objects between intention and implementation.
```

A good ticket gives change a shape.

A bad ticket lets change leak everywhere.
