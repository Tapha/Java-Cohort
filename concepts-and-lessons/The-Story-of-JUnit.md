# 🧪 The Story of JUnit — How Java Developers Prove Behaviour ☕️✅

The Fridge2Meal app now has real movement.

A user can take or select a fridge image.

The frontend can send that image to the backend.

The backend can receive it.

The controller can pass it to the service.

The service can call the vision boundary.

The adapter can return meal suggestions.

The frontend can display the result.

That is the loop.

Now a new question appears:

```text
How do we prove this behaviour works
without relying only on clicking around manually?
```

That is where JUnit enters.

JUnit is not just a testing library.

JUnit is the way Java developers turn expected behaviour into executable proof.

```text
JUnit = Java behaviour proof system
```

---

# 🧠 1️⃣ Why Testing Exists

Code can look correct and still be wrong.

The app can run and still behave incorrectly.

A method can compile and still return the wrong result.

A controller can exist and still expose the wrong response.

A service can call the wrong dependency.

A frontend can send data in a shape the backend does not expect.

So the question is:

```text
How do we know the system behaves the way the ticket promised?
```

Testing answers that.

```text
Ticket = promise
Implementation = attempt
Test = proof
```

---

# 🎟️ 2️⃣ Testing Begins With the Ticket

The current ticket is:

```text
Image to Meal Full Loop
```

The ticket promised:

```text
A fridge image should be sent to the backend.

The backend should process the image.

The frontend should receive a meal suggestion.

The response should include:
- meal title
- used ingredients
- steps
- time estimate
```

JUnit helps us prove pieces of that behaviour.

Not by clicking through the whole app every time.

By writing repeatable Java checks.

---

# 🧾 3️⃣ Manual Testing vs Automated Testing

Manual testing means:

```text
A human runs the app and checks behaviour.
```

Example:

```text
Open frontend
Take image
Send request
Check response
Look at backend logs
```

This is useful.

But it is slow.

It depends on a person.

It is easy to forget steps.

Automated testing means:

```text
Code checks the behaviour for us.
```

Example:

```text
Run test
JUnit creates inputs
JUnit calls method
JUnit checks result
JUnit tells us pass or fail
```

Manual testing proves the real loop once.

JUnit lets us prove important parts repeatedly.

---

# 🔬 4️⃣ What JUnit Actually Does

JUnit lets you write Java methods that test other Java methods.

A simple test looks like this:

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @Test
    void add_returnsSumOfTwoNumbers() {
        int result = 2 + 3;

        assertEquals(5, result);
    }
}
```

This test says:

```text
When this behaviour runs,
the result should be 5.
```

If the result is 5, the test passes.

If the result is not 5, the test fails.

JUnit gives Java a way to say:

```text
This behaviour must remain true.
```

---

# 🧱 5️⃣ The Shape of a Test: Arrange, Act, Assert

Most tests follow this structure:

```text
Arrange
Act
Assert
```

## Arrange

Set up the situation.

```text
Create objects.
Create fake data.
Prepare dependencies.
```

## Act

Run the behaviour being tested.

```text
Call the method.
Send the request.
Trigger the action.
```

## Assert

Check the result.

```text
Did we get the expected output?
Was the correct method called?
Was the expected exception thrown?
```

Example:

```java
@Test
void suggestMealFromImage_returnsMealSuggestion() {
    // Arrange
    MealService mealService = new MealService(fakeMealVisionPort);
    MultipartFile image = createFakeImage();

    // Act
    MealResponse response = mealService.suggestMealFromImage(image);

    // Assert
    assertEquals("Tomato Pasta", response.title());
}
```

This pattern is the backbone of testing.

```text
Arrange = prepare world
Act = trigger behaviour
Assert = check truth
```

---

# 🧠 6️⃣ Tests Are About Behaviour, Not Implementation

A weak test checks too much internal detail.

A stronger test checks behaviour.

Bad testing mindset:

```text
Does the method contain this exact line of code?
```

Better testing mindset:

```text
Given this input,
when this behaviour happens,
then this output should appear.
```

For the Image to Meal loop:

```text
Given an image,
when the service processes it,
then a meal response should be returned.
```

That is behaviour.

The exact internal steps can change later.

The behaviour should remain true.

---

# 🔌 7️⃣ Why the Port/Adapter Pattern Helps Testing

The ticket introduced:

```text
MealVisionPort
MealVisionAdapter
```

This matters for testing.

Without a port, `MealService` might depend directly on a real vision system.

That would make testing hard.

The test might need:

```text
real image processing
real external API
network connection
real camera data
```

That is too much for a simple service test.

With a port, the service depends on an abstraction:

```text
MealService
        ↓ depends on
