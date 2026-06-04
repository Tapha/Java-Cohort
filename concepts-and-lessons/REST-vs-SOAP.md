# REST vs SOAP: Understanding API Communication

## Core idea

REST and SOAP are both ways for systems to talk to each other.

The difference is the style of conversation:

- **REST** is usually simple, resource-based, and web-native.
- **SOAP** is formal, contract-heavy, and envelope-based.

---

## 1. Why APIs exist

An API is a controlled doorway into a system.

Instead of one program reaching directly into another program’s memory or database, it sends a request and receives a response.

In Fridge2Meal, the frontend does not generate meals by itself. It asks the backend for a meal suggestion. The backend decides what to do and returns data.

```txt
Frontend → HTTP request → Backend → JSON response → Frontend displays result
```

---

## 2. What REST is

REST stands for **Representational State Transfer**.

In day-to-day backend development, REST usually means building web APIs around **resources** using normal HTTP methods.

A resource is a thing the API works with:

- meal
- user
- product
- booking
- order

A URL points to the resource. An HTTP method describes the action. The response is commonly JSON.

### REST example

```http
GET /api/meals/suggestion
```

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal using tomatoes and pasta.",
  "steps": ["Boil pasta", "Cook tomatoes", "Mix together"]
}
```

### REST mental model

REST feels like asking for or changing a named thing.

```txt
GET    /api/meals          → get meals
GET    /api/meals/12       → get meal 12
POST   /api/meals          → create a meal
PUT    /api/meals/12       → replace meal 12
DELETE /api/meals/12       → delete meal 12
```

---

## 3. What SOAP is

SOAP stands for **Simple Object Access Protocol**.

SOAP is older and more formal than the REST style most students will meet first. It usually sends XML messages inside a structured envelope.

SOAP is not just “send me this resource.” It is more like:

> “Here is a formal message, wrapped in a standard envelope, following a contract, asking a named operation to run.”

### SOAP-style example

```xml
<soap:Envelope>
  <soap:Body>
    <GetMealSuggestion>
      <ingredient>tomatoes</ingredient>
      <ingredient>pasta</ingredient>
    </GetMealSuggestion>
  </soap:Body>
</soap:Envelope>
```

### SOAP mental model

- SOAP is operation-based: call this operation with this structured message.
- SOAP usually uses XML.
- SOAP commonly depends on a formal contract called WSDL.
- SOAP can include built-in standards for security, transactions, and reliability.

---

## 4. The key contrast

| Question | REST | SOAP |
|---|---|---|
| Main style | Resource-based | Operation/message-based |
| Common data format | JSON | XML |
| Feels like | Using the web naturally | Submitting a formal document |
| Contract | Often lighter / OpenAPI optional | WSDL contract is central |
| Typical use today | Web apps, mobile apps, public APIs | Enterprise, banking, legacy systems |
| Learning curve | Usually easier for beginners | More formal and verbose |

---

## 5. Why we teach REST first

We teach REST first because it connects directly to how modern web and mobile apps work. It also maps cleanly onto Spring Boot annotations.

- `@RestController` means this class returns API responses, not HTML pages.
- `@GetMapping` means this method responds to an HTTP GET request.
- A DTO is the object shape we send back to the frontend.
- Spring Boot automatically converts Java objects into JSON.

### Spring Boot example

```java
@RestController
@RequestMapping("/api/meals")
public class MealController {

    @GetMapping("/suggestion")
    public MealResponse getSuggestion() {
        return new MealResponse(
            "Tomato Pasta",
            "A simple pasta meal using tomatoes and pasta.",
            List.of("Boil pasta", "Cook tomatoes", "Mix together")
        );
    }
}
```

---

## 6. The point we are trying to learn

REST is not just syntax.

REST teaches students to think in:

- resources
- boundaries
- requests
- responses
- system-to-system communication

That is the important shift.

A beginner may think a backend is just Java code. But once we introduce REST, they start seeing the backend as a service with doors.

Each endpoint is a doorway. The frontend knocks on the doorway. The backend answers with data.

---

## 7. Fridge2Meal mapping

| Fridge2Meal concept | REST version |
|---|---|
| User wants a meal suggestion | `GET /api/meals/suggestion` |
| Frontend sends fridge image later | `POST /api/meals/from-image` |
| Backend returns meal data | JSON response |
| Frontend displays result | `MealResultScreen` reads JSON |
| Errors mean re-shoot or retry | HTTP status code + error message |

---

## 8. Common beginner confusion

### “Is REST a programming language?”

No. REST is an architectural style. You can build REST APIs in Java, Python, JavaScript, C#, PHP, Go, and many other languages.

### “Is JSON the same as REST?”

No. JSON is a data format. REST is the style of API. REST APIs often use JSON, but they do not have to.

### “Is SOAP bad?”

No. SOAP is not bad. It is just heavier and more formal. Many serious enterprise systems still use SOAP because formal contracts, reliability, and security standards matter there.

### “Which one are we using?”

For Fridge2Meal, we are using REST with JSON because it is simpler, common in modern apps, and fits Spring Boot very well.

---

## 9. Student checkpoint

By the end of this lesson, you should be able to explain:

- What an API is.
- What REST means in practical Spring Boot terms.
- Why REST commonly uses URLs, HTTP methods, and JSON.
- How SOAP differs from REST.
- Why Fridge2Meal starts with a REST endpoint instead of SOAP.

---

## 10. One-sentence summary

**REST is like asking the web for a resource. SOAP is like sending a formal XML letter asking a system to perform an operation.**

Next step: build and test the first Spring Boot JSON endpoint.
