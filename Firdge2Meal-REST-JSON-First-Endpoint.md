# 🧩 Fridge2Meal — REST API Using JSON: First Endpoint

## Goal

By the end of this lesson, you should understand how a basic Spring Boot REST endpoint works.

You will build one endpoint:

```text
GET /api/meals/suggestion
```

When you open that endpoint in the browser, the backend should return JSON:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal using tomatoes and pasta.",
  "steps": [
    "Boil pasta",
    "Cook tomatoes",
    "Mix together"
  ]
}
```

This is the first real backend milestone.

The app is not intelligent yet.

It is alive.

---

# 1️⃣ Where We Are in the Curriculum

We are currently in:

```text
Stage 1: Core Java + REST API Using JSON
```

We have already done the setup work:

- created the project skeleton
- created the Spring Boot backend
- added Maven dependencies
- created the backend packages

Now we are moving from folder setup into actual API behaviour.

The focus today is:

```text
Request comes in → Java handles it → JSON goes back out
```

---

# 2️⃣ The Mental Model

A REST API is like a counter in a restaurant.

```text
Customer makes request
        ↓
Counter receives request
        ↓
Kitchen prepares response
        ↓
Customer receives result
```

In our backend:

```text
Browser / Frontend
        ↓
MealController
        ↓
MealService
        ↓
MealResponse DTO
        ↓
JSON response
```

The controller does not cook the meal.

The controller receives the order.

The service decides what to return.

The DTO is the shape of the message sent back.

---

# 3️⃣ What Is JSON?

JSON means JavaScript Object Notation.

It is a simple text format for sending data between systems.

Example:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal",
  "steps": ["Boil pasta", "Cook tomatoes"]
}
```

Even though JSON came from JavaScript, almost every language can use it:

- Java
- JavaScript
- Python
- C#
- Go
- PHP

In this project:

```text
Spring Boot creates JSON
React Native reads JSON
```

---

# 4️⃣ What Files Are We Creating?

We need three files today:

```text
backend/src/main/java/com/fridge2meal/controller/MealController.java
backend/src/main/java/com/fridge2meal/service/MealService.java
backend/src/main/java/com/fridge2meal/dto/MealResponse.java
```

Their jobs:

| File | Job |
|---|---|
| `MealController.java` | Defines the API URL |
| `MealService.java` | Contains the backend logic |
| `MealResponse.java` | Defines the JSON response shape |

---

# 5️⃣ Step One — Create the DTO

A DTO means Data Transfer Object.

It is not the database model.

It is not the business logic.

It is the message shape that leaves the API.

Create this file:

```text
backend/src/main/java/com/fridge2meal/dto/MealResponse.java
```

Add:

```java
package com.fridge2meal.dto;

import java.util.List;

public record MealResponse(
    String title,
    String description,
    List<String> steps
) {
}
```

## What this means

```java
public record MealResponse(...)
```

A `record` is a compact Java way to create a simple data object.

Spring Boot can automatically convert this object into JSON.

So this Java object:

```java
new MealResponse("Tomato Pasta", "A simple meal", List.of("Boil pasta"))
```

can become this JSON:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple meal",
  "steps": ["Boil pasta"]
}
```

---

# 6️⃣ Step Two — Create the Service

The service is where the backend decision happens.

Create this file:

```text
backend/src/main/java/com/fridge2meal/service/MealService.java
```

Add:

```java
package com.fridge2meal.service;

import com.fridge2meal.dto.MealResponse;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class MealService {

    public MealResponse generateMeal() {
        return new MealResponse(
            "Tomato Pasta",
            "A simple pasta meal using tomatoes and pasta.",
            List.of("Boil pasta", "Cook tomatoes", "Mix together")
        );
    }
}
```

## What this means

```java
@Service
```

This tells Spring:

```text
This class contains business logic. Please manage it for me.
```

The method:

```java
public MealResponse generateMeal()
```

returns a fake meal for now.

Later, this method can call:

- AI
- database
- image analysis
- recipe logic

But today it returns fixed data so we can prove the API works.

---

# 7️⃣ Step Three — Create the Controller

The controller defines the API endpoint.

Create this file:

```text
backend/src/main/java/com/fridge2meal/controller/MealController.java
```

Add:

```java
package com.fridge2meal.controller;

