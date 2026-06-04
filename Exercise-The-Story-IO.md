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
Input = data entering the program

Output = data leaving the program

Memory = where Java works with data

I/O = how data crosses the bounday of the system
```

## Questions

1. Why is a program without I/O like a room with no doors?
2. Give three examples of input.
3. Give three examples of output.
4. Why is I/O not just about files?
5. Complete this sentence:

```text
I/O matters because software needs to move data across boundaries. 
```
## Answers
1. The analogy of I/O is a doorway where data can enter and leave the program. If a program has no way to work with I/O, there is no way data would be able to enter or leave the program therefore like a room without doors.
2. Read from console, read file and take a network request.
3. Write to console, write to a file and send a response across network. 
4. I/O involves network and database queries. 
---

# 🚪 Part 2 — Identify the Doorway

For each example, decide whether it is input, output, or both.

| Example | Input / Output / Both? | Why? |
|---|---|---|
| User types their name into the console | Input | External data is entering the system.  |
| Java prints “Hello” to the console | Output | Data is leaving the system. |
| Java reads `students.txt` | Input | External data is entering the system. |
| Java writes `results.txt` | Output | Data is leaving the system in the form of a .txt file. |
| Frontend sends JSON to backend | Both | It is both because data is leaving the frontend but entering the backend. |
| Backend returns JSON response | Both | This is both because data is leaving the backend and entering the frontend. |
| Application writes a log message | Output | Data is leaving the system as a log message. |
| Java queries a database | Input | Data is entering the system from the database. |
| Java saves a row to a database | Output | Data is leaving the system as a database row. |

## Reflection

Complete:

```text
A boundary is crossed whenever data enters or leaves a system. 
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
| long-term reservoir | database |

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
2. Why is memory not the same thing as input?
3. Explain this sentence:

```text
Input is the incoming flow.
Memory is the working substance.
```
## Answers
1. I/O is about movement because data is moving between systems by entering and leaving.
2. Memory is not the same as input because memory is where working data is stored e.g. objects but input is data entering the system. Anything from memory is already in the system, it is not external.
3. Input is where data enters the system and memory is where the system works with the data. 
---

# 🖨️ Part 4 — Console Output

Look at this code:

```java
System.out.println("Hello world");
```

## Questions

1. What data exists in memory?
2. Where does the data go?
3. Is this input or output?
4. Complete the flow:

```text
String in memory
        ↓
System.out.println()
        ↓
Console output
```

## Answers
1. The "Hello World" string is in memory as an object.
2. The data gets displayed to the user's screen.
3. This is an example of output. 

## Challenge

Write one more example of console output.

System.out.println("Completing I/O Exercises");

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
2. What reads the input?
3. Where is the user’s name stored after it is read?
4. What output is produced?
5. Complete the flow:

```text
User types name
        ↓
System.in receives input
        ↓
Scanner reads input
        ↓
String stored in memory and output created
        ↓
Console displays greeting
```

## Answers
1. The console via System.in.
2. The Scanner object reads the input.
3. The user's name gets stored in memory as an object.
4. Hello NameEnteredByUser
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
Input is...
Memory is...
Static means...
The better question is...
```

## Answer
Input is data entering the system and memory is where data is worked on for example, where Java works is in memory. Static means the data in the system is consitent and unchanging. 

## Final answer frame

Complete:

```text
Input = what enters

Memory = where it is worked on

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
2. What happens to normal runtime memory when the program stops?
3. What happens when Java reads a file?
4. What happens when Java writes a file?

Complete:

```text
Read = outside → memory

Write = memory → outside
```
## Answer
1. We need files because we need a way to store data when the program has finished running as memory is only available during the time the program is running.
2. Runtime memory disappears when the program stops as it is deallocated.
3. When Java reads a file, it reads the file on disk and the data from the file enters memory.
4. When Java writes a file, it writes the area of memory that needs writing into a file on disk. 
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

1. What file is being read?
2. Which method reads the file?
3. What variable holds the file content?
4. Where does the file content live after it is read?
5. What output happens after reading?

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
## Answers
1. students.txt
2. Files.readString()
3. content
4. The content of the file is in memory.
5. Printing to the console System.out.println(contents).
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

1. What data starts in memory?
2. Which method writes the file?
3. What file is created or updated?
4. Is this input or output?
5. Complete the flow:

```text
String in memory
        ↓
Files.writeString()
        ↓
students.txt on disk
```
## Answers
1. String variable content.
2. Files.writeString()
3. students.txt
4. Output
---

# 🧭 Part 10 — Paths

A `Path` points to a file or folder location.

```java
Path path = Path.of("students.txt");
```