MealVisionPort
```

The real app can use:

```text
MealVisionAdapter
```

The test can use:

```text
FakeMealVisionPort
```

Same service.

Different implementation.

This is the Dependency Inversion Principle.

```text
High-level use case depends on a boundary,
not directly on the external detail.
```

This is why SOLID makes testing easier.

---

# 🧪 8️⃣ Fake vs Mock

When testing, you may hear:

```text
fake
mock
stub
```

For now, understand this simple version.

## Fake

A fake is a simple replacement that behaves in a predictable way.

Example:

```java
MealVisionPort fakePort = image -> new MealResponse(
        "Tomato Pasta",
        List.of("tomato", "pasta", "cheese"),
        List.of("Boil pasta", "Cook tomatoes", "Mix together"),
        20
);
```

The fake does not really analyze an image.

It returns a controlled response.

## Mock

A mock is usually created by a tool like Mockito.

It can be told:

```text
When this method is called,
return this result.
```

Example:

```java
MealService mealService = Mockito.mock(MealService.class);
```

In this course:

```text
Use fakes when you want simple control.

Use mocks when testing controllers or verifying interactions.
```

---

# 🧱 9️⃣ What We Should Test in the Image to Meal Loop

The full feature contains multiple concerns.

We can test it in layers.

| Test Type | What It Proves |
|---|---|
| Service test | MealService can turn image input into MealResponse through MealVisionPort |
| Controller test | Endpoint accepts multipart image and returns JSON response |
| Manual full-loop test | Frontend + backend work together |
| Failure test | Missing/bad image does not silently succeed |

JUnit mainly helps with the backend tests.

Manual testing still helps with the full frontend/backend loop.

---

# 🎯 1️⃣0️⃣ First JUnit Target: MealService

The service is usually the best first test.

Why?

Because the service owns the use case.

For this ticket, the service should coordinate:

```text
Image comes in
        ↓
Service receives image
        ↓
Service calls MealVisionPort
        ↓
MealResponse comes back
        ↓
Service returns MealResponse
```

We want to prove:

```text
Given an image,
MealService returns the expected meal response.
```

---

# 🧪 1️⃣1️⃣ Example MealService Test

This is the shape of the kind of test you will write.

Adjust names to match your actual project.

```java
package com.fridge2meal.service;

import com.fridge2meal.dto.MealResponse;
import com.fridge2meal.port.MealVisionPort;
import org.junit.jupiter.api.Test;
import org.springframework.mock.web.MockMultipartFile;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

class MealServiceTest {

