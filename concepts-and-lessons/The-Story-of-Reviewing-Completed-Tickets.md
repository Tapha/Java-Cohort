# 🔍 The Story of Review — How Developers Prove a Ticket Became Working Software 🎟️✅

The class has now worked on a ticket.

A feature has been implemented across its full loop.

That means the work has moved from:

```text
Ticket
        ↓
Code
        ↓
Pull Request
        ↓
Review
```

This is an important professional moment.

Because a ticket is not complete just because code exists.

A ticket is complete when the code proves that the requested behaviour now works.

That is the story of review.

```text
A ticket is the promise.

A pull request is the evidence.

A review checks whether the evidence proves the promise.
```

---

# 🧠 1️⃣ Why Review Exists

Review is not there to embarrass people.

Review is not there to prove who is better.

Review is not there to argue about personal style.

Review exists because software is shared reality.

When code is merged, it becomes part of the system everyone else must live with.

So the question is not:

```text
Do I like this code?
```

The better question is:

```text
Is this change safe, clear, and correct enough to enter the shared system?
```

That is the professional mindset.

---

# 🎟️ 2️⃣ Start With the Ticket

Before reviewing the code, read the ticket.

The ticket tells you what the work was supposed to achieve.

Ask:

```text
What was the goal?

What behaviour was expected?

What was in scope?

What was out of scope?

What acceptance criteria were given?

How should this be tested?
```

If you review without reading the ticket, you may judge the wrong thing.

A reviewer should not start with personal preference.

A reviewer starts with the contract.

```text
Ticket = intended change
PR = proposed implementation
Review = comparison between the two
```

---

# 🧾 3️⃣ The Ticket Is the Contract

A good ticket usually defines:

```text
Title
Context
User story / goal
Scope
Out of scope
Technical notes
Acceptance criteria
Verification steps
```

The most important part for review is:

```text
Acceptance criteria
```

Acceptance criteria are the finish line.

If the ticket says:

```text
POST /api/users should create a user and return 201 Created.
```

Then the reviewer must check:

```text
Does POST /api/users exist?

Does it create a user?

Does it return 201 Created?

Does the user appear in the database?

Does the response match the expected shape?
```

Review is evidence-based.

---

# 🔄 4️⃣ The Full-Loop Review

For a backend feature, reviewing only one file is not enough.

A full-loop feature travels through layers.

For Fridge2Meal, a typical backend loop is:

```text
Request JSON
        ↓
Request DTO
        ↓
Controller
        ↓
Service
        ↓
Repository
        ↓
Entity
        ↓
Database
        ↓
Response DTO
        ↓
Response JSON
```

A good review checks the whole loop.

Not just “does the controller look okay?”

Not just “does the service compile?”

The question is:

```text
Does the whole feature work from outside request to stored data to outside response?
```

That is full-loop thinking.

---

# 🧱 5️⃣ Review the Layers

Each layer has a job.

A good reviewer checks whether each layer is doing the right kind of work.

| Layer | Review Question |
|---|---|
| DTO | Does the input/output shape match the ticket? |
| Controller | Is it receiving HTTP and delegating properly? |
| Service | Is business logic in the right place? |
| Repository | Is it only handling persistence access? |
| Entity | Does it match the database schema? |
| Exception | Are failure paths named clearly? |
| Database | Does the expected data actually change? |

This connects directly to SOLID.

A class should have one main reason to change.

A layer should have one main kind of responsibility.

A ticket should have one main piece of change.

Same law.

Different levels.

---

# 🧠 6️⃣ Review Against SOLID

When reviewing code, use SOLID as a lens.

## SRP — Single Responsibility Principle

Ask:

```text
Is this class doing one clear kind of job?
```

Bad smell:

```text
Controller validates, decides business rules, saves to DB, builds response, logs everything.
```

Better:

```text
Controller receives request.
Service decides.
Repository stores.
DTO shapes data.
```

## OCP — Open/Closed Principle

Ask:

```text
Will adding the next feature require breaking this one?
```

Good code makes the next change easier.

## DIP — Dependency Inversion Principle

Ask:

```text
Is the class depending on the right abstraction?
```

Example:

```text
Controller depends on Service.
Service depends on Repository.
```

Bad:

