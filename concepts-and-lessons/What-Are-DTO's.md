## DTOs are guardrails for developer behaviour 🛤️

They don’t just protect the domain from users.
They protect the domain from **developers taking shortcuts**.

---

### The core control mechanism

DTOs force developers to ask:

* “Is this external data or domain meaning?”
* “Should this field be exposed?”
* “Who is allowed to change this value?”
* “Does this belong in the API contract or the domain model?”
* “Am I leaking persistence/domain internals?”

That mental pause is the enforcement.

---

### How they maintain DDD discipline

#### 1. They stop entity abuse

Without DTOs, developers start using entities everywhere:

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}
```

This teaches the codebase a bad habit:

```text
API object = domain object = database object
```

DTOs break that habit.

```java
public CreateUserRequest {
    String name;
    String email;
}
```

Now the developer cannot casually expose `id`, `role`, `status`, `createdAt`, `internalNotes`, etc.

---

#### 2. They make boundaries visible

DDD can sound abstract until the folder structure enforces it:

```text
controller/dto
application/service
domain/model
infrastructure/repository
```

The DTO becomes a visual reminder:

```text
This class belongs to the outside layer.
It must not contain business rules.
It must not become the domain.
```

That shapes how future developers extend the system.

---

#### 3. They force translation

The act of mapping DTO → domain is where design thinking happens.

```java
Order order = Order.create(
    customer,
    request.toOrderLines()
);
```

This prevents lazy mutation like:

```java
order.setStatus(request.status);
```

Instead, the developer is pushed toward domain language:

```java
order.place();
order.cancel();
order.markAsPaid();
```

That is DDD.

Not “set fields.”
Perform meaningful business actions.

---

#### 4. They protect invariants

DTOs can accept incomplete, messy, external data.

The domain should never live in that messy state.

```text
DTO: maybe invalid
Domain object: valid by construction
```

So a developer learns:

```text
Validation at the edge.
Rules in the domain.
Persistence after meaning is protected.
```

That keeps the domain strong.

---

### The big idea

DTOs create **friction against bad architecture**.

They make the easy wrong thing harder:

```text
Just expose the entity.
Just add another setter.
Just let the frontend update status.
Just return the database object.
```

And they make the right thing normal:

```text
Create request object.
Map to domain command.
Call domain behaviour.
Return response DTO.
```

---

### Learner-friendly phrasing

DTOs are like a **dress code for data** 👔

Any data entering the system must wear the right uniform before it can talk to the domain.

That means developers cannot just throw raw external data into the core of the application. They have to pass through a controlled layer that says:

```text
What is this data?
Where did it come from?
What is it allowed to change?
What domain action does it represent?
```

That is how DTOs help maintain DDD inside the codebase.