    @Test
    void suggestMealFromImage_returnsMealSuggestion() {
        // Arrange
        MealVisionPort fakePort = image -> new MealResponse(
                "Tomato Pasta",
                List.of("tomato", "pasta", "cheese"),
                List.of("Boil pasta", "Cook tomatoes", "Mix together"),
                20
        );

        MealService mealService = new MealService(fakePort);

        MockMultipartFile image = new MockMultipartFile(
                "image",
                "fridge.jpg",
                "image/jpeg",
                "fake image bytes".getBytes()
        );

        // Act
        MealResponse response = mealService.suggestMealFromImage(image);

        // Assert
        assertEquals("Tomato Pasta", response.title());
        assertTrue(response.usedIngredients().contains("tomato"));
        assertEquals(3, response.usedIngredients().size());
        assertFalse(response.steps().isEmpty());
        assertEquals(20, response.timeEstimateMinutes());
    }
}
```

What this proves:

```text
MealService can use MealVisionPort
and return a valid MealResponse.
```

What this does not prove:

```text
It does not prove the frontend works.
It does not prove axios is correct.
It does not prove the real adapter works.
It does not prove real image recognition works.
```

That is okay.

Each test has a boundary.

---

# 🔎 1️⃣2️⃣ Common JUnit Assertions

Assertions are how tests check truth.

| Assertion | Meaning |
|---|---|
| `assertEquals(expected, actual)` | values should be equal |
| `assertTrue(condition)` | condition should be true |
| `assertFalse(condition)` | condition should be false |
| `assertNotNull(value)` | value should not be null |
| `assertThrows(Exception.class, () -> action)` | action should throw exception |
| `assertAll(...)` | group multiple assertions |

Examples:

```java
assertEquals("Tomato Pasta", response.title());
```

```java
assertTrue(response.usedIngredients().contains("tomato"));
```

```java
assertFalse(response.steps().isEmpty());
```

```java
assertNotNull(response);
```

Assertions are the proof statements.

---

# ⚠️ 1️⃣3️⃣ Testing Failure Cases

A good test suite does not only test the happy path.

Happy path:

```text
Valid image returns meal suggestion.
```

Failure paths:

```text
Missing image
Empty image
Unsupported file type
Vision adapter fails
Response is incomplete
```

Example failure test idea:

```java
@Test
void suggestMealFromImage_whenImageIsEmpty_throwsException() {
    // Arrange
    MealService mealService = new MealService(fakePort);

    MockMultipartFile emptyImage = new MockMultipartFile(
            "image",
            "empty.jpg",
            "image/jpeg",
            new byte[0]
    );

    // Act + Assert
    assertThrows(IllegalArgumentException.class, () -> {
        mealService.suggestMealFromImage(emptyImage);
    });
}
```

This assumes your service has been written to reject empty images.

If your current service does not do this yet, create a follow-up ticket:

```text
Add validation for empty image upload.
```

Testing often reveals the next task.

---

# 🚦 1️⃣4️⃣ Controller Tests: Testing the HTTP Boundary

The controller is the HTTP boundary.

A controller test checks:

```text
Can the endpoint receive the right request?

Does it call the service?

Does it return the expected status and JSON shape?
```

For multipart image upload, a controller test may use:

```text
MockMvc
MockMultipartFile
Mockito
jsonPath
```

The idea:

```text
Pretend to send a multipart request.
Check the HTTP response.
```

Example shape:

```java
mockMvc.perform(multipart("/api/meals/from-image")
        .file(image)
        .contentType(MediaType.MULTIPART_FORM_DATA))
    .andExpect(status().isOk())
    .andExpect(jsonPath("$.title").value("Tomato Pasta"));
```

This proves the controller boundary works.

---

# 🧠 1️⃣5️⃣ Why We Mock the Service in a Controller Test

In a controller test, we usually do not want to test the whole service again.

We want to test the controller.

So we replace the service with a mock.

```text
Controller test:
Controller is real.
Service is controlled/fake.
```

Why?

Because if the test fails, we want to know:

```text
The controller boundary failed.
```

Not:

```text
Maybe the service failed.
Maybe the adapter failed.
Maybe the external vision logic failed.
```

Good tests isolate the thing being tested.

---

# 🧪 1️⃣6️⃣ Example Controller Test Shape

Adjust names to match your project.

```java
package com.fridge2meal.controller;

import com.fridge2meal.dto.MealResponse;
import com.fridge2meal.service.MealService;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockMultipartFile;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.mockito.ArgumentMatchers.any;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.multipart;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

class MealControllerTest {

    private final MealService mealService = Mockito.mock(MealService.class);

    private final MockMvc mockMvc = org.springframework.test.web.servlet.setup.MockMvcBuilders
            .standaloneSetup(new MealController(mealService))
            .build();

