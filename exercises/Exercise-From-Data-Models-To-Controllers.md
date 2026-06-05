# 🧪 Exercise: From Data Models to Controllers

## Goal

In this exercise, you will take a simple data model and turn it into the structure needed for a basic API.

You will practise moving from:

```text
Model → Repository → Service → DTO → Controller
```

By the end, you should understand how data moves through a layered Java application.

---

## Scenario

You are building a small course management system.

The system needs to manage **students**.

A student has:

```text
id
firstName
lastName
email
age
```

Your job is to design the basic backend structure for working with students.

---

## Part 1: Create the Model

Create a `Student` class.

It should contain:

```java
private Long id;
private String firstName;
private String lastName;
private String email;
private int age;
```

### Task

Create the class with:

* fields
* constructor
* getters
* setters

### Reflection Question

What does the `Student` model represent in the system?

Write one sentence:

```text
The Student model represents...
```

---

## Part 2: Create the Repository

Create a `StudentRepository`.

For now, you do not need to connect it to a real database.

You can imagine it as the part of the system responsible for storing and finding students.

### Task

Write method names you think this repository should have.

Example:

```java
findAll()
findById(Long id)
save(Student student)
deleteById(Long id)
```

### Reflection Question

Why should the controller not directly manage the storage of students?

---

## Part 3: Create the Request DTO

Create a DTO called:

```java
CreateStudentRequest
```

This class should contain only the data needed when creating a new student.

It should include:

```java
private String firstName;
private String lastName;
private String email;
private int age;
```

It should not include:

```java
id
```

### Question

Why should the user not send the `id` when creating a student?

---

## Part 4: Create the Response DTO

Create a DTO called:

```java
StudentResponse
```

This class should represent what the API sends back to the user.

It should include:

```java
private Long id;
private String firstName;
private String lastName;
private String email;
private int age;
```

### Challenge

Add a method or constructor that can convert a `Student` into a `StudentResponse`.

Example idea:

```java
public StudentResponse(Student student) {
    this.id = student.getId();
    this.firstName = student.getFirstName();
    this.lastName = student.getLastName();
    this.email = student.getEmail();
    this.age = student.getAge();
}
```

---

## Part 5: Create the Service

Create a `StudentService`.

The service should contain the application logic.

### Task

Create a method:

```java
public StudentResponse createStudent(CreateStudentRequest request)
```

Inside this method, think through the flow:

```text
1. Receive CreateStudentRequest
2. Create a Student model
3. Save the Student using StudentRepository
4. Convert the saved Student into StudentResponse
5. Return StudentResponse
```

### Reflection Question

Why is the service a better place for this logic than the controller?

---

## Part 6: Create the Controller

Create a `StudentController`.

The controller should expose an endpoint for creating a student.

Example endpoint:

```text
POST /students
```

### Task

Create a method like this:

```java
@PostMapping
public StudentResponse createStudent(@RequestBody CreateStudentRequest request) {
    return studentService.createStudent(request);
}
```

### Reflection Question

What is the controller responsible for?

Choose the best answer:

```text
A. Storing data directly
B. Receiving HTTP requests and calling the service
C. Containing all business rules
D. Replacing the repository
```

---

## Part 7: Draw the Flow

Draw or write the journey of a request through your application.

Use this structure:

```text
HTTP Request
    ↓
Controller
    ↓
Request DTO
    ↓
Service
    ↓
Model
    ↓
Repository
    ↓
Database / Storage
```

Then draw the return journey:

```text
Storage
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

## Part 8: Explain the Building Analogy

Complete the analogy:

```text
Model        = ______________________
Repository   = ______________________
Service      = ______________________
DTO          = ______________________
Controller   = ______________________
```

Use this guide:

```text
Model        = the real thing inside the system
Repository   = the storage/access doorway
Service      = the staff who know the rules
DTO          = the form visitors fill in
Controller   = the reception desk
```

---

## Extension Challenge

Add another model called `Course`.

A course has:

```text
id
title
description
durationWeeks
```

Then create the same structure:

```text
Course
CourseRepository
CreateCourseRequest
CourseResponse
CourseService
CourseController
```

Finally, create an endpoint:

```text
POST /courses
```

---

## Final Questions

Answer these in your own words:

1. Why do we create DTOs instead of exposing models directly?
2. What is the difference between a model and a DTO?
3. What is the job of the service layer?
4. What is the job of the controller?
5. Why is it useful to separate the application into layers?

---

## Success Criteria

You have completed the exercise when you can explain this flow clearly:

```text
The controller receives the request.
The DTO shapes the data.
The service applies the application logic.
The model represents the core object.
The repository handles storage.
The response DTO sends clean data back to the user.
```

That is the basic journey from data models to controllers.
