# 🚦 Exercise: The Story of Controllers — How Requests Become Java Work 🌐☕️

## Goal

By the end of this exercise, you should be able to explain how an HTTP request becomes Java work:

```text
HTTP request
        ↓
Controller
        ↓
DTO / Java values
        ↓
Service
        ↓
Result
        ↓
HTTP response
```

You are not just learning annotations.

You are learning how the outside world enters your Java application.

---

# 🧠 Part 1 — The Big Picture

Complete the missing words:

```text
Controller = HTTP __________

Route = external __________

DTO = boundary __________

Service = business __________

Response = data leaving the __________
```

## Questions

1. Why is a controller like the front door of a backend?
2. What does a controller receive?
3. What does a controller return?
4. Why should a controller not contain all business logic?
5. Complete this sentence:

```text
A controller turns a web request into...
```

---

# 🚪 Part 2 — Controller as Doorway

Look at this flow:

```text
Frontend sends request
        ↓
Backend receives request
        ↓
Controller method runs
        ↓
Service performs work
        ↓
Response returns to frontend
```

## Questions

1. Which layer receives the HTTP request?
2. Which layer should perform the main business work?
3. Which layer sends the HTTP response back?
4. Why is the controller a boundary?
5. What could go wrong if the controller tries to do everything?

---

# 🌐 Part 3 — HTTP Requests

For each request, explain what the client is asking for.

| HTTP Request | What is being asked? |
|---|---|
| `GET /api/meals` | ? |
| `GET /api/meals/5` | ? |
| `POST /api/meals/suggestion` | ? |
| `DELETE /api/meals/5` | ? |
| `GET /api/meals?type=vegetarian` | ? |

## Reflection

Complete:

```text
An HTTP request is a message from...
```

---

# 🗺️ Part 4 — Routes to Java Methods

Look at this controller:

```java
@RestController
@RequestMapping("/api/meals")
public class MealController {

    @GetMapping
    public List<MealResponse> getMeals() {
        return mealService.getMeals();
    }
}
```

## Questions

1. What is the base route for this controller?
2. Which annotation maps the `GET` request?
3. What Java method handles `GET /api/meals`?
4. What does the method return?
5. Complete the mapping:

```text
GET /api/meals
        ↓
?
```

---

# 🧾 Part 5 — HTTP Methods

Match the HTTP method to its usual meaning.

| HTTP Method | Meaning |
|---|---|
| GET | ? |
| POST | ? |
| PUT | ? |
| PATCH | ? |
| DELETE | ? |

Use these meanings:

```text
read data
create or submit data
replace/update data
partially update data
remove data
```

## Questions

1. Why is `GET /api/meals` different from `POST /api/meals`?
2. Which method would you use to delete a meal?
3. Which method would you use to submit ingredients for a suggestion?
4. Which method would you use to retrieve one meal?

---

# 📥 Part 6 — Request Body to Java Object

Frontend sends JSON:

```json
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```

Java DTO:

```java
public record MealRequest(
    List<String> ingredients
) {}
```

Controller method:

```java
@PostMapping("/suggestion")
public MealResponse suggestMeal(@RequestBody MealRequest request) {
    return mealService.generateMeal(request);
}
```

## Questions

1. What is the request body?
2. Which Java object receives the JSON shape?
3. What does `@RequestBody` mean conceptually?
4. What type is the `request` parameter?
5. Complete the flow:

```text
JSON request body
        ↓
Spring __________ JSON
        ↓
MealRequest object in memory
        ↓
Controller method receives __________
```

---

# 📤 Part 7 — Java Object to Response Body

Look at this response DTO:

```java
public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
```

And this controller method:

```java
@PostMapping("/suggestion")
public MealResponse suggestMeal(@RequestBody MealRequest request) {
    return mealService.generateMeal(request);
}
```

## Questions

1. What Java object does the controller return?
2. What format does the frontend usually receive?
3. What does Spring serialize?
4. Complete the flow:

```text
MealResponse object in memory
        ↓
Spring __________ object
        ↓
JSON response body
        ↓
HTTP response leaves backend
```

---

# 📦 Part 8 — DTOs

DTO means:

```text
Data Transfer Object
```

Fill in the table.

| DTO | Direction | Purpose |
|---|---|---|
| `MealRequest` | input / output? | ? |
| `MealResponse` | input / output? | ? |

## Questions