    @Test
    void suggestMealFromImage_acceptsMultipartImageAndReturnsMealResponse() throws Exception {
        // Arrange
        Mockito.when(mealService.suggestMealFromImage(any()))
                .thenReturn(new MealResponse(
                        "Tomato Pasta",
                        List.of("tomato", "pasta", "cheese"),
                        List.of("Boil pasta", "Cook tomatoes", "Mix together"),
                        20
                ));

        MockMultipartFile image = new MockMultipartFile(
                "image",
                "fridge.jpg",
                "image/jpeg",
                "fake image bytes".getBytes()
        );

        // Act + Assert
        mockMvc.perform(multipart("/api/meals/from-image")
                        .file(image)
                        .contentType(MediaType.MULTIPART_FORM_DATA))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.title").value("Tomato Pasta"))
                .andExpect(jsonPath("$.usedIngredients[0]").value("tomato"))
                .andExpect(jsonPath("$.steps[0]").value("Boil pasta"))
                .andExpect(jsonPath("$.timeEstimateMinutes").value(20));
    }
}
```

This test proves:

```text
The controller can receive multipart image input
and return the expected JSON response shape.
```

---

# 🧩 1️⃣7️⃣ JUnit and the Full Loop

JUnit does not replace manual full-loop testing.

JUnit supports it.

Think of it like this:

```text
Manual test = prove the whole system works from the user view

JUnit service test = prove the use case logic works

JUnit controller test = prove the HTTP boundary works

Frontend check = prove the UI sends/receives data correctly
```

Together, they create confidence.

No single test proves everything.

Different tests prove different parts.

---

# 🧠 1️⃣8️⃣ Common Beginner Mistakes

## Testing too much at once

Bad:

```text
One test tries to start frontend, backend, database, camera, adapter, and UI.
```

Better:

```text
Break the proof into layers.
```

## Testing implementation instead of behaviour

Bad:

```text
Check private method details.
```

Better:

```text
Check returned behaviour.
```

## Not testing failure paths

Bad:

```text
Only valid image tested.
```

Better:

```text
Also test missing or empty image.
```

## Mocking everything

Bad:

```text
Test passes because nothing real happened.
```

Better:

```text
Mock only what is outside the boundary of the thing being tested.
```

## Ignoring the ticket

Bad:

```text
Write tests unrelated to acceptance criteria.
```

Better:

```text
Turn acceptance criteria into tests.
```

---

# 📌 1️⃣9️⃣ How to Turn Acceptance Criteria Into Tests

Acceptance criterion:

```text
Given an image is taken,
then axios multipart POST accurately sends image data to the backend.
```

Possible verification:

```text
Manual/frontend test
Check FormData field name
Check backend receives MultipartFile
```

Acceptance criterion:

```text
Given the POST method sends accurate image data,
a response is sent to the frontend.
```

Possible verification:

```text
Controller multipart test
Manual API test
Frontend network check
```

Acceptance criterion:

```text
Given the response is returned,
then it includes meal title, ingredients, steps and time estimate.
```

Possible verification:

```text
JUnit service test
JUnit controller JSON response test
Frontend display check
```

This is how tickets become test plans.

---

# ✅ 2️⃣0️⃣ What You Should Be Ready to Do Next

After this doc, you should be ready to:

```text
Create a MealServiceTest
Create fake MealVisionPort behaviour
Use MockMultipartFile
Call the service method
Assert the MealResponse fields
Create a MealControllerTest
Use MockMvc multipart request
Mock MealService
Assert status code and JSON fields
Record manual full-loop evidence
Create follow-up tickets for missing failure handling
```

---

# 🚀 Final Compression

```text
JUnit = Java behaviour proof system
Test = executable expectation
Arrange = prepare the world
Act = trigger the behaviour
Assert = check the truth
Fake = simple controlled replacement
Mock = tool-controlled replacement
Service test = use-case proof
Controller test = HTTP boundary proof
Acceptance criteria = source of tests
```

---

# 🌌 Ultimate Compression

```text
The ticket says what should be true.

The implementation tries to make it true.

JUnit lets Java prove whether it is true again and again.
```

That is why testing matters.
