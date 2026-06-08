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
Controller = HTTP boundary

Route = external address

DTO = boundary shape

Service = business use case

Response = data leaving the system
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
## Answers
1. A controller is like a front door because it receives the requests from the front end and welcomes them in through accepting the request. It will then process the request and use services then send the data back as a response from the same doorway. It is the entry and exit point of data in the backend. 
2. The backend receives a request as JSON from the frontend.
3. The controller returns the response as JSON to the frontend.
4. The controller should only coordinte and manage the requests, it should hand off business logic to services and this allows it to comply with SOLID.
5. A controller turns a web request into a Java object, passes to services and returns a response. 
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

## Answers
1. Backend receives request.
2. Service performs work.
3. Response returns to frontend.
4. A controller is a boundary because it sits at the edge of the backend and it receives requests and returns responses.
5. If a controller tried to do everything, the code would be confusing to maintain and data could leak into the API. 
---

# 🌐 Part 3 — HTTP Requests

For each request, explain what the client is asking for.

| HTTP Request | What is being asked? |
|---|---|
| `GET /api/meals` | retrieve all meals |
| `GET /api/meals/5` | retrieve meal 5 |
| `POST /api/meals/suggestion` | create meal suggestion |
| `DELETE /api/meals/5` | remove meal 5 |
| `GET /api/meals?type=vegetarian` | retrieve meal where the type is vegetarian |

## Reflection

Complete:

```text
An HTTP request is a message from the outside to the backend. 
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
MealController.getMeals()
```
## Answers
1. /api/meals
2. @GetMapping
3. mealService.getMeals();
4. A List of MealResponses (List<MealResponse>)
---

# 🧾 Part 5 — HTTP Methods

Match the HTTP method to its usual meaning.

| HTTP Method | Meaning |
|---|---|
| GET | read data |
| POST | create or submit data |
| PUT | replace/update data |
| PATCH | partially update data |
| DELETE | remove data |

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

## Answers
1. GET reads and retrieves the data whereas POST will create or submit data for /api/meals.
2. DELETE
3. POST
4. GET
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
Spring deserializes JSON
        ↓
MealRequest object in memory
        ↓
Controller method receives object
```
## Answers
1. "ingredients": ["tomato", "pasta", "cheese"]
2. MealResponse
3. It will take the JSON and turn it into a Java object which can be used.
4. MealRequest
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
Spring serializes object
        ↓
JSON response body
        ↓
HTTP response leaves backend
```
## Answers
1. MealResponse
2. JSON
3. Spring will serialize the MealResponse object into JSON.
---

# 📦 Part 8 — DTOs

DTO means:

```text
Data Transfer Object
```

Fill in the table.

| DTO | Direction | Purpose |
|---|---|---|
| `MealRequest` | input | Ensuring request complies with this format |
| `MealResponse` |output | Ensure the format of the response is this |

## Questions

1. Why is `MealRequest` an input shape?
2. Why is `MealResponse` an output shape?
3. Why should we avoid exposing database entities directly through the API?
4. Complete:

```text
DTO = boundary shape

Entity = storage shape
```
## Answers
1. Because an input is a request into the backend so MealRequest is the shape of the request data.
2. Because an output from the backend is the response, the MealResponse will the the correct shape of the output.
3. We don't want our frontend accessing the entities directly which would cause issues with data integrity. 
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
Controller should receive the request and return the response. 
Service should handle the business logic.
Repository should handle the access to data. 
```
## Answers
1. Handling the request and response, handling the business logic and handling respository.
2. This is dangerous because the controller has too many responsibilities, which is against SOLID principles, particularly single responsibility principle.
3. The business logic parts like validate ingredients, generate recipe, etc.
4. Save history.
5. Single Responsibility Principle. 
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
Service = logic coordinator
```
## Answers
1. MealService
2. Pass the request object to the service to carry out the logic and then handle the response when returned back from the service.
3. The service generates the meal based on the request, it does the main logic.
4. It separates the responsibilities and makes the code easier to maintain. 
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
Dependency injection means dependencies are passed into the constructor. 
```
## Answers
1. A MealService.
2. No it automatically instantiates the MealService object without new MealService.
3. Spring handles the creation and it makes the code better for maintainability and extension.
4. Dependency Inversion Principle (DIP).
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
id = 5
        ↓
getMealById(5)
```
## Answers
1. 5
2. @GetMapping("/{id}") 
3. @PathVariable Long id
4. mealService.getMealById(id);
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
Path variable = which resource?

