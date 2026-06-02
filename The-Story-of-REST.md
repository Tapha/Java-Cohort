# The Story of REST: From SOAP to REST

## The old castle and the open road

Before modern apps talked through simple APIs, many systems used **SOAP**.

SOAP was like sending a formal letter to a government office.

The message had to be wrapped correctly.  
The structure had to be exact.  
The contract mattered.

REST arrived later with a different attitude:

> Instead of wrapping every request in a heavy envelope, use the web itself.

URLs.  
HTTP methods.  
JSON.  
Clear resources.

That is the story we are learning today.

---

## 1. The world before REST

SOAP stands for:

```txt
Simple Object Access Protocol
```

The name says “simple”, but SOAP could feel quite heavy in real projects.

A SOAP request often looked like this:

```xml
<soap:Envelope>
  <soap:Body>
    <GetMealSuggestion>
      <ingredients>tomato, pasta, cheese</ingredients>
    </GetMealSuggestion>
  </soap:Body>
</soap:Envelope>
```

Notice the structure.

The request is not just saying:

```txt
Give me a meal suggestion.
```

It is wrapped inside a formal SOAP envelope.

This was useful for large enterprise systems, especially where strict contracts mattered:

- banks
- insurance companies
- telecoms
- government systems
- internal corporate platforms

SOAP was not useless. It solved real problems.

But it came from a heavier world.

---

## 2. Then the web changed everything

The web became bigger, faster, and more app-driven.

Now systems needed to talk across:

- websites
- mobile apps
- JavaScript frontends
- backend APIs
- cloud services
- startup products
- public APIs

Developers needed a simpler way for systems to communicate.

They did not always need a strict XML envelope.

They needed something that matched how the web already worked:

```txt
URL + HTTP method + data
```

That is where REST became powerful.

---

## 3. The core REST shift

REST stands for:

```txt
Representational State Transfer
```

You do not need to memorise the full phrase yet.

For now, understand it like this:

> REST is a way for software systems to communicate using web addresses, HTTP methods, and usually JSON.

REST asks a different question from SOAP.

SOAP often asks:

```txt
What operation should I call?
```

REST asks:

```txt
What resource am I working with?
```

That is the main idea.

---

## 4. SOAP thinks in operations

SOAP often feels like calling named functions on another system.

Examples:

```txt
GetMealSuggestion()
CreateCustomer()
CheckAccountBalance()
SubmitInsuranceClaim()
```

This is operation-first thinking.

The system exposes actions.

In Java terms, SOAP can feel close to this:

```java
mealService.getMealSuggestion();
```

That is not wrong.

But over a network, it can become heavy because every call needs formal structure, contracts, and message wrapping.

---

## 5. REST thinks in resources

REST thinks in things.

A resource is a thing your application cares about.

In Fridge2Meal, resources might include:

```txt
/meals
/ingredients
/images
/recipes
```

Then HTTP methods describe what you want to do with those resources.

```http
GET /api/meals
POST /api/meals
GET /api/meals/42
DELETE /api/meals/42
```

Same resource family.  
Different actions.

This is the key contrast:

```txt
SOAP = operation-first
REST = resource-first
```

---

## 6. The restaurant analogy

Imagine you are ordering food.

SOAP is like sending a formal written request:

```txt
Dear KitchenService,
Please execute PrepareMealSuggestionOperation
with the following ingredients...
```

REST is more like using a clear menu:

```txt
POST /orders
GET /orders/123
DELETE /orders/123
```

The URL tells us the thing.

The HTTP method tells us the action.

The body carries extra data.

The response tells us what happened.

---

## 7. Why JSON helped REST become common

SOAP commonly uses XML.

Example XML:

```xml
<meal>
  <title>Tomato Pasta</title>
  <description>A simple pasta meal</description>
</meal>
```

REST APIs commonly use JSON.

Example JSON:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal"
}
```

JSON became popular because it is:

- shorter
- easier to read
- natural for JavaScript
- easy for mobile apps
- easy for frontend/backend communication

This matters because many modern apps look like this:

```txt
React Native frontend -> REST API -> Java backend -> database or AI service
```

JSON became the common language between those parts.

---

## 8. What REST gives us as developers

REST gives us a clean boundary.

The frontend does not need to know how the backend works internally.

In Fridge2Meal, the mobile app does not need to know:

- which AI model is being used
- how the prompt is created
- whether there is a database
- how the Java service is structured
- whether the backend calls OpenAI, Claude, or another service

The frontend only needs a clear endpoint.

Example:

```http
GET /api/meals/suggestion
```

And a clear JSON response:

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

That is REST doing its job.

It hides internal complexity behind a simple communication boundary.

---

## 9. REST in our Spring Boot project

When we write this:

```java
@RestController
@RequestMapping("/api/meals")
public class MealController {

    @GetMapping("/suggestion")
    public MealResponse getSuggestion() {
        return mealService.generateMeal();
    }
}
```

We are creating a REST endpoint.

The browser or frontend can call:

```http
GET /api/meals/suggestion
```

Spring Boot then turns our Java object into JSON.

That means this Java record:

```java
public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
```

Can become this JSON:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal using tomatoes and pasta.",
  "steps": ["Boil pasta", "Cook tomatoes", "Mix together"]
}
```

This is one of the most important backend ideas:

```txt
Java object -> JSON response -> frontend display
```

---

## 10. The first API loop

Right now, our goal is not to build the full Fridge2Meal app.

Our goal is to understand the first API loop:

```txt
Frontend or browser
    -> HTTP request
        -> Controller
            -> Service
                -> DTO
                    -> JSON response
```

In words:

1. The frontend asks for something.
2. The backend receives the request.
3. The controller handles the route.
4. The service creates the result.
5. The DTO shapes the response.
6. JSON travels back to the frontend.

Once you understand this loop, backend development becomes much less mysterious.

---

## 11. SOAP vs REST summary

| Idea | SOAP | REST |
|---|---|---|
| Main style | Operation-first | Resource-first |
| Common format | XML | JSON |
| Message shape | Envelope-based | HTTP-native |
| Typical feel | Formal contract | Web conversation |
| Common use | Enterprise/legacy systems | Web/mobile APIs |
| Beginner mental model | Calling remote operations | Interacting with resources |

---

## 12. Final picture

SOAP came from the formal enterprise world.

REST came from the web.

SOAP says:

```txt
Call this operation using this contract.
```

REST says:

```txt
Here is a resource. Use an HTTP method to interact with it.
```

For Fridge2Meal, REST is the right model because our app is a modern frontend/backend system:

```txt
React Native app -> REST API -> Spring Boot backend -> future AI/database layer
```

You are not just learning annotations.

You are learning how modern software systems talk.

---

## Key takeaway

REST is not just a technology.

It is a way of thinking about software boundaries.

The frontend asks.  
The backend responds.  
The contract between them is the API.

That is the foundation we are building on.
