# 🧱 From Data Models to Controllers

## 1. Where We Are Now

Right now, we are defining our **data models**.

A data model describes the important things our application needs to remember.

For example:

```java
Student
Course
Enrollment
Assignment
```

These are not just random classes.

They represent the **main nouns** in the system.

If the application is a school system, then students, courses, teachers, and assignments are part of the world we are modelling.

---

## 2. What a Model Really Means

A model answers:

> “What kind of thing exists in this system, and what information does it carry?”

Example:

```java
public class Student {
    private Long id;
    private String name;
    private String email;
}
```

This tells us that a `Student` has:

* an `id`
* a `name`
* an `email`

At this stage, we are shaping the **memory of the application**.

The model is what the system knows.

---

## 3. From Models to Repositories

Once we have models, the next question is:

> “How do we save, find, update, and delete these things?”

That is the job of the **repository**.

The model is the thing.

The repository is how we access the thing.

```java
Student → StudentRepository
Course → CourseRepository
Assignment → AssignmentRepository
```

So if `Student` is the house, the repository is the controlled doorway into that house.

It allows us to say:

```java
findAllStudents()
findStudentById()
saveStudent()
deleteStudent()
```

---

## 4. From Repositories to Services

Repositories are about data access.

But applications are not only about storing data.

They also need rules.

For example:

* A student should not enroll twice in the same course.
* A course may have a maximum number of students.
* An assignment cannot be submitted after the deadline.
* A user may need permission before changing data.

These rules belong in the **service layer**.

The service asks:

> “What should the application actually do?”

Example:

```java
StudentService
CourseService
EnrollmentService
```

The service coordinates the work.

It may use one repository or many repositories.

---

## 5. From Services to DTOs

Before we reach controllers, we need to think about the data coming in and going out.

We do not usually want the outside world to directly touch our models.

So we create **DTOs**.

DTO means **Data Transfer Object**.

A DTO controls the shape of data entering or leaving the system.

Example:

```java
CreateStudentRequest
StudentResponse
UpdateStudentRequest
```

The DTO protects the domain model.

The outside world sends a request.

The application decides what that request means.

---

## 6. Now We Are Ready for Controllers

A controller is the part of the application that receives HTTP requests.

It is the public entrance into the system.

For example:

```text
GET /students
POST /students
GET /students/{id}
PUT /students/{id}
DELETE /students/{id}
```

The controller does not contain the main business logic.

Its job is to:

1. Receive the request
2. Accept the DTO
3. Call the correct service method
4. Return the response

Example:

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    private final StudentService studentService;

    public StudentController(StudentService studentService) {
        this.studentService = studentService;
    }

    @PostMapping
    public StudentResponse createStudent(@RequestBody CreateStudentRequest request) {
        return studentService.createStudent(request);
    }
}
```

The controller is the front desk.

The service does the real application work.

The repository handles storage.

The model represents the core thing.

---

## 7. The Full Flow

```text
HTTP Request
    ↓
Controller
    ↓
DTO
    ↓
Service
    ↓
Model / Domain Logic
    ↓
Repository
    ↓
Database
```

And then the response comes back:

```text
Database
    ↓
Repository
    ↓
Service
    ↓
Response DTO
    ↓
Controller
    ↓
HTTP Response
```

---

## 8. The Key Mental Model

Think of the application like a building.

```text
Model        = the real rooms and objects inside the building
Repository   = the storage/access system
Service      = the staff who know the rules
DTO          = the forms visitors fill in
Controller   = the reception desk
```

A visitor does not walk directly into the storage room.

They go to reception.

Reception takes the form.

The staff check the rules.

Then the system performs the action.

That is why we move from:

```text
Models → Repositories → Services → DTOs → Controllers
```

Each layer has a job.

Good software keeps those jobs separate.

---

## 9. What We Build Next

Now that our data models are defined, we can start exposing them through controlled routes.

For each model, we will usually create:

```text
Model
Repository
Service
Request DTO
Response DTO
Controller
```

Example for `Student`:

```text
Student.java
StudentRepository.java
StudentService.java
CreateStudentRequest.java
StudentResponse.java
StudentController.java
```

This gives us a clean path from internal data structure to external API endpoint.

The model defines what exists.

The controller defines how the outside world can interact with it.