```text
Controller directly talks to database.
```

---

# ✅ 7️⃣ Review the Acceptance Criteria

For each acceptance criterion, write one of these:

```text
Met
Not met
Unclear
```

Example:

| Acceptance Criteria | Review Result |
|---|---|
| POST /api/users exists | Met |
| Valid request creates user | Met |
| Returns 201 Created | Met |
| Response excludes password | Met |
| Duplicate email handled | Unclear |
| User appears in database | Met |

This makes review concrete.

No vague feedback.

No vibes.

No ego.

Just evidence.

---

# 🧪 8️⃣ Review by Running the App

Reading code is useful.

Running the feature is stronger.

For an API ticket, reviewers should test manually:

```text
Start backend
Open Postman / Bruno / Insomnia
Send request
Check status code
Check response body
Check database
Check failure case
```

Example for User Registration:

```http
POST /api/users
```

Request:

```json
{
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com",
  "password": "password123"
}
```

Expected:

```text
201 Created
Response includes id, firstName, lastName, email
Response does not include password
User appears in users table
```

The strongest review evidence is working behaviour.

---

# 🗄️ 9️⃣ Review the Database Result

If the ticket changes persistent data, check the database.

For example:

```sql
SELECT id, first_name, last_name, email, created_at, updated_at
FROM users;
```

Ask:

```text
Was the row created?

Are the fields correct?

Are timestamps populated?

Is sensitive data exposed in the API response?

Does the database match what the API claimed happened?
```

An API response can look right while storage is wrong.

A good reviewer checks both.

---

# ⚠️ 1️⃣0️⃣ Review Failure Paths

Beginners often test only the happy path.

Professionals review failure paths too.

Ask:

```text
What happens if the request is invalid?

What happens if the email already exists?

What happens if the ID does not exist?

What happens if the database is unavailable?

What error does the client receive?
```

For now, some failure handling may be incomplete.

That is okay if it is tracked.

Bad:

```text
We ignore the failure path.
```

Better:

```text
We note the failure path and create the next ticket.
```

Example follow-up ticket:

```text
Add Global Exception Handling for User Registration API
```

---

# 🔐 1️⃣1️⃣ Review Sensitive Data

Some mistakes are more serious than others.

For user features, always check:

```text
Is password returned in the response?

Is password logged?

Is private data exposed?

Is the response showing more than the client needs?
```

For the current early implementation, password hashing may be deferred.

But password exposure should not be accepted.

```text
Plain password storage may be temporary technical debt.

Returning password in the API response is a boundary failure.
```

That distinction matters.

---

# 🧹 1️⃣2️⃣ Review Readability

Code is read more often than it is written.

Ask:

```text
Can another student understand this tomorrow?

Are names clear?

Are methods small enough?

Is the flow obvious?

Is there unnecessary duplication?

Are files in the correct packages?

Is the code formatted consistently?
```

Readable code protects the next developer.

That next developer might be you.

---

# 🧭 1️⃣3️⃣ Good Review Comments

Good review comments are specific, respectful, and tied to the work.

Bad comment:

```text
This is bad.
```

Better:

```text
The controller is currently creating the User entity directly. Since the ticket uses the controller/service/repository structure, this logic should move into UserService.
```

Bad comment:

```text
Wrong.
```

Better:

```text
The response currently includes password. The acceptance criteria says password should not be returned, so UserResponse should exclude it.
```

Bad comment:

```text
I would not do it like this.
```

Better:

```text
This works, but the service method is doing both registration and response formatting. We may want to keep the mapping clear so the next endpoint is easier to add.
```

Good reviewers explain the reason.

---

# 🧠 1️⃣4️⃣ Review Without Ego

Review can feel personal.

But professional review is not an attack.

The code is not the person.

The PR is a proposed change to the shared system.

Reviewer mindset:

```text
I am helping protect the system.
```

Author mindset:

```text
The reviewer is helping make the change safer.
```

The best teams do not avoid critique.

They make critique safe, useful, and specific.

---

# 🚦 1️⃣5️⃣ Approve, Comment, or Request Changes

A reviewer usually has three choices.

## Approve

Use when:

```text
The ticket is fulfilled.
The app works.
The code is understandable.
No serious issues remain.
```

## Comment

