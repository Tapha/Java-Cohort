# 🧪 Exercise: Testing the Image to Meal Full Loop 📸🍽️

## Goal

You have now built a full feature loop:

```texts
Take fridge photo
        ↓
Frontend sends image using multipart POST
        ↓
Backend receives image
        ↓
MealController handles request
        ↓
MealService coordinates the use case
        ↓
MealVisionPort defines the vision capability
        ↓
MealVisionAdapter implements the vision capability
        ↓
Meal suggestion is returned
        ↓
Frontend displays the response
```

Now the next task is to prove that the loop works.

This exercise is about testing the behaviour of the feature you created.

Not just checking that the code exists.

Not just checking that the app starts.

The real question is:

```text
Can we prove that an image can travel through the system
and return a useful meal response?
```

---

# 🧠 1️⃣ The Testing Mindset

A feature is not finished when the code is written.

A feature is finished when the expected behaviour can be proven.

For this ticket, the promise was:

```text
A user can take a fridge photo
and receive meal suggestions based on that image.
```

Testing checks whether the promise became working software.

```text
Ticket = promise
Implementation = attempt
Test = proof
```

---

# 🎟️ 2️⃣ Ticket Being Tested

## Title

```text
Image to Meal Full Loop
```

## User Story

```text
As a Fridge2Meal user,
I want to take a picture of what's in my fridge and get meal suggestions as a response,
so that I can decide on what I would like to cook using the ingredients in my fridge.
```

## Acceptance Criteria

```text
Given an image is taken,
then axios multipart POST accurately sends image data to the backend.

Given the POST method sends accurate image data,
a response is sent to the frontend.

Given the response is returned,
then it includes meal title, ingredients, steps, and time estimate.
```

Your tests should prove these criteria.

---

# 🔄 3️⃣ The Full Loop We Are Testing

This is the full path:

```text
Vision camera capture
        ↓
Image file / image URI
        ↓
FormData
        ↓
Axios multipart POST
        ↓
Spring Boot multipart endpoint
        ↓
MealController
        ↓
MealService
        ↓
MealVisionPort
        ↓
MealVisionAdapter
        ↓
Meal suggestion response
        ↓
Frontend UI state
```

A weak test checks only one part.

A strong test checks the full behaviour and key boundaries.

---

# 🧱 4️⃣ Layers to Test

| Layer | What to Prove |
|---|---|
| Frontend capture | An image/file is available after capture |
| Frontend request | Image is sent as multipart/form-data |
| Backend controller | Endpoint accepts MultipartFile |
| Backend service | Service calls the vision port |
| Vision port | Interface allows the service to depend on abstraction |
| Vision adapter | Adapter implements the external/image-processing behaviour |
| Response DTO | Response contains meal title, ingredients, steps, time estimate |
| Frontend response handling | UI receives and displays the meal suggestion |

---

# 🧠 5️⃣ What Kind of Tests Are We Doing?

There are different levels of testing.

## Manual full-loop test

You run the app and test the real behaviour.

```text
Frontend
        ↓
Backend
        ↓
Response
```

This proves the feature works from the user’s point of view.

## Backend API test

You send a multipart request directly to the backend using Postman, Bruno, Insomnia, or curl.

This proves the backend endpoint works without relying on the frontend.

## Unit test

You test one class in isolation.

Example:

```text
MealService should call MealVisionPort and return MealResponse.
```

## Integration-style test

You test that the controller endpoint can receive the request and produce the expected response.

For this exercise, you should do:

```text
1. Manual full-loop verification
2. Backend multipart API verification
3. Service-level test with fake MealVisionPort
4. Controller multipart test shape
```

---

# 📸 6️⃣ Part 1 — Manual Full-Loop Test

Run the frontend and backend.

## Backend

```powershell
cd backend
.\mvnw.cmd spring-boot:run
```

## Frontend

```powershell
cd frontend
npm start
```

Use the frontend to take or select an image.

Then check:

```text
Does the image capture work?
Does the request get sent?
Does the backend receive the request?
Does the frontend receive a response?
Does the frontend display the meal title?
Does the frontend display ingredients?
Does the frontend display steps?
Does the frontend display the time estimate?
```

## Evidence to Capture

Record the result in this table:

| Check | Result | Notes |
|---|---|---|
| Frontend opens | ✅  | Frontend loaded successfully at http://localhost:8081|
| Camera/capture works | ✅ | Image selection/upload worked using the upload button|
| Image URI/file exists | ✅ |An image was selected before the meal response was returned |
| Axios request fires | ✅ | Upload triggered the request to the backend|
| Backend logs show request | 🟡 |No detailed backend upload log was printed, but the Network tab shows the backend request returned 200 OK |
| Response returns to frontend | ✅  |The mock meal response returned after upload |
| Meal title displays | ✅  | Tomato Pasta displayed|
| Ingredients display | ✅  | Pasta and Tomatoes displayed|
| Steps display | ✅ | Cooking steps displayed|
| Time estimate displays | ✅  |15 Minutes displayed |