1. Why is `MealRequest` an input shape?
2. Why is `MealResponse` an output shape?
3. Why should we avoid exposing database entities directly through the API?
4. Complete:

```text
DTO = __________ shape

Entity = __________ shape
```

---

# 🧠 Part 9 — Controller vs Service

Look at this bad controller:

```java
@RestController
public class MealController {

    @PostMapping("/suggestion")
    public MealResponse suggestMeal(@RequestBody MealRequest request) {
        // validate ingredients
        // decide recipe
        // calculate nutrition
        // save history
        // send notification
        // build response
    }
}
```

## Questions

1. What responsibilities are mixed into the controller?
2. Why is this design dangerous?
3. Which parts should move into a service?
4. Which parts might belong to a repository?
5. Which SOLID principle is being violated?

## Refactor idea

Complete:

```text
Controller should...
Service should...
Repository should...
```

---

# 🧱 Part 10 — Thin Controller

A good controller is usually thin.

Look at this design:

```java
@RestController
@RequestMapping("/api/meals")
public class MealController {

    private final MealService mealService;

    public MealController(MealService mealService) {
        this.mealService = mealService;
    }

    @PostMapping("/suggestion")
    public MealResponse suggestMeal(@RequestBody MealRequest request) {
        return mealService.generateMeal(request);
    }
}
```

## Questions

1. What dependency does the controller have?
2. What does the controller do?
3. What does the service do?
4. Why is this cleaner than putting everything in the controller?
5. Complete:

```text
Controller = boundary coordinator
Service = __________ coordinator
```

---

# 🔌 Part 11 — Dependency Injection

Look at this constructor:

```java
public MealController(MealService mealService) {
    this.mealService = mealService;
}
```

## Questions

1. What dependency does the controller receive?
2. Does the controller create `MealService` manually?
3. Why is receiving the dependency better than creating it inside the controller?
4. Which SOLID principle does this connect to?
5. Complete:

```text
Dependency injection means dependencies are...
```

---

# 🧭 Part 12 — Path Variables

Request:

```http
GET /api/meals/5
```

Controller method:

```java
@GetMapping("/{id}")
public MealResponse getMealById(@PathVariable Long id) {
    return mealService.getMealById(id);
}
```

## Questions

1. What value is inside the URL path?
2. Which annotation extracts it?
3. What Java parameter receives it?
4. What service method is called?
5. Complete the flow:

```text
GET /api/meals/5
        ↓
id = __________
        ↓
getMealById(__________)
```

---

# 🔎 Part 13 — Query Parameters

Request:

```http
GET /api/meals?type=vegetarian
```

Controller method:

```java
@GetMapping
public List<MealResponse> getMeals(@RequestParam String type) {
    return mealService.getMealsByType(type);
}
```

## Questions

1. What is the query parameter?
2. What value does it contain?
3. Which annotation extracts it?
4. What is the difference between a path variable and a query parameter?

Complete:

```text
Path variable = which __________?

Query parameter = what __________ or __________?
```

---

# ⚠️ Part 14 — Validation at the Boundary

Outside data cannot be trusted.

For a `MealRequest`, decide whether each input should be valid or invalid.

| Request Body | Valid / Invalid? | Why? |
|---|---|---|
| `{ "ingredients": ["tomato"] }` | ? | ? |
| `{ "ingredients": [] }` | ? | ? |
| `{ "ingredients": null }` | ? | ? |
| `{}` | ? | ? |
| invalid JSON text | ? | ? |

## Questions

1. Why should we validate request input?
2. Where does untrusted data first enter the Java backend?
3. What should happen if input is invalid?
4. Complete:

```text
The controller boundary is where untrusted input first becomes...
```

---

# 🧨 Part 15 — Controller Failure Paths

Match each failure to a likely HTTP status code.

| Failure | Status Code |
|---|---|
| invalid JSON | ? |
| missing required field | ? |
| meal not found | ? |
| duplicate meal already exists | ? |
| user not logged in | ? |
| user logged in but not allowed | ? |
| unexpected server error | ? |

Use:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
500 Internal Server Error
```

## Questions

1. Why is failure part of the API contract?
2. Why is `500 Internal Server Error` not the right response for every problem?
3. Why should API errors be meaningful?

---

# 🧾 Part 16 — Status Codes

Fill in the table.

| Status Code | Meaning |
|---|---|
| 200 OK | ? |
| 201 Created | ? |
| 204 No Content | ? |
| 400 Bad Request | ? |
| 401 Unauthorized | ? |
| 403 Forbidden | ? |
| 404 Not Found | ? |
| 409 Conflict | ? |
| 500 Internal Server Error | ? |

## Reflection

Complete:

```text
Response body = __________