## Questions

1. Does this line read the file?
2. What does `Path.of("students.txt")` represent?
3. Why is a path like an address?
4. What is the difference between a path and the data inside the file?

Complete:

```text
Path = location of external data
```
## Answers
1. No, it just creates a path object for the file.
2. A pointer to the location of students.txt
3. It is like an address because it points to the location of where data should be. 
---

# 🌊 Part 11 — Streams as Pipes

A stream moves data gradually.

## Questions

1. Why might reading a whole file at once be a problem?
2. What kinds of data might be too large to load all at once?
3. Why is a stream like a pipe?
4. Give three examples where streams are useful.

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
## Answers 
1. Files which are generally streamed may be too large to be stored in memory and would overfill memory, causing problems.
2. Large files, audio files, video files and PDFs.
3. Streams are like pipes because they are a controlled flow of data, moving data piece by piece.
4. Uploading and downloading large files, streaming video and downloading images. 

---

# 🔌 Part 12 — InputStream and OutputStream

Match the term to the meaning.

| Term | Meaning |
|---|---|
| InputStream | pipe coming in |
| OutputStream | pipe going out |

Use:

```text
pipe coming in
pipe going out
reads bytes into the program
writes bytes out of the program
```

## Questions

1. Are streams byte-based or text-based?
2. What does an `InputStream` bring into Java memory?
3. What does an `OutputStream` send out?
4. Complete:

```text
InputStream = pipe coming in

OutputStream = pipe going out
```
## Answers
1. Streams are byte based.
2. An InputStream reads bytes into a program.
3. OutputStream writes bytes out of a program. 
---

# ✍️ Part 13 — Readers and Writers

Fill in the table.

| Tool | Works With | Example Use |
|---|---|---|
| InputStream | incoming bytes | reading bytes from a video |
| OutputStream | outgoing bytes | writing bytes as a PDF file |
| Reader | incoming characters | reading from .csv files |
| Writer | outgoing characters | writing to .txt files |

## Questions

1. When would a `Reader` be more natural than an `InputStream`?
2. When would an `InputStream` be more natural than a `Reader`?
3. Complete:

```text
Streams = bytes

Readers/Writers = text
```
## Answers
1. A Reader would be more natural when working with text files.
2. An InputStream would be more natural when working with byte based files like audio and video. 
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
Buffer = temporary memory used to make I/O smoother
```

## Answers
1. It will take longer to do any operation as data will need to continuously loaded into memory.
2. It will wait and collect a chunk of data then move the data when it has enough.
3. A buffer for reading text files.
4. A buffer for writing to text files. 

## Reflection

Explain this metaphor:

```text
Instead of carrying water drop by drop,
carry a bucket.
```
This means that the buffer will collect a bucket of data when it arrives and deliver that as opposed to moving small bits of data each time they arrive. 
---

# 🧬 Part 15 — Serialization and Deserialization

Complete the definitions:

```text
Serialization = object → transferable format

Deserialization = transferable format → object
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
serialization
        ↓
JSON/text/bytes
        ↓
file/API/network
```

Reverse flow:

```text
JSON/text/bytes
        ↓
deserialization
        ↓
Java object
```
## Answers
1. Java memory objects cannot travel across a network because the receiving side will not know how to interpret the Java object.
2. Because Java objects only exist in memory and can only be used by Java so for other access, they need to be converted.
3. JSON, text or bytes.
4. Serialization is important for APIs because APIs expect JSON to be transferred and not Java objects. 

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
Java object → JSON response

JSON request → JSON object
```
## Answers
1. The Java record lives inside the Java memory.
2. The JSON is sent across the network.
3. Spring Boot will convert Java objects to JSON and back again automatically.
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
Spring deserializes JSON
        ↓
MealRequest object in memory
        ↓
Service uses object
        ↓
MealResponse object
        ↓
Spring serializes to JSON
        ↓