---

# 🌐 7️⃣ Part 2 — Check the Multipart Request

The frontend should send image data using `multipart/form-data`.

Example shape:

```javascript
const formData = new FormData();

formData.append("image", {
  uri: imageUri,
  name: "fridge.jpg",
  type: "image/jpeg"
});

const response = await axios.post(
  "http://localhost:8080/api/meals/from-image",
  formData,
  {
    headers: {
      "Content-Type": "multipart/form-data"
    }
  }
);
```

## Questions

1. What is the endpoint URL?
2. What is the multipart field name?
3. What is the image file name?
4. What is the image MIME type?
5. What does the backend expect the field name to be?
6. Does the frontend field name match the backend parameter?

Important:

```text
If frontend sends "photo"
but backend expects "image",
the loop breaks.
```
1. http://localhost:8080/api/meals/suggestion
2. image
3. image/jpeg
4. Code checks file.type.startsWith("image/") so the selected file must be an image type such as image/jpeg or image/png
5. image
6. Yes in the network tab-> Payload tab shows image (binary) and the request returned 200 OK
---

# 🚦 8️⃣ Part 3 — Backend Endpoint Check

Your controller should have a multipart endpoint.

Example:

```java
@PostMapping(value = "/from-image", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public ResponseEntity<MealResponse> suggestMealFromImage(
        @RequestParam("image") MultipartFile image
) {
    MealResponse response = mealService.suggestMealFromImage(image);
    return ResponseEntity.ok(response);
}
```

## Questions

1. What route handles the image upload?
2. What HTTP method is used?
3. What does `consumes = MediaType.MULTIPART_FORM_DATA_VALUE` mean?
4. What is the `MultipartFile` parameter called?
5. Does the parameter name match the frontend `FormData` field?
6. Which service method does the controller call?

1. /api/meals/suggestion
2. POST
3. Allows the uploaded form fields including the image to be mapped into the request object
4. There is no direct MultipartFile parameter in the controller method, The controller uses MealRequest request instead,therefore uploaded image is expected to be inside the MealRequest DTO
5. Yes, the frontend sends the image using the field name image, and the Network Payload tab showed image (binary)
6. mealService.generateMeal(request)
---

# 🧪 9️⃣ Part 4 — Test Backend Directly Without Frontend

Sometimes the frontend has bugs.

Sometimes the backend has bugs.

To know which side is failing, test the backend directly.

Use Postman, Bruno, Insomnia, or curl.

## Option A — Postman / Bruno / Insomnia

Create request:

```text
Method: POST
URL: http://localhost:8080/api/meals/from-image
Body type: multipart/form-data
Field name: image
Field type: file
Value: choose a fridge image
```

Expected response:

```json
{
  "title": "Tomato Pasta",
  "usedIngredients": ["tomato", "pasta", "cheese"],
  "steps": [
    "Boil the pasta",
    "Cook the tomatoes",
    "Mix with cheese"
  ],
  "timeEstimateMinutes": 20
}
```

## Option B — curl

```bash
curl -X POST http://localhost:8080/api/meals/from-image \
  -F "image=@fridge.jpg"
```

## Record Results

| Check | Result | Notes |
|---|---|---|
| Backend endpoint reachable | ✅ | Insomnia returned 200 OK from /api/meals/suggestion|
| Multipart request accepted | ✅  | Multipart request was accepted by the backend|
| Image field name works | ✅  |Field name image worked |
| Response returns JSON | ✅  |Backend returned a JSON meal response|
| Response has title | ✅ | Response included Tomato Pasta|
| Response has ingredients | ✅ | Response included Pasta and Tomatoes|
| Response has steps | ✅ |Response included Boil pasta, Cook tomatoes, and Mix together |
| Response has time estimate | ✅  | Response included timeEstimate: 15|

---

# 🧠 1️⃣0️⃣ Part 5 — Understand the Adapter Pattern

The ticket asked for:

```text
MealVisionPort
MealVisionAdapter
GoF Object Adapter
```

The purpose is to separate the service from the specific vision implementation.

```text
MealService should not depend directly on camera/image/AI details.

MealService should depend on a port.
```

Conceptually:

```text
MealService
        ↓ depends on
MealVisionPort
        ↑ implemented by
MealVisionAdapter
```