import com.fridge2meal.dto.MealResponse;
import com.fridge2meal.service.MealService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/meals")
public class MealController {

    private final MealService mealService;

    public MealController(MealService mealService) {
        this.mealService = mealService;
    }

    @GetMapping("/suggestion")
    public MealResponse getSuggestion() {
        return mealService.generateMeal();
    }
}
```

## What this means

```java
@RestController
```

This tells Spring:

```text
This class handles web/API requests and returns data.
```

```java
@RequestMapping("/api/meals")
```

This sets the base URL for this controller.

```java
@GetMapping("/suggestion")
```

This creates the full endpoint:

```text
GET /api/meals/suggestion
```

So the final URL is:

```text
http://localhost:8080/api/meals/suggestion
```

---

# 8️⃣ Important Pattern: Constructor Injection

This part matters:

```java
private final MealService mealService;

public MealController(MealService mealService) {
    this.mealService = mealService;
}
```

This is called constructor injection.

It means:

```text
MealController needs MealService.
Spring gives MealService to MealController automatically.
```

This is better than creating it manually:

```java
MealService mealService = new MealService();
```

Why?

Because Spring manages the objects for us.

That makes the app easier to test, replace, and grow.

---

# 9️⃣ Step Four — Run the Backend

Go into the backend folder:

```bash
cd backend
```

Run the app:

```bash
./mvnw spring-boot:run
```

On Windows:

```bash
mvnw.cmd spring-boot:run
```

Wait until you see something like:

```text
Started Fridge2MealApplication
```

Now open:

```text
http://localhost:8080/api/meals/suggestion
```

You should see JSON.

---

# 🔟 Step Five — Test with cURL

The browser is fine for a GET request.

But developers often test APIs with tools like cURL, Postman, or Insomnia.

Try:

```bash
curl http://localhost:8080/api/meals/suggestion
```

You should get the same JSON response.

If you want formatted JSON, you can use Postman or install `jq` later.

---

# 1️⃣1️⃣ What Counts as Done?

You are done when:

- the backend runs without errors
- the endpoint opens in the browser
- `/api/meals/suggestion` returns JSON
- you can explain what the controller does
- you can explain what the service does
- you can explain what the DTO does

The minimum win:

```text
I can send a request to my Java backend and get JSON back.
```

---

# 1️⃣2️⃣ Common Errors

## Error: package does not exist

Check your package names.

The top of each file should match its folder.

Example:

```java
package com.fridge2meal.controller;
```

must be inside:

```text
com/fridge2meal/controller/
```

## Error: cannot find symbol List

You probably forgot:

```java
import java.util.List;
```

## Error: Whitelabel Error Page

Check the URL carefully.

Correct:

```text
http://localhost:8080/api/meals/suggestion
```

Wrong examples:

```text
http://localhost:8080/meals/suggestion
http://localhost:8080/api/meal/suggestion
http://localhost:8080/api/meals/suggestions
```

## Error: Port 8080 already in use

Something else is using port 8080.

Add this to `application.properties`:

```properties
server.port=8081
```

Then use:

```text
http://localhost:8081/api/meals/suggestion
```

---

# 1️⃣3️⃣ The Bigger Picture

Today’s flow:

```text
GET request → Controller → Service → DTO → JSON
```

Later, the flow will become:

```text
Image upload → Controller → Service → AI Adapter → MealResponse → JSON
```

And later still:

```text
Image upload → AI → Database → Saved recipe history → Cloud deployment
```

But all of that grows from today’s simple endpoint.

First endpoint first.

Everything else hangs from this.
