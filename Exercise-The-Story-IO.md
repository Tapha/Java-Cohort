# 🌊 Exercise: The Story of I/O — How Java Systems Breathe 📥📤☕️

## Goal

By the end of this exercise, you should be able to explain how Java moves data through this full cycle:

```text
Outside world
        ↓
Input
        ↓
Java memory
        ↓
Processing
        ↓
Output
        ↓
Outside world
```

You are not just learning file handling.

You are learning how Java systems breathe.

---

# 🧠 Part 1 — The Big Picture

Complete the missing words:

```text
Input = data in the program

Output = data out the program

Memory = where Java works with data

I/O = how data crosses the boundary of the system
```

## Questions

1. Why is a program without I/O like a room with no doors?
   Because the data cant leave and doesnt not interact with the system at all
2. Give three examples of input.
   form, JSON request, file being read
3. Give three examples of output.
   console.log text, JSON response, log message 
4. Why is I/O not just about files?
   Because it works with many thing such as the JSON and database not just the files
5. Complete this sentence:

```text
I/O matters because software needs to receive data, work with it then send the data back out
```

---

# 🚪 Part 2 — Identify the Doorway

For each example, decide whether it is input, output, or both.

| Example | Input / Output / Both? | Why? |
|---|---|---|
| User types their name into the console | Input| data entering the system |
| Java prints “Hello” to the console | output | data leaving and displaying hello |
| Java reads `students.txt` | input | files data that enters java memory  |
| Java writes `results.txt` | output | data that leaves the memory and goes into the file|
| Frontend sends JSON to backend | input | data that enters the beckend |
| Backend returns JSON response | output | JSON data that leaves the backend|
| Application writes a log message | output | displays log message |
| Java queries a database |  both| a request goes out and returns data that goes back in| Java saves a row to a database | output | data leaves the system and goes to the database |

## Reflection

Complete:

```text
A boundary is crossed whenever data crosses the system boundary 
```

---

# 💧 Part 3 — The Water Metaphor

Match the metaphor to the Java concept.

| Metaphor | Java / Software Concept |
|---|---|
| water | memory |
| water entering | input |
| water leaving | output |
| cups | variables |
| containers | objects |
| pipes | streams |
| buckets | buffers |
| storage tanks | files |
| long-term reservoir | database|

Use these options:

```text
variables
memory
input
output
objects
streams
buffers
files
database
```

## Questions

1. Why is I/O about movement?
   data goes in, the memory shapes and the data then goes back out
2. Why is memory not the same thing as input?
   the memory holds the data, the input is the data entering
3. Explain this sentence:

```text
Input is the incoming flow.
Memory is the working substance.
```
Input is the incoming flow, the momery is the working substance, therefore the input enters the system and then the memory holds it 
---

# 🖨️ Part 4 — Console Output

Look at this code:

```java
System.out.println("Hello world");
```

## Questions

1. What data exists in memory?
   "Hello world"
2. Where does the data go?
 console
3. Is this input or output?
 output
4. Complete the flow:

```text
String in memory
        ↓
System.out.println("Hello world")
        ↓
Console output
```

## Challenge

Write one more example of console output.

System.out.println("Ubah")
---

# ⌨️ Part 5 — Console Input

Look at this code:

```java
Scanner scanner = new Scanner(System.in);

System.out.println("Enter your name:");

String name = scanner.nextLine();

System.out.println("Hello " + name);
```

## Questions

1. What is the input source?
 System.in
2. What reads the input?
   scanner
3. Where is the user’s name stored after it is read?
   name
4. What output is produced?
   Hello + name 
5. Complete the flow:

```text
User types name
        ↓
System.in recieves name
        ↓
Scanner reads input
        ↓
Name is stroed in memory
        ↓
Console displays greeting
```

---

# 🧩 Part 6 — Input Is Not Memory

A student asks:

```text
Is input static memory?
```

## Task

Explain why this is the wrong category of question.

Use this structure:

```text
Input is data entering the system
Memory is where the system holds that data while it works
Static means how data is owned or managed
The better question is where does the input go after it is read?
```