This is DIP from SOLID.

```text
High-level policy should not depend directly on low-level detail.
```

In this feature:

```text
MealService = high-level use case
MealVisionAdapter = low-level implementation detail
MealVisionPort = boundary between them
```

---

# 🔌 1️⃣1️⃣ Part 6 — Service Test With Fake Port

A strong service test does not need a real camera.

It does not need a real external AI service.

It can use a fake `MealVisionPort`.

Why?

Because the service should depend on the abstraction.

Example idea:

```java
class FakeMealVisionPort implements MealVisionPort {

    @Override
    public MealResponse analyseImage(MultipartFile image) {
        return new MealResponse(
                "Tomato Pasta",
                List.of("tomato", "pasta", "cheese"),
                List.of("Boil pasta", "Cook tomatoes", "Mix together"),
                20
        );
    }
}
```

Then test:

```text
Given a fake image
When MealService.suggestMealFromImage is called
Then it returns the fake meal response
```

This proves:

```text
MealService can coordinate the use case through the port.
```

---

# 🧪 1️⃣2️⃣ Example JUnit Service Test

This is an example shape.

Adjust package names and method names to match your code.

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

## Questions

1. Why do we use a fake port?
2. Why does this test not need a real image?
3. What behaviour is being tested?
4. What would make this test fail?
5. Which SOLID principle makes this easier?

1. Bceause the service should be ested without dpeneidng on the real work such as the real camera, or the ai service. It allows us to give the controlled meal response so the service behaviour can be checked to see if it works
2. The aim is to test the mealservice can pass the request to the vision port and return the meal response
3. if mealservices works as expected by recieving the request, calls the vision port and returns a response
4. if the mealservice didnt call the port, called the wrong method, didnt return a reponse or the wrong response.
5. DIP bc mealservice depends on the mealVisionPort rather then depending on speciifc implementor 
---

# 🚦 1️⃣3️⃣ Part 7 — Controller Multipart Test Shape

A controller test checks whether the endpoint accepts multipart input.

Example shape:

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

## Questions

1. What does `MockMultipartFile` represent?
2. What does `multipart("/api/meals/from-image")` test?
3. Why do we mock `MealService` here?
4. What response fields are being checked?
5. What would fail if the endpoint path was wrong?
6. What would fail if the JSON response shape was wrong?

1. The fake uploaded image file used in the controller test
2. Test if the correct backend route can accept a  form data request
3. Purpose is to only check that the endpoint accepts the uploaded image and returns the expected response
4. title, ingredients, steps, and timeEstimate
5. the test would fail because the controller route would not be found
6. jsonPath checks would fail

---

# ⚠️ 1️⃣4️⃣ Part 8 — Failure Case Tests

Happy path is not enough.

Try these failure scenarios.

| Scenario | Expected Behaviour |
|---|---|
| No image sent | Should return a clear error |
| Wrong multipart field name | Backend should not receive expected file |
| Empty image file | Should be rejected or handled clearly |
| Backend service throws error | Frontend should not silently fail |
| Response missing title | Frontend should not treat it as valid |
| Response missing ingredients | Frontend should not treat it as valid |

## Questions

1. Which failure cases does your current code handle?
2. Which failure cases are not handled yet?
3. Which failures should become follow-up tickets?
4. Which failure should block merge?

1. Only checks to see if it works, a valid image can be uploaded and the backend returns a meal response. The frontend also checks that the selected file is an image by using file.type.startsWith("image/")
2. Doesnt really handle anything else such as handling for missing images, wrong field names, empty image files, backend service errors, or missing response field
3. Follow up tickets should be created for sad paths such as missing image validation, wrong field name handling, empty file validation, backend error handling, frontend error messages, and frontend response validation
4. When the normal happy path does not work like if a vlaid image cannot be uploaded
---

# 🖥️ 1️⃣5️⃣ Part 9 — Frontend Response Check

After axios receives a response, check that the frontend uses the data correctly.

Expected response shape:

```json
{
  "title": "Tomato Pasta",
  "usedIngredients": ["tomato", "pasta", "cheese"],
  "steps": [
    "Boil pasta",
    "Cook tomatoes",
    "Mix together"
  ],
  "timeEstimateMinutes": 20
}
```

Frontend should be able to display:

```text
Meal title
Used ingredients
Steps
Time estimate
```

## Questions

1. Where is the response stored in frontend state?
2. What happens while the request is loading?
3. What happens if the backend returns an error?
4. What happens if the response is missing a field?
5. Does the frontend log or display useful information during failure?