Status code = __________
```

---

# 🧰 Part 17 — ResponseEntity

Look at this method:

```java
@PostMapping
public ResponseEntity<MealResponse> createMeal(@RequestBody MealRequest request) {
    MealResponse response = mealService.createMeal(request);

    return ResponseEntity.status(201).body(response);
}
```

## Questions

1. What does `ResponseEntity` allow us to control?
2. What status code is being returned?
3. What response body is being returned?
4. When might `ResponseEntity` be useful?
5. Complete:

```text
ResponseEntity = full HTTP __________ wrapper
```

---

# 🔄 Part 18 — Full Controller Flow

Complete the flow:

```text
Frontend sends HTTP request
        ↓
Spring matches __________
        ↓
Controller method __________
        ↓
Request body/path/query data becomes __________
        ↓
Controller calls __________
        ↓
Service performs __________
        ↓
Controller receives __________
        ↓
Java result becomes __________
        ↓
HTTP response leaves backend
```

## Questions

1. Where does deserialization happen?
2. Where does business logic happen?
3. Where does serialization happen?
4. Where does output happen?

---

# 🍅 Part 19 — Fridge2Meal Controller

Request:

```http
POST /api/meals/suggestion
```

JSON body:

```json
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```

## Tasks

1. Create a `MealRequest` DTO.
2. Create a `MealResponse` DTO.
3. Write a controller class outline.
4. Add a base route of `/api/meals`.
5. Add a method for `/suggestion`.
6. Make the method accept a request body.
7. Make the method call `mealService.generateMeal(request)`.
8. Make the method return `MealResponse`.

## Reflection

Explain the full flow:

```text
JSON ingredients
        ↓
?
        ↓
MealController
        ↓
?
        ↓
MealResponse
        ↓
?
```

---

# 🧠 Part 20 — Controllers and SOLID

Fill in the table.

| SOLID Principle | Controller Meaning |
|---|---|
| SRP | ? |
| OCP | ? |
| LSP | ? |
| ISP | ? |
| DIP | ? |

Use these ideas:

```text
controller handles HTTP boundary, not all business logic
new endpoints can be added without breaking existing ones
controller contracts should behave consistently
clients should receive focused DTOs
controllers depend on services, not low-level details
```

---

# 🗺️ Part 21 — Connect Everything So Far

Complete:

```text
Memory gives Java a __________ space.

Objects give memory __________.

Collections organize __________ objects.

ORM maps objects to __________ rows.

I/O moves data __________ and __________.

REST structures __________ communication.

Controllers receive __________ input.

DTOs shape __________ data.

Services perform __________ work.

Repositories access __________.

Exceptions handle __________ paths.

Logging makes runtime __________.
```

---

# 🚀 Final Reflection

Answer in your own words:

1. What is a controller?
2. Why is a controller a boundary?
3. What is a route?
4. What is the difference between request body, path variable, and query parameter?
5. What is a DTO?
6. Why should controllers usually be thin?
7. What is the difference between a controller and a service?
8. Why do status codes matter?
9. What is `ResponseEntity` useful for?
10. Explain this sentence:

```text
A controller is where the outside world becomes Java work.
```

---

# 🌟 Stretch Challenge — Design Your Own Controller

Design a controller for a student management API.

Requirements:

```text
GET /api/students
GET /api/students/{id}
POST /api/students
GET /api/students?course=java
DELETE /api/students/{id}
```

## Tasks

1. Write the controller class name.
2. Write the base route.
3. Identify each HTTP method.
4. Identify where path variables are used.
5. Identify where query parameters are used.
6. Create a `StudentRequest` DTO.
7. Create a `StudentResponse` DTO.
8. Identify which methods should return a list.
9. Identify which methods should return one object.
10. Identify which methods should call `studentService`.

---

# 🧠 Final Compression

```text
Controller = HTTP boundary
Route = external address
Method = Java handler
Request body = input payload
Response body = output payload
DTO = boundary shape
Path variable = resource identity from URL
Query parameter = filter/options from URL
Service = business use case
Repository = persistence boundary
Status code = outcome signal
ResponseEntity = full response control
```

Use this as your map.