## Final answer frame

Complete:

```text
Input = what enters
Memory = where it is held
Static/dynamic = how data is owned or managed
```

---

# 📄 Part 7 — Files as External Storage

A file exists outside the running Java program.

Examples:

```text
students.txt
orders.csv
config.json
image.png
application.properties
```

## Questions

1. Why do we need files if we already have memory?
   files would still exist when the memory disappears
2 What happens to normal runtime memory when the program stops?
   disappears
3. What happens when Java reads a file?
   data enters memory
4. What happens when Java writes a file?
   data leaves the memory and goes to the file

Complete:

```text
Read = outside → memory

Write = memory → outside
```

---

# 📥 Part 8 — Reading a File

Look at this code:

```java
import java.nio.file.Files;
import java.nio.file.Path;

public class FileExample {

    public static void main(String[] args) throws Exception {
        String content = Files.readString(Path.of("students.txt"));

        System.out.println(content);
    }
}
```

## Questions

1. What file is being read? students.txt
2. Which method reads the file? Files.readString()
3. What variable holds the file content? content
4. Where does the file content live after it is read? memory
5. What output happens after reading? prints in the console

Complete the flow:

```text
students.txt
        ↓
Files.readString()

        ↓
String content in memory
        ↓
System.out.println()
        ↓
Console output
```

---

# 📤 Part 9 — Writing a File

Look at this code:

```java
import java.nio.file.Files;
import java.nio.file.Path;

public class WriteFileExample {

    public static void main(String[] args) throws Exception {
        String content = "Amina,David,Sara";

        Files.writeString(Path.of("students.txt"), content);
    }
}
```

## Questions

1. What data starts in memory? "Amina,David,Sara";
2. Which method writes the file? Files.writeString
3. What file is created or updated? students.txt
4. Is this input or output? output
5. Complete the flow:

```text
String in memory
        ↓
Files.writeString()

        ↓
students.txt on disk
```

---

# 🧭 Part 10 — Paths

A `Path` points to a file or folder location.

```java
Path path = Path.of("students.txt");
```

## Questions

1. Does this line read the file? No
2. What does `Path.of("students.txt")` represent? the location where the file is found
3. Why is a path like an address? shows you where the filed live
4. What is the difference between a path and the data inside the file? path is not the data it is where the data is stored

Complete:

```text
Path = address of external data
```

---

# 🌊 Part 11 — Streams as Pipes

A stream moves data gradually.

## Questions

1. Why might reading a whole file at once be a problem? too large to read
2. What kinds of data might be too large to load all at once? things like images and videos
3. Why is a stream like a pipe? the data moves through the pipe gradually
4. Give three examples where streams are useful.Streams are useful for large files, uploads, downloads, network responses, images, PDFs, audio/video, and big log

Use examples from:

```text
large files
uploads
downloads
network responses
images
PDFs
audio/video
big logs
```

Complete:

```text
Stream = pipe for moving data gradually
```

---

# 🔌 Part 12 — InputStream and OutputStream

Match the term to the meaning.

| Term | Meaning |
|---|---|
| InputStream | pipe coming in, reads bytes into the program |
| OutputStream | pipe going out, writes bytes out of the program |

Use:

```text
pipe coming in
pipe going out
reads bytes into the program
writes bytes out of the program
```

## Questions

1. Are streams byte-based or text-based? byte-based
2. What does an `InputStream` bring into Java memory?bytes into Java memory
3. What does an `OutputStream` send out?sends bytes out of Java memory
4. Complete:

```text
InputStream = pipe coming in

OutputStream = pipe going out
```

---

# ✍️ Part 13 — Readers and Writers

Fill in the table.

| Tool | Works With | Example Use |
|---|---|---|
| InputStream | incoming bytes | images, PDFs, audio, video |
| OutputStream | outgoing bytes | writing images, PDFs, binary files |
| Reader | incoming characters | reading .txt, .csv, .json |
| Writer | outgoing characters | writing text files or logs |

