# 🚀 User Registration API — First Full Backend Loop for Fridge2Meal 👤☕️

The setup gave the project a skeleton.

The database exists.

The `users` table exists.

The `User` entity exists.

The repository gives Java a way to speak to the database.

Now we make the backend do something real.

We are going to build the first complete API loop:

```text
JSON comes in.

Java receives it.

The service applies the registration rule.

The repository saves the user.

PostgreSQL remembers it.

JSON goes back out.
```

This is the first moment the backend truly speaks.

---

# 🎯 Goal

By the end of this guide, you will build this flow:

```text
JSON request
        ↓
Controller
        ↓
DTO
        ↓
Service
        ↓
Repository
        ↓
PostgreSQL users table
        ↓
Response DTO
        ↓
JSON response
```

You are moving from:

```text
Project installed
Database created
Tables migrated
Entities created
Repositories created
```

to:

```text
The backend can receive real requests and create real users.
```

---

# 🧠 1️⃣ The Feature We Are Building

We will build:

```http
POST /api/users
```

This endpoint will create a new user.

Request body:

```json
{
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com",
  "password": "password123"
}
```

Response body:

```json
{
  "id": 1,
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com"
}
```

Important:

```text
Do not return the password.
```

The password enters the system.

The password may be stored.

But the password should not leave in the API response.

That is boundary discipline.

---

# 🗺️ 2️⃣ The Package Structure

Make sure your backend has this structure:

```text
com.fridge2meal
├── controller
├── dto
├── entity
├── repository
├── service
├── exception
└── config
```

For this feature, we will use:

```text
entity/User.java
repository/UserRepository.java
dto/UserRequest.java
dto/UserResponse.java
service/UserService.java
controller/UserController.java
exception/UserAlreadyExistsException.java
```

Each file has a job.

```text
Entity = database shape
Repository = storage boundary
DTO = API boundary shape
Service = business use case
Controller = HTTP boundary
Exception = named failure
```

---

# 🧱 3️⃣ Confirm the User Entity

Your `User` entity should map to the `users` table.

Create/update:

```text
entity/User.java
```

```java
package com.fridge2meal.entity;

import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "first_name", nullable = false)
    private String firstName;

    @Column(name = "last_name", nullable = false)
    private String lastName;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(nullable = false)
    private String password;

    @Column(name = "created_at")
    private LocalDateTime createdAt;

    @Column(name = "updated_at")
    private LocalDateTime updatedAt;

    @Column(name = "deleted_at")
    private LocalDateTime deletedAt;

    public User() {
    }

    public User(String firstName, String lastName, String email, String password) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.password = password;
    }

    @PrePersist
    public void onCreate() {
        LocalDateTime now = LocalDateTime.now();
        this.createdAt = now;
        this.updatedAt = now;
    }

    @PreUpdate
    public void onUpdate() {
        this.updatedAt = LocalDateTime.now();
    }

    public Long getId() {
        return id;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public String getEmail() {
        return email;
    }

    public String getPassword() {
        return password;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }

    public LocalDateTime getUpdatedAt() {
        return updatedAt;
    }

    public LocalDateTime getDeletedAt() {
        return deletedAt;
    }
}
```

What this means:

```text
User entity ↔ users table
```

The Java object represents a database row while the app is running.

---

# 🗄️ 4️⃣ Create / Update the UserRepository

Create:

```text
repository/UserRepository.java
```

```java
package com.fridge2meal.repository;

import com.fridge2meal.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository extends JpaRepository<User, Long> {

    boolean existsByEmail(String email);

    Optional<User> findByEmail(String email);
}
```

What this gives us:

```text
save(user)
findById(id)
findAll()
existsByEmail(email)
findByEmail(email)
```

The important custom method for registration is:

```java
boolean existsByEmail(String email);
```

Because before creating a user, we need to check:

```text
Is this email already taken?
```

---

# 📥 5️⃣ Create the UserRequest DTO

Create:

```text
dto/UserRequest.java
```

```java
package com.fridge2meal.dto;

public record UserRequest(
    String firstName,
    String lastName,
    String email,
    String password
) {
}
```

This is the input shape.

It represents JSON entering the backend.

```text
JSON request body
        ↓
UserRequest object
```

---

# 📤 6️⃣ Create the UserResponse DTO

Create:

```text
dto/UserResponse.java
```

```java
package com.fridge2meal.dto;

public record UserResponse(
    Long id,
    String firstName,
    String lastName,
    String email
) {
}
```

This is the output shape.

Notice:

```text
No password.
```

The response exposes only what the client should receive.

```text
User entity
        ↓
UserResponse DTO
        ↓
JSON response
```

---

# 🧨 7️⃣ Create the UserAlreadyExistsException

Create:

```text
exception/UserAlreadyExistsException.java
```

```java
package com.fridge2meal.exception;

public class UserAlreadyExistsException extends RuntimeException {

    public UserAlreadyExistsException(String message) {
        super(message);
    }
}
```

This gives a name to a business failure.

Instead of vague failure:

```text
Something went wrong.
```

we now have specific failure:

```text
User already exists.
```

---

# 🎯 8️⃣ Create the UserService

Create:

```text
service/UserService.java
```

