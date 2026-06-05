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

public class Student {
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    private int age;

    public Student(Long id, String firstName, String lastName, String email, int age) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.age = age;
    }
    public Long getId() {
        return id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getFirstName() {
        return firstName;
    }
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}

### Reflection Question

What does the `Student` model represent in the system?

Write one sentence:

```text
The Student model represents the information of the student that the system needs to know
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
public class StudentRepository {
    public List<Student> findAll() {
        return null;
    }
    public Student findById(Long id) {
        return null;
    }
    public Student save(Student student) {
        return student;
    }
    public void deleteById(Long id) {
    }
}

### Reflection Question

Why should the controller not directly manage the storage of students?
Because the storage is the job of the repositiory but the controller should rather work with the request and services
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
public class CreateStudentRequest {
    private String firstName;
    private String lastName;
    private String email;
    private int age;

    public CreateStudentRequest(String firstName, String lastName, String email, int age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.age = age;
    }
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public String getEmail() {
        return email;
    }
    public int getAge() {
        return age;
    }
}
### Question

Why should the user not send the `id` when creating a student?
Because the system should create the id when the information of the student is saved rather then the user
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
public class StudentResponse {
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    private int age;

    public StudentResponse(Student student) {
        this.id = student.getId();
        this.firstName = student.getFirstName();
        this.lastName = student.getLastName();
        this.email = student.getEmail();
        this.age = student.getAge();
    }
    public Long getId() {
        return id;
    }
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public String getEmail() {
        return email;
    }
    public int getAge() {
        return age;
    }
}
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
public class StudentService {

    private final StudentRepository studentRepository;
    public StudentService(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }
    public StudentResponse createStudent(CreateStudentRequest request) {
        Student student = new Student(
                null,
                request.getFirstName(),
                request.getLastName(),
                request.getEmail(),
                request.getAge()
        );
        Student savedStudent = studentRepository.save(student);
        return new StudentResponse(savedStudent);
    }
}
### Reflection Question

Why is the service a better place for this logic than the controller?

the service contains the business logic and rules, but the controller just recieves the request and calls out to the service
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
### Reflection Question

What is the controller responsible for?

Choose the best answer:
```text
A. Storing data directly
B. Receiving HTTP requests and calling the service
C. Containing all business rules
D. Replacing the repository
```
B. Receiving HTTP requests and calling the service

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
HTTP Request → Controller → Request DTO → Service → Model → Repository → Database / Storage
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
Storage → Repository → Service → Response DTO → Controller → HTTP Response
---

## Part 8: Explain the Building Analogy

Complete the analogy:

```text
Model        = the real thing inside the system
Repository   = the storage/access doorway
Service      = the staff who know the rules
DTO          = the form visitors fill in
Controller   = the reception desk
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
public class Course {
    private Long id;
    private String title;
    private String description;
    private int durationWeeks;

    public Course(Long id, String title, String description, int durationWeeks) {
        this.id = id;
        this.title = title;
        this.description = description;
        this.durationWeeks = durationWeeks;
    }
    public Long getId() {
        return id;
    }
    public String getTitle() {
        return title;
    }
    public String getDescription() {
        return description;
    }
    public int getDurationWeeks() {
        return durationWeeks;
    }
}
Then create the same structure:

```text
Course
CourseRepository
CreateCourseRequest
CourseResponse
CourseService
CourseController
```
public class CourseRepository {
    public List<Course> findAll() {
        return null;
    }
    public Course findById(Long id) {
        return null;
    }
    public Course save(Course course) {
        return course;
    }
    public void deleteById(Long id) {
    }
}
public class CreateCourseRequest {
    private String title;
    private String description;
    private int durationWeeks;

    public CreateCourseRequest(String title, String description, int durationWeeks) {
        this.title = title;
        this.description = description;
        this.durationWeeks = durationWeeks;
    }
    public String getTitle() {
        return title;
    }
    public String getDescription() {
        return description;
    }
    public int getDurationWeeks() {
        return durationWeeks;
    }
}
public class CourseResponse {
    private Long id;
    private String title;
    private String description;
    private int durationWeeks;

    public CourseResponse(Course course) {
        this.id = course.getId();
        this.title = course.getTitle();
        this.description = course.getDescription();
        this.durationWeeks = course.getDurationWeeks();
    }
}
public class CourseService {
    private final CourseRepository courseRepository;

    public CourseService(CourseRepository courseRepository) {
        this.courseRepository = courseRepository;
    }
    public CourseResponse createCourse(CreateCourseRequest request) {
        Course course = new Course(
                null,
                request.getTitle(),
                request.getDescription(),
                request.getDurationWeeks()
        );
        Course savedCourse = courseRepository.save(course);
        return new CourseResponse(savedCourse);
    }
}
@RestController
@RequestMapping("/courses")
public class CourseController {

    private final CourseService courseService;

    public CourseController(CourseService courseService) {
        this.courseService = courseService;
    }

    @PostMapping
    public CourseResponse createCourse(@RequestBody CreateCourseRequest request) {
        return courseService.createCourse(request);
    }
}
Finally, create an endpoint:

```text
POST /courses
```
POST /courses
---

## Final Questions

Answer these in your own words:

1. Why do we create DTOs instead of exposing models directly?
2. What is the difference between a model and a DTO?
3. What is the job of the service layer?
4. What is the job of the controller?
5. Why is it useful to separate the application into layers?


1. By creating the DTOS to control the data coming in and out rather than letting the outside change the model
2. a model is a representation of the system but the dto is the shape of the data used for the request and response
3. the service layer contains the application logic and rule
4. the controller recieves the HTTP requests, gets the dtos and then calls the service and then returns the response
5. each of the layer has their own job that keeps everything clean and easy to understand 
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