1. stored in meal state using setMeal(data) after getMealSuggestion(file) returns the backend response
2. no clear loading message shown on the page
3. the error is logged using console.error("Failed to load meal suggestion:", error). However, there is no user friendly error message shown on the page
4. the frontend may not display correctly using the full title, steps etc...
5. The frontend logs useful information for developers through the console, but it does not yet display useful failure information to the user
   
---

# 📊 1️⃣6️⃣ Part 10 — Test Evidence Table

Complete this after testing.

| Evidence | Result | Notes |
|---|---|---|
| Frontend sends multipart request | ✅ | The frontend code uses FormData and sends the image using apiClient.post("/meals/suggestion", formData)|
| Backend receives `MultipartFile` | ✅  |The backend accepts the image through MealRequest, which contains MultipartFile image |
| Controller calls service | ✅  |MealController calls mealService.generateMeal(request) |
| Service calls `MealVisionPort` | ✅ |MealService now depends on MealVisionPort, and MealVisionAdapter provides the implementation
| Adapter returns meal suggestion | ✅  | The adapter returns the mock meal suggestion|
| Backend returns JSON response | ✅ | Insomnia returned 200 OK with JSON containing title, ingredients, steps, and timeEstimate|
| Frontend receives response | ✅ | The frontend stores the response using setMeal(data)|
| Meal title displayed | ✅ | Tomato Pasta displayed on the frontend|
| Ingredients displayed | ✅ | Pasta and Tomatoes displayed on the frontend|
| Steps displayed | ✅  |Cooking steps displayed on the frontend |
| Time estimate displayed | ✅ | 15 Minutes displayed on the frontend|
| Backend logs checked | ✅ | Backend ran successfully, but there was no detailed custom upload log|
| Failure cases noted | ✅  | Missing image validation, empty file validation, backend error handling, and frontend error messages were noted as follow-up work|

---

# 🧠 1️⃣7️⃣ Reflection Questions

Answer these after completing the exercise.

1. What part of the loop was easiest to test?
2. What part of the loop was hardest to prove?
3. Did the frontend send the image in the shape the backend expected?
4. Did the backend response match the frontend’s expectations?
5. What did the adapter pattern make easier?
6. Why should the service depend on `MealVisionPort` rather than directly on `MealVisionAdapter`?
7. Which parts of the feature are currently real?
8. Which parts are still fake/simulated?
9. Which failure cases need follow-up tickets?
10. Would you be comfortable merging this feature into the shared project?


1. the frontend display because after uploading an image the meal title, ingredients, steps and time         estimate appeared clearly on the page
2. the upload shape  I had to check that the frontend sent the file as image and that the backend         expected MultipartFile image inside MealRequest
3. Yes, the frontend sent the image in the correct shape. It used 'FormData' with the field name                 'image', and the backend expected 'MultipartFile image' inside 'MealRequest'
4. Yes backend response matched the frontend,  returned title, ngredients steps and timeEstimate which         the frontend displayed from the meal state
5. The adapter pattern made it easier to keep the image processing logic separate from the controller         and frontend
6. The service should depend on MealVisionPort because it is the abstraction. This would make it easier         to replace the mock adapter with real AI later without changing the service logic
7. The real parts are the image upload, multipart request, backend endpoint, service call, JSON response         and frontend display
8. The fake part is the actual image analysis because the adapter currently returns a mock meal suggeston
9. Follow-up tickets are needed for missing image validation, empty file validation, backend error         handling, frontend error messages and real AI integration
10. Yes I would be comfortable merging this as a Phase 1 mock loop because the happy path works and the         backend tests pass. It still needs better validation and real image analysis later

---

# 🧾 1️⃣8️⃣ Follow-Up Tickets to Create

Testing often reveals the next work.

Create follow-up tickets for anything not handled yet.

Possible follow-up tickets:

```text
Add validation for missing image upload
Add clean error response for image-processing failure
Add loading and error states on frontend
Add tests for MealService image flow
Add controller multipart test for image endpoint
Add logging around image upload request
Add response validation on frontend
Add real meal vision integration
Add persistence for generated meal suggestions
```

This is how testing creates the next layer of Agile work.

---

# 🚀 Final Compression

```text
Feature = claimed behaviour
Test = proof of behaviour
Manual test = user-level proof
API test = backend boundary proof
Unit test = class-level proof
Mock/Fake = controlled dependency
Adapter = replaceable implementation detail
Port = stable abstraction
Multipart = image transport format
Response DTO = contract back to frontend
```

---

# 🌌 Ultimate Compression

```text
The ticket said:
A fridge image should become meal suggestions.

The implementation claims:
This loop now exists.

The tests ask:
Can you prove the claim?
```

That is the professional standard.