Query parameter = what filter or options?
```
## Answers 
1. ?type=vegetarian
2. vegetarian
3. @RequestParam String type
4. A path variable is specific for a particular resources whereas a query parameter is for filtering results or for a search term. 
---

# ⚠️ Part 14 — Validation at the Boundary

Outside data cannot be trusted.

For a `MealRequest`, decide whether each input should be valid or invalid.

| Request Body | Valid / Invalid? | Why? |
|---|---|---|
| `{ "ingredients": ["tomato"] }` | valid | contains a string array of ingredients |
| `{ "ingredients": [] }` | valid | contains an empty array |
| `{ "ingredients": null }` | invalid | it requires an array, can't be null |
| `{}` | invalid | empty request, needs an array |
| invalid JSON text | invalid | invalid JSON is not valid |

## Questions

1. Why should we validate request input?
2. Where does untrusted data first enter the Java backend?
3. What should happen if input is invalid?
4. Complete:

```text
The controller boundary is where untrusted input first becomes trusted using a DTO.
```
1. To ensure that we are getting the correct data for the given request and prevents errors.
2. Untrusted data first enters the backend through the request.
3. If the input is valid, Spring will deserialize into a Java object. 
---

# 🧨 Part 15 — Controller Failure Paths

Match each failure to a likely HTTP status code.

| Failure | Status Code |
|---|---|
| invalid JSON | 400 |
| missing required field | 400 |
| meal not found | 404 |
| duplicate meal already exists | 409 |
| user not logged in | 401 |
| user logged in but not allowed | 403 |
| unexpected server error | 500 |

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

## Answers
1. To ensure the correct data is sent and received and if not failure takes place.
2. It gives no idea of what the actual problem is, so other codes should be used to give the user an idea of how to fix the problem.
3. To tell the user where the error went wrong and how to fix the error, error messages should be useful. 
---

# 🧾 Part 16 — Status Codes

Fill in the table.

| Status Code | Meaning |
|---|---|
| 200 OK | request succeeded |
| 201 Created | new resource created |
| 204 No Content | success with no body |
| 400 Bad Request | client send invalid data |
| 401 Unauthorized | authentication required |
| 403 Forbidden | not allowed |
| 404 Not Found | resource not found |
| 409 Conflict | conflict with current state |
| 500 Internal Server Error | unexpected server failure |

## Reflection

Complete:

```text
Response body = detail

Status code = outcome signal
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
ResponseEntity = full HTTP response wrapper
```
## Answers
1. It allows us to control access to the entity.
2. 201
3. The response from mealService.createMeal(request)
4. When controlling access to the database through requests. 
---

# 🔄 Part 18 — Full Controller Flow

Complete the flow:

```text
Frontend sends HTTP request
        ↓
Spring matches route
        ↓
Controller method runs
        ↓
Request body/path/query data becomes Java values
        ↓
Controller calls service
        ↓
Service performs business use case
        ↓
Controller receives result
        ↓
Java result becomes JSON
        ↓
HTTP response leaves backend
```

## Questions

1. Where does deserialization happen?
2. Where does business logic happen?
3. Where does serialization happen?
4. Where does output happen?

## Answers
1. Request body/path/query data becomes Java values
2. Service performs business use case
3. Java result becomes JSON
4. HTTP response leaves backend
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

## Answers 
1. public record MealRequest(
    List<String> ingredients
) {}
2. public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
3. 4. 5. 6. 7. 8.
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
## Reflection

Explain the full flow:

```text
JSON ingredients
        ↓
MealRequest DTO
        ↓
MealController
        ↓
MealService
        ↓
MealResponse
        ↓
JSON response
```

---

# 🧠 Part 20 — Controllers and SOLID

Fill in the table.

| SOLID Principle | Controller Meaning |
|---|---|
| SRP |  |
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
