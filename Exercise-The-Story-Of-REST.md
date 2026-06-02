# Exercise: From SOAP Thinking to REST Thinking

## REST as the open road

In the story of REST, we saw a shift:

```txt
SOAP = operation-first
REST = resource-first
```

This exercise helps you practise that shift.

You are not just learning API syntax. You are learning how to think about communication between systems.

---

## Learning goals

By the end of this exercise, you should be able to:

- explain the difference between SOAP-style and REST-style thinking
- identify resources in a simple app
- choose suitable HTTP methods
- design REST endpoints for Fridge2Meal
- connect a Spring Boot controller to a JSON response

---

## Part 1: Warm-up questions

Answer these in your own words.

### 1. What does SOAP mainly focus on?

Write one sentence.

```txt
SOAP mainly focuses on...
```
```txt
SOAP mainly focuses on operations
```

### 2. What does REST mainly focus on?

Write one sentence.

```txt
REST mainly focuses on...
```
```txt
REST mainly focuses on resources
```


### 3. Complete the contrast

```txt
SOAP thinks in operations.
REST thinks in resources.
```

### 4. Why did JSON become common with REST APIs?

Write two reasons.

```txt
Reason 1: JSON is easier to read
Reason 2: Natural for javascript
```

---

## Part 2: Spot the thinking style

For each example, decide whether it feels more like SOAP thinking or REST thinking.

Write either `SOAP` or `REST`.

| Example | SOAP or REST? | Why? |
|---|---|---|
| `GetMealSuggestion()` | SOAP | It's looking for an operation |
| `POST /api/meals` | REST | It's going straight for the resource at the URL with the http method |
| `CheckAccountBalance()` | SOAP| It's looking for an operation |
| `GET /api/recipes/15` |REST | It's going straight for the resource at the URL with the http method |
| `SubmitInsuranceClaim()` |SOAP |It's looking for an operation  |
| `DELETE /api/meals/42` |REST |It's going straight for the resource at the URL with the http method |

### Hint

If it looks like a named operation or function, it is probably SOAP-style thinking.

If it looks like a URL/resource plus an HTTP method, it is probably REST-style thinking.

---

## Part 3: Translate SOAP-style operations into REST-style endpoints

Convert each operation into a REST-style endpoint.

You do not need to write Java yet. Just design the API route.

### Example

SOAP-style operation:

```txt
GetMealSuggestion()
```

REST-style endpoint:

```http
GET /api/meals/suggestion
```

Now try these.

| SOAP-style operation | REST-style endpoint |
|---|---|
| `CreateMeal()` | POST /api/meals |
| `GetMealById()` | GET /api/meals/Id |
| `DeleteMeal()` | DELETE /api/meals/Id |
| `UploadFridgeImage()` | PUT api/meals/Id |
| `GetRecipeSteps()` | GET /api/recipies/Id|

### Think carefully

Ask yourself:

```txt
What is the resource?
What action am I performing?
Which HTTP method matches that action?
```

Useful method guide:

| HTTP method | Meaning |
|---|---|
| `GET` | Read something |
| `POST` | Create or submit something |
| `PUT` | Replace/update something |
| `PATCH` | Partially update something |
| `DELETE` | Remove something |

---

## Part 4: Identify Fridge2Meal resources

Fridge2Meal is an app where the user takes a photo of fridge ingredients and receives a meal suggestion.

List at least four possible resources in the app.

Example:

```txt
/meals
```

Your answers:

```txt
1. /images
2. /recipies
3. /ingredients
4. /notes
```

Now choose the two most important resources for version 1.

```txt
Most important resource 1: meals
Most important resource 2: images
```

---

## Part 5: Design the first Fridge2Meal API

We want a very simple first endpoint.

It should return a fake meal suggestion as JSON.

The frontend or browser should be able to call:

```http
GET /api/meals/suggestion
```

The response should look like this:

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

Answer these before coding.

### 1. What is the resource?

```txt
Resource: meals
```

### 2. What HTTP method are we using?

```txt
HTTP method: GET
```

### 3. Which class handles the route?

```txt
Class: MealController
```

### 4. Which class contains the business logic?

```txt
Class:MealService
```

### 5. Which class shapes the response data?

```txt
Class:MealResponse
```

---

## Part 6: Build the endpoint

Create these files in your Spring Boot backend:

```txt
backend/src/main/java/com/fridge2meal/controller/MealController.java
backend/src/main/java/com/fridge2meal/service/MealService.java
backend/src/main/java/com/fridge2meal/dto/MealResponse.java
```

### Step 1: Create the DTO

```java
package com.fridge2meal.dto;

import java.util.List;

public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
```

### Step 2: Create the service

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

### Step 3: Create the controller

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

---

## Part 7: Run and test

Start the backend.

In the terminal:

```bash
cd backend
./mvnw spring-boot:run
```

On Windows:

```bash
cd backend
mvnw.cmd spring-boot:run
```

Then open this URL in your browser:

```txt
http://localhost:8080/api/meals/suggestion
```

You should see JSON.

---

## Part 8: Explain the API loop

Fill in the blanks.

```txt
The browser sends a Http request.
The request enters the controller.
The controller calls the service.
The service returns a MealResponse object.
Spring Boot converts the Java object into JSON.
The browser displays the response.
```

Word bank:

```txt
JSON
Controller
HTTP
Service
MealResponse
```

---

## Part 9: Reflection

Answer in 3-5 sentences.

```txt
Why is REST a good fit for Fridge2Meal?
```

Use at least three of these words:

```txt
resource
JSON
frontend
backend
HTTP
boundary
```

```txt
In fridge2meal, when the frontend sends a Http request, the mobile app doesn't need to know what the backend is doing. The frontend only needs a clear endpoint and a clear JSON response
```

---

## Stretch challenge

Add another endpoint:

```http
GET /api/meals/healthy
```

It should return a different fake meal suggestion.

Example response:

```json
{
  "title": "Chicken Salad",
  "description": "A lighter meal using chicken and vegetables.",
  "steps": [
    "Grill chicken",
    "Chop vegetables",
    "Mix with dressing"
  ]
}
```

Questions:

```txt
1. Which file did you change?
2. Did you need to create a new DTO?
3. Why or why not?
```

```txt
1. MealController.java and MealService.java
2. No
3. This is because the same DTO works for this endpoint as well
```

---

## Submission checklist

Before you finish, make sure you have:

- answered the SOAP vs REST questions
- translated operations into REST endpoints
- identified Fridge2Meal resources
- created `MealResponse`
- created `MealService`
- created `MealController`
- tested `/api/meals/suggestion` in the browser
- explained the API loop in your own words

---

## Key takeaway

REST is not just about writing URLs.

REST is a way of organising communication between software systems.

The frontend asks for a resource.
The backend responds with JSON.
The API is the boundary between them.