```java
package com.fridge2meal.service;

import com.fridge2meal.dto.UserRequest;
import com.fridge2meal.dto.UserResponse;
import com.fridge2meal.entity.User;
import com.fridge2meal.exception.UserAlreadyExistsException;
import com.fridge2meal.repository.UserRepository;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public UserResponse register(UserRequest request) {
        if (userRepository.existsByEmail(request.email())) {
            throw new UserAlreadyExistsException("User already exists with email: " + request.email());
        }

        User user = new User(
            request.firstName(),
            request.lastName(),
            request.email(),
            request.password()
        );

        User savedUser = userRepository.save(user);

        return new UserResponse(
            savedUser.getId(),
            savedUser.getFirstName(),
            savedUser.getLastName(),
            savedUser.getEmail()
        );
    }
}
```

What the service does:

```text
Check business rule
        ↓
Create entity
        ↓
Save through repository
        ↓
Map entity to response DTO
```

The service owns the use case.

The repository owns database access.

The controller owns HTTP.

---

# 🚦 9️⃣ Create the UserController

Create:

```text
controller/UserController.java
```

```java
package com.fridge2meal.controller;

import com.fridge2meal.dto.UserRequest;
import com.fridge2meal.dto.UserResponse;
import com.fridge2meal.service.UserService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public ResponseEntity<UserResponse> register(@RequestBody UserRequest request) {
        UserResponse response = userService.register(request);

        return ResponseEntity.status(201).body(response);
    }
}
```

What this means:

```text
POST /api/users
        ↓
UserController.register()
        ↓
UserService.register()
        ↓
UserRepository.save()
        ↓
201 Created + UserResponse JSON
```

The controller is thin.

It does not create users directly.

It delegates to the service.

---

# 🔄 1️⃣0️⃣ Full Runtime Flow

When you send a request:

```http
POST /api/users
```

with body:

```json
{
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com",
  "password": "password123"
}
```

the backend flow is:

```text
JSON request body
        ↓
Spring deserializes JSON
        ↓
UserRequest object in memory
        ↓
UserController receives request
        ↓
UserService checks email uniqueness
        ↓
User entity created
        ↓
UserRepository saves entity
        ↓
Hibernate generates SQL
        ↓
PostgreSQL inserts row
        ↓
Saved User returned
        ↓
UserResponse created
        ↓
Spring serializes response to JSON
        ↓
HTTP 201 response returned
```

This is the first full backend loop.

---

# 🧪 1️⃣1️⃣ Test With Postman / Bruno / Insomnia

Start the backend:

```powershell
cd C:\dev\Fridge2Meal\backend
.\mvnw.cmd spring-boot:run
```

Create request:

```text
Method: POST
URL: http://localhost:8080/api/users
```

Headers:

```text
Content-Type: application/json
```

Body:

```json
{
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com",
  "password": "password123"
}
```

Expected status:

```text
201 Created
```

Expected response:

```json
{
  "id": 1,
  "firstName": "Amina",
  "lastName": "Khan",
  "email": "amina@example.com"
}
```

---

# 🗄️ 1️⃣2️⃣ Confirm the User Was Saved

In pgAdmin or DBeaver, run:

```sql
SELECT id, first_name, last_name, email, created_at, updated_at
FROM users;
```

You should see the created user.

Important:

```text
The API response proves the backend responded.

The database query proves the data was persisted.
```

Both matter.

---

# ⚠️ 1️⃣3️⃣ Try Duplicate Email

Send the same request again.

Expected result:

```text
The backend should throw UserAlreadyExistsException.
```

At this stage, you may see a generic 500 error.

That is okay for now.

Current lesson:

```text
Service detects business failure.
Exception represents the failure.
```

Next improvement:

```text
Global exception handling converts exceptions into clean API error responses.
```

---

# 🧱 1️⃣4️⃣ Why This Architecture Matters

This feature uses the backend layers properly.

| Layer | Job |
|---|---|
| Controller | receives HTTP request |
| DTO | shapes input/output |
| Service | owns registration use case |
| Repository | accesses database |
| Entity | maps to database table |
| Exception | names failure path |

This is professional backend structure.

Not everything in one controller.

Not SQL in the controller.

Not business rules in the repository.

Each layer has a job.

---

# 🧨 1️⃣5️⃣ Common Errors

## 404 Not Found

Check:

```text
URL is /api/users
Controller has @RequestMapping("/api/users")
Method has @PostMapping
Backend is running
```

## 415 Unsupported Media Type

Check header:

```text
Content-Type: application/json
```

## 400 Bad Request

Check JSON syntax.

## 500 Internal Server Error

Check backend console logs.

Possible causes:

```text
database not running
entity/table mismatch
Flyway migration failed
duplicate email
repository method issue
```

## Hibernate validation error

The entity does not match the database schema.

Check:

```text
@Column names
field types
table name
id type
```

---

# 🚀 1️⃣6️⃣ Completion Checklist

You are done when:

```text
UserRequest exists
UserResponse exists
UserAlreadyExistsException exists
UserRepository has existsByEmail()
UserService has register()
UserController has POST /api/users
Backend starts successfully
POST /api/users returns 201
Password is not returned
User appears in PostgreSQL
Duplicate email is detected
```

---

# 🧠 Final Compression

```text
Controller = receives HTTP request
DTO = request/response shape
Service = registration use case
Repository = database access
Entity = users table row as Java object
Exception = named business failure
POST /api/users = first full backend loop
```

---

# 🌌 Ultimate Compression

```text
The setup gave the app a skeleton.

The repository gives it memory.

The service gives it intention.

The controller gives it a voice.

The User Registration API is the first moment the backend truly speaks.
```
