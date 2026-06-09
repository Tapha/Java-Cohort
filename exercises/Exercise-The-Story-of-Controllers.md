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

Service = business logic

Response = data leaving the backend
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
1. Thats where it recieved the requesr from the outside the backend
2. HTTP request
3. JSON response
4. The service is where the business logic is dealt
5. A controller truns a web request into Java work 
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

1. Controller
2. SERVICE
3. Controller
4. This is where the outside information request enters the java backend
5. It can become large and mix request up
---

# 🌐 Part 3 — HTTP Requests

For each request, explain what the client is asking for.

| HTTP Request | What is being asked? |
|---|---|
| `GET /api/meals` | Gets all the meals |
| `GET /api/meals/5` | Gets the meals with id 5 |
| `POST /api/meals/suggestion` | Submits the ingredients to create a meal suggestion  |
| `DELETE /api/meals/5` | deleteds the meal id 5 |
| `GET /api/meals?type=vegetarian` | Gets meals filtered by the veg type |

## Reflection

Complete:

```text
An HTTP request is a message from the outside the backend
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
1. the base route - /api/meals
2. @Getmapping
3. getMeals()
4. List<MealResponse>
5. GET /api/meals -> MealCobtroler.getMeals()
---

# 🧾 Part 5 — HTTP Methods

Match the HTTP method to its usual meaning.

| HTTP Method | Meaning |
|---|---|
| GET | reads the data |
| POST | creates the data  |
| PUT | replace/update data |
| PATCH | partially updates the data |
| DELETE | remove data|

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

1. GET /api/meals reads meal data but the POST /api/meals submits meal data
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
Spring __________ JSON
        ↓
MealRequest object in memory
        ↓
Controller method receives __________
```
1.  {
  "ingredients": ["tomato", "pasta", "cheese"]
}
2.  MealRequest
3.  takes the JSON body from the HTTP request and turns into a Java object
4.  MealRequest
5.  JSON request body
        ↓
Spring deserializes JSON
        ↓
MealRequest object in memory
        ↓
Controller method receives request
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
1. MealResponse
2. JSON
3. MealResponse Object
4. MealResponse object in memory
        ↓
Spring serializes object
        ↓
JSON response body
        ↓
HTTP response leaves backend
---

# 📦 Part 8 — DTOs

DTO means:

```text
Data Transfer Object
```

Fill in the table.

| DTO | Direction | Purpose |
|---|---|---|
| `MealRequest` | input / output? | input |
| `MealResponse` | input / output? | output |

## Questions

1. Why is `MealRequest` an input shape?
2. Why is `MealResponse` an output shape?
3. Why should we avoid exposing database entities directly through the API?
4. Complete:

```text
DTO = __________ shape

Entity = __________ shape
```
1. This is where it recieves data from the request
2. this is where it returns back to the frontend
3. For security reason to protect the system
4. DTO = boundary shape
   Entity = storage shape
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
Controller should recieve the request and call the service
Service should perform the business logic
Repository should access or save data 
```
1. validation, recipe decisions, nutirition calc, saving history, sending notifications and building the response
2. because the controlle is too large and has many jobs to do
3. the validation, deciding recipe, calc nutrition, sending notification, sending notification and building response -> to service
4. saving history
5. Single resposibility principle 
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
1. MealService
2. Recieves teh request and calls the service
3. Works with ther business work
4. each layer has one job so the controller does not become too large
5. Controller = boundary coordinator
   Service = business coordinator
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
1. MealService
2. NO
3. I think it is because spring provides it and the controler stars easier to update or test it
4. depeendency inversion priniciple
5. Dependency injection means dependencies are provided to a class instead of created inside it
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
1.  5
2.  @PathVariable
3.  Long ID
4.  getMealById(id)
5.  GET /api/meals/5
        ↓
   id = 5
        ↓
   getMealById(5)
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

Query parameter = what filter or option?
```
1. type
2. vegetarian
3. @RequestParam
4. A path variable idenfities which resources and the queiry parameter gives options 
---

# ⚠️ Part 14 — Validation at the Boundary

Outside data cannot be trusted.

For a `MealRequest`, decide whether each input should be valid or invalid.

| Request Body | Valid / Invalid? | Why? |
|---|---|---|
| `{ "ingredients": ["tomato"] }` | Valid | it has the ingredient name |
| `{ "ingredients": [] }` | invalid | ingreident should never be empty |
| `{ "ingredients": null }` | invalid| the ingreident cant be null |
| `{}` | invalid | the field is empty |
| invalid JSON text | invalid | its a invalid JSON |