## Questions

1. When would a `Reader` be more natural than an `InputStream`? text 
2. When would an `InputStream` be more natural than a `Reader`?with bytes, such as images, PDFs, audio, or video.

3. Complete:

```text
Streams = bytes

Readers/Writers = text
```

---

# 🪣 Part 14 — Buffers

A buffer is temporary memory used while moving data.

## Questions

1. Why is moving data one tiny piece at a time inefficient?
2. Why is a buffer like a bucket?
3. What does `BufferedReader` suggest?
4. What does `BufferedWriter` suggest?
5. Complete:

```text
Buffer = temporary __________ used to make I/O smoother
```

## Reflection

Explain this metaphor:

```text
Instead of carrying water drop by drop,
carry a bucket.
```

---

# 🧬 Part 15 — Serialization and Deserialization

Complete the definitions:

```text
Serialization = object → __________

Deserialization = __________ → object
```

## Questions

1. Why can’t Java memory objects directly travel across a network?
2. Why do objects need to be converted before storage or transfer?
3. What formats can objects be serialized into?
4. Why is serialization important for APIs?

Complete the flow:

```text
Java object
        ↓
?
        ↓
JSON/text/bytes
        ↓
file/API/network
```

Reverse flow:

```text
JSON/text/bytes
        ↓
?
        ↓
Java object
```

---

# 🟨 Part 16 — JSON as API Format

Java object shape:

```java
public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}
```

JSON shape:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple pasta meal",
  "steps": [
    "Boil pasta",
    "Cook tomatoes",
    "Mix together"
  ]
}
```

## Questions

1. Which one lives inside Java memory?
2. Which one crosses the network?
3. What does Spring Boot often do automatically?
4. Complete:

```text
Java object → __________ response

JSON request → __________ object
```

---

# 🌐 Part 17 — JSON Request to Java Object

Frontend sends:

```json
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```

Java receives:

```java
public record MealRequest(
    List<String> ingredients
) {}
```

Controller:

```java
@PostMapping("/suggestion")
public MealResponse suggestMeal(@RequestBody MealRequest request) {
    return mealService.generateMeal(request);
}
```

## Questions

1. What is the JSON request body?
2. What Java object represents the request?
3. What does `@RequestBody` suggest conceptually?
4. What does the service receive?
5. What does the controller return?

Complete the flow:

```text
JSON request body
        ↓
Spring __________ JSON
        ↓
MealRequest object in memory
        ↓
Service uses object
        ↓
MealResponse object
        ↓
Spring __________ to JSON
        ↓
HTTP response
```

---

# 🧱 Part 18 — DTOs as Boundary Objects

DTO means:

```text
Data Transfer Object
```

Fill in the table.

| Type | Purpose |
|---|---|
| Entity | ? |
| DTO | ? |
| Service | ? |
| Collection | ? |

Use:

```text
database-mapped object
data crossing an API boundary
business logic / coordination
many objects in memory
```

## Questions

1. Why is a DTO a boundary shape?
2. Why should we avoid exposing entities directly through APIs?
3. What is the difference between `MealRequest` and `MealResponse`?
4. Complete:

```text
Entity = __________ shape

DTO = __________ shape
```

---

# 🧾 Part 19 — Logs Are Output

Logging is output.

Examples:

```text
User registration started
Meal suggestion generated
File upload failed
Database connection failed
Payment provider timed out
```

## Questions

1. Why are logs a form of output?
2. Where can logs go?
3. Why is a system without logs blind?
4. What kind of information should logs include?
5. What kind of information should logs avoid?

Complete:

```text
Good logs make runtime behaviour __________
```

---

# ⚠️ Part 20 — I/O Can Fail

Any time data crosses a boundary, failure can happen.

Match the failure to the boundary.

| Failure | Boundary |
|---|---|
| file not found | ? |
| permission denied | ? |
| network timeout | ? |
| invalid JSON | ? |
| database unavailable | ? |
| disk full | ? |

## Questions

1. Why do boundaries fail?
2. Why is I/O closely connected to exceptions?
3. What should professional code do when boundary failure happens?

Complete:

```text
Boundaries fail.
Professional code handles __________ failure.
```

---

# 🐢 Part 21 — I/O and Performance

Rank these from fastest to slowest in general:

```text
network call
memory access
database query
CPU work
disk I/O
```

## Questions

1. Why is I/O often slower than memory?
2. Why can calling an API inside a loop become dangerous?
3. Why can too many database queries hurt performance?
4. How does this connect to the ORM N+1 problem?

Complete:

```text
The N+1 problem is an __________ problem hiding behind object access.
```

---

# 🔄 Part 22 — Full Backend Data Journey

Complete the backend journey:

```text
Frontend sends JSON request
        ↓