Use when:

```text
There are small suggestions.
The work is mostly correct.
The issue does not block merging.
```

## Request Changes

Use when:

```text
Acceptance criteria are not met.
The app does not run.
The response is wrong.
Sensitive data is exposed.
The wrong layer owns the logic.
A serious failure path is ignored.
```

Do not request changes for tiny personal preferences.

Do request changes for correctness, safety, and maintainability.

---

# 🔁 1️⃣6️⃣ After Review: What Happens Next?

After peer review, the class may have several PRs.

Some will be strong.

Some will be incomplete.

Some will have good ideas but weak structure.

Some will have clean structure but missing behaviour.

The team must then decide:

```text
Which implementation should merge?

What useful ideas should be taken from the others?

What follow-up tickets should be created?
```

A rejected PR is not wasted.

It can still teach the team.

It can still contain useful code.

It can still reveal a better direction.

The goal is not to crown one person.

The goal is to improve the shared system.

---

# 🧬 1️⃣7️⃣ Merge Means Shared Responsibility

Once code is merged, it is no longer “their code.”

It becomes:

```text
our code
```

That means:

```text
Everyone now depends on it.
Everyone must work around it.
Everyone may need to extend it.
Everyone may be affected by its bugs.
```

So review matters.

Merge is not just a Git action.

Merge is system mutation.

```text
Before merge = isolated attempt

After merge = shared reality
```

---

# 🧪 1️⃣8️⃣ Review Checklist for This Class

Use this checklist when reviewing another student’s PR.

```text
1. Did I read the ticket first?
2. Does the implementation match the ticket?
3. Does the app run?
4. Can I test the feature manually?
5. Does the endpoint path match the requirement?
6. Does the request body match the expected DTO?
7. Does the response body match the expected DTO?
8. Does the response status code match the ticket?
9. Does the database change correctly?
10. Is sensitive data hidden?
11. Is business logic in the service?
12. Is the controller thin?
13. Is persistence handled through the repository?
14. Are entity/table mappings correct?
15. Is the failure path handled or clearly noted?
16. Are names clear?
17. Is the code readable?
18. Would I be comfortable building the next feature on top of this?
```

That final question is powerful:

```text
Would I be comfortable building the next feature on top of this?
```

If the answer is no, explain why.

---

# 📌 1️⃣9️⃣ Example Review: User Registration API

Ticket:

```text
Create User Registration API
```

Expected:

```text
POST /api/users
Valid request creates user
Returns 201 Created
Response includes id, firstName, lastName, email
Response does not include password
Duplicate email is rejected
User appears in PostgreSQL
```

Review table:

| Check | Result | Notes |
|---|---|---|
| Endpoint exists | ✅ Met | `/api/users` present |
| POST method works | ✅ Met | Tested in Postman |
| User saved | ✅ Met | Row appears in DB |
| Status code 201 | ✅ Met | Correct |
| Password excluded | 🔴 Not met | Response currently includes password |
| Duplicate email handled | 🟡 Unclear | Exception thrown but response not clean |
| Controller thin | ✅ Met | Delegates to service |
| Service owns logic | ✅ Met | Email check inside service |
| Repository used correctly | ✅ Met | Uses `existsByEmail` and `save` |

Review conclusion:

```text
Request changes:
Password must be removed from the response before merge.

Follow-up ticket:
Add global exception handling for duplicate email.
```

This is clear.

It separates blocking issues from future improvements.

---

# 🧠 2️⃣0️⃣ The Deep Compression

A ticket defines intended change.

A PR proposes code that claims to fulfil that change.

A review checks the claim.

Testing provides evidence.

Comments improve the change.

Approval allows the change into shared reality.

Merge turns individual work into team-owned software.

---

# 🚀 Final Compression

```text
Ticket = promise
PR = evidence
Review = proof check
Acceptance criteria = finish line
Manual testing = behaviour evidence
Database check = persistence evidence
Review comment = improvement signal
Request changes = protect the system
Approve = accept the change
Merge = shared system mutation
```

---

# 🌌 Ultimate Compression

```text
Agile controls the flow of work.

SOLID controls the shape of code.

Review connects them.

It asks:

Did this ticket become working software
without damaging the system?
```

That is the professional standard.