## Questions

1. Why should we validate request input?
2. Where does untrusted data first enter the Java backend?
3. What should happen if input is invalid?
4. Complete:

```text
The controller boundary is where untrusted input first becomes...
```
1. For security as outside data shouldnt be trusted
2. the controller boundary
3. API should return a error response
4. The controller boundary is where untrusted input first becomes data
---

# 🧨 Part 15 — Controller Failure Paths

Match each failure to a likely HTTP status code.

| Failure | Status Code |
|---|---|
| invalid JSON | 400 bad request |
| missing required field | 400 bad request |
| meal not found | 404 not found |
| duplicate meal already exists | 409 conflict  |
| user not logged in | 401 unauthorised |
| user logged in but not allowed | 403 forbidden  |
| unexpected server error | 500 intenral server error |

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

1. The frontend and the whole system should know and display what the error was
2. some errros may be caused by a bad input, data missing or permission issues
3. the frontend may be able to handle the problem more efficiently 
---

# 🧾 Part 16 — Status Codes

Fill in the table.

| Status Code | Meaning |
|---|---|
| 200 OK | working  |
| 201 Created | new created |
| 204 No Content | worked but nothing sent back |
| 400 Bad Request | the request was bad or missing data |
| 401 Unauthorized | user needs to log in  |
| 403 Forbidden | the user is logged in but not allowed |
| 404 Not Found | the thing requested was not found |
| 409 Conflict | there is a conflict such as user already exists |
| 500 Internal Server Error | something wrong in the server |

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
5. Complete:
4. When might `ResponseEntity` be useful?

```text
ResponseEntity = full HTTP __________ wrapper
```
1. control the status of the code, the response and the headers
2. 201 created
3. reponse
4. if we want to control the HTTP response more clearly like 201 created
5. ResponseEntity = full HTTP response wrapper
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
Request body/path/query data becomes values
        ↓
Controller calls service
        ↓
Service performs busniess logic
        ↓
Controller receives result
        ↓
Java result becomes JSON request
        ↓
HTTP response leaves backend
```

## Questions

1. Where does deserialization happen?
2. Where does business logic happen?
3. Where does serialization happen?
4. Where does output happen?

1. when spring turns JSON into data
2. service
3. spring turns java results into JSON
4. when the HTTP response leaves the backend 
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

ANSWER:

import java.util.List;

public record MealRequest(
    List<String> ingredients
) {}
import java.util.List;

public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
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
| SRP | controller handles HTTP boundary, not all business logic |
| OCP | new endpoints can be added without breaking existing ones |
| LSP | controller contracts should behave consistently |
| ISP | clients should receive focused DTOs |
| DIP | controllers depend on services, not low-level details |

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
Memory gives Java a working space.

Objects give memory shape.

Collections organize many  objects.

ORM maps objects to database  rows.

I/O moves data in and out.

REST structures web  communication.

Controllers receive HTTP  input.

DTOs shape boundary  data.

Services perform business  work.

Repositories access data storage.

Exceptions handle failure paths.

Logging makes runtime visible.
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
1. HTTP boundary that receives requests and returns response
2. where outside data enters the backend
3. External address for an endpoint
4. Request body is data sent in the request, path variable identifies a specific resource, query parameter gives filters or options
5. Data Transfer Object to shape data going in or out of the API
6. So business logic stays in the service and the controller does not do too many jobs
7. Controller handles HTTP requests and responses but the service handles business logic
8. tell the frontend what happened
9. Controls the status code, response body and headers
10. It means the controller takes an HTTP request from outside the backend and turns it into Java method or service work
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

1. StudentController
2. /api/students
3.                 GET /api/students: GET
                   GET /api/students/{id} : GET
                   POST /api/students : POST
                   GET /api/students?course=java : GET
                   DELETE /api/students/{id} : DELETE
4. /api/students/{id}
5. /api/students?course=java
6.
   public record StudentRequest(
    String name,
    String course
) {}

7.  public record StudentResponse(
    Long id,
    String name,
    String course

) {}

8. GET /api/students
   GET /api/students?course=java

9. GET /api/students/{id}
   POST /api/students 

10. getStudents()
    getStudentById(id)
    createStudent(request)
    getStudentsByCourse(course)
    deleteStudent(id)
--

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