HTTP input crosses network
        ↓
Controller receives request
        ↓
JSON becomes __________
        ↓
Service processes __________
        ↓
Repository may query __________
        ↓
ORM maps rows to __________
        ↓
Service creates response __________
        ↓
Java object becomes __________
        ↓
HTTP response leaves backend
```

## Questions

1. Where does input happen?
2. Where does memory processing happen?
3. Where does database I/O happen?
4. Where does output happen?
5. Why is this the full breathing cycle of a backend?

---

# 🍅 Part 23 — Fridge2Meal Flow

Request:

```http
POST /api/meals/suggestion
```

Request body:

```json
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```

Expected response:

```json
{
  "title": "Tomato Pasta",
  "description": "A simple meal using tomato, pasta and cheese.",
  "steps": [
    "Boil pasta",
    "Cook tomatoes into a sauce",
    "Mix pasta and sauce together"
  ]
}
```

## Tasks

1. Create a `MealRequest` DTO.
2. Create a `MealResponse` DTO.
3. Identify the input.
4. Identify the output.
5. Identify where serialization happens.
6. Identify where deserialization happens.
7. Identify which part belongs to the controller.
8. Identify which part belongs to the service.

## Reflection

Complete:

```text
This feature contains REST + I/O + JSON + DTOs + objects because...
```

---

# 🗺️ Part 24 — Connect the Curriculum

Fill in the missing pieces.

```text
Memory gives the program a __________ space.

Objects give memory __________.

Collections organize __________ objects.

ORM connects objects to __________ storage.

Exceptions handle boundary __________.

Logging makes runtime behaviour __________.

I/O moves data __________ and __________.

REST structures __________ I/O.
```

---

# 🚀 Final Reflection

Answer in your own words:

1. What is I/O?
2. Why is I/O how software touches the outside world?
3. What is the difference between input and memory?
4. What is a stream?
5. What is a buffer?
6. What is serialization?
7. What is deserialization?
8. Why is JSON important in REST APIs?
9. What is a DTO?
10. Why can I/O fail?
11. Why is I/O often a performance bottleneck?
12. Explain this sentence:

```text
I/O is how Java systems breathe.
```

---

# 🌟 Stretch Challenge — Design an I/O Flow

Design an I/O flow for a student management feature.

Scenario:

```text
A frontend sends a student registration request.
The backend validates it.
The backend saves the student.
The backend logs the event.
The backend returns a JSON response.
```

## Tasks

1. Write the request JSON.
2. Design a `StudentRequest` DTO.
3. Design a `StudentResponse` DTO.
4. Identify the input boundary.
5. Identify the output boundary.
6. Identify where the database I/O happens.
7. Identify where logging output happens.
8. Identify two possible failure points.
9. Explain how the system should respond if validation fails.
10. Explain how the system should respond if saving fails.

---

# 🧠 Final Compression

```text
Input = data entering
Output = data leaving
Memory = where Java works
Path = address of external data
File = stored external data
Stream = pipe for moving data
Buffer = temporary bucket
Reader/Writer = text I/O
InputStream/OutputStream = byte I/O
Serialization = object → transferable format
Deserialization = transferable format → object
JSON = common API format
DTO = boundary data shape
REST = structured web I/O
```

Use this as your map.