HTTP response
```
## Answers
1. The JSON which is sent as part of the request.
2. The MealRequest object
3. Does the deserialization of the JSON. 
---

# 🧱 Part 18 — DTOs as Boundary Objects

DTO means:

```text
Data Transfer Object
```

Fill in the table.

| Type | Purpose |
|---|---|
| Entity | database mapped object |
| DTO | data crossing an API boundary |
| Service | business logic and coordination |
| Collection | many objects in memory |

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
Entity = storage shape

DTO = boundary shape
```
## Answers
1. It is a boundary shape because it is a representation of the entity.
2. Because entities could leak into the API and untracked changes could be made to the entities.
3. MealRequest is the format which requests are sent in and MealResponse is the format which responses are sent in.
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
Good logs make runtime behaviour visible
```
## Answers
1. Logs are a form of output because logs send information out of a running program.
2. Logs can go to the console or a file. 
3. A system without logs is blind because there is nothing telling you about what events are happening in the system.
4. Logs should include information about current events which are happening in the system and their success or failure.
5. Logs should avoid logging information such as personal information like passwords or API keys.
---

# ⚠️ Part 20 — I/O Can Fail

Any time data crosses a boundary, failure can happen.

Match the failure to the boundary.

| Failure | Boundary |
|---|---|
| file not found | Path/File system |
| permission denied | API |
| network timeout | Network |
| invalid JSON | Serialization |
| database unavailable | database |
| disk full | disk storage  |

## Questions

1. Why do boundaries fail?
2. Why is I/O closely connected to exceptions?
3. What should professional code do when boundary failure happens?

Complete:

```text
Boundaries fail.
Professional code handles boundary failure.
```
## Answers
1. Boundaries fail because it is the access to external data and systems which may not always be available.
2. I/O is a risky operation because there is no guarantee that this boundary will be available so it could throw an exception. I/O operations should be wrapped in a try{} catch{} block.
3. Professional code should handle boundary failure gracefully without any catastrophic events. 
---

# 🐢 Part 21 — I/O and Performance

Rank these from fastest to slowest in general:

```text
CPU work
memory access
disk I/O
database query
network call
```

## Questions

1. Why is I/O often slower than memory?
2. Why can calling an API inside a loop become dangerous?
3. Why can too many database queries hurt performance?
4. How does this connect to the ORM N+1 problem?

Complete:

```text
The N+1 problem is an I/O problem hiding behind object access.
```
## Answers
1. I/O is slower than memory because it involves a request to disk, which is a much slower operation compared to memory transfer speeds.
2. It may cause issues with the API timing out or going through too many tokens.
3. Database operations are slow so many database operations will cause a queue of operations waiting to happen, this will slow performance.
4. The ORM N+1 problem is there are database queries in hiding and behind these database queries are I/O operations. 
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
JSON becomes Data DTO
        ↓
Service processes business logic
        ↓
Repository may query database
        ↓
ORM maps rows to entities
        ↓
Service creates response DTO
        ↓
Java object becomes JSON
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

## Answers
1. The HTTP request entering the backend is an example of input.
2. The JSON becoming Java DTO.
3. Repository may query database
4. HTTP response leaving the backend
5. The backend takes an input, processes the data and does any required queries and then processes the output data and sends it as a response. 

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


## Answers
1.
```
public record MealRequest(
    List<String> ingredients
) {}

```
2. 
```
public record MealResponse(
    String title,
    String description,
    List<String> steps
) {}

```
3.
```
POST /api/meals/suggestion
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```
4.
```
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
5. Serialization happens when the object for the response gets turned back into JSON to be sent to the frontend.
6. Deserialization happens when the request JSON gets turned into an object so it can be used in Java.
7. The controller receives the JSON request.
8. The service will generate the required data for the controller to send as a response. 

## Reflection

Complete:

```text
This feature contains REST + I/O + JSON + DTOs + objects because data is crossing multiple boundaries. 
```

---

# 🗺️ Part 24 — Connect the Curriculum

Fill in the missing pieces.

```text
Memory gives the program a working space.

Objects give memory shape.

Collections organize many objects.

ORM connects objects to database storage.

Exceptions handle boundary failure.

Logging makes runtime behaviour visible.

I/O moves data in and out.

REST structures web I/O.
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
## Answers 
1. I/O is the process of allowing data to enter and leave a system through various boundaries.
2. I/O allows software to interact with other software and services which are available and receive extra data, it is not closed off.
3. Input is where data enters a system whereas memory is where data is worked on in a system.
4. A stream is where data is sent in parts usually because it is too big to be sent at once into memory.
5. A buffer is a bucket or temporary memory location to collect incoming data before it is sent on to make the movement of data more efficient.
6. Serialization is turning Java objects into another useful format e.g. JSON.
7. Deserialization is turning a format sent into a program into a Java object so it can be used in the Java program.
8. JSON is important because it is the common format for sending data over APIs and goes hand in hand with JavaScript which is used on the web.
9. A DTO is a representation of an entity so entities do not have to be visible to the APIs.
10. I/O can fail because it relies on other systems and resources which may not always be available so can often throw exceptions.
11. I/O is a performance bottleneck because it isn't memory operations and relies on network and disk which are inherently slow.
12. This sentence represents the fact that software will take data in (input), process the data and then send data out of the system (output). This is a full cycle rather like the breathing cycle which humans do. 
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
