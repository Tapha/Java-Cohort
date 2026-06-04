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

Output = data exiting the program

Memory = where Java saves and stores the data

I/O = how data crosses the different layers of the system
```

## Questions

1. Why is a program without I/O like a room with no doors?

There is no where for data to flow in and out of the system.

2. Give three examples of input.

a txt file, a cvs file, a pdf

3. Give three examples of output.

The same as above, logs images etc.

4. Why is I/O not just about files?

Its mainly about the flow of data, the inputted data can be a file, but it can be other things, and this is the same with the outputted data.

5. Complete this sentence:

```text
I/O matters because software needs to have data flow
```

---

# 🚪 Part 2 — Identify the Doorway

For each example, decide whether it is input, output, or both.

| Example | Input / Output / Both? | Why? |
|---|---|---|
| User types their name into the console | input | info flowing into system |
| Java prints “Hello” to the console | output | info flowing out of system |
| Java reads `students.txt` | input | data is flowing into the system |
| Java writes `results.txt` | output | data is flowing out of the system |
| Frontend sends JSON to backend | input | json is converted into an object and inputted to the system |
| Backend returns JSON response | output | object converted into json and outputted out of the ystem |
| Application writes a log message | output | ? |
| Java queries a database | both | data is entering the database and info will be returned to java |
| Java saves a row to a database | both | ? |

## Reflection

Complete:

```text
A boundary is crossed whenever I/O is used.
```

---

# 💧 Part 3 — The Water Metaphor

Match the metaphor to the Java concept.

| Metaphor | Java / Software Concept |
|---|---|
| water | data |
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

I/O allows for flow between the application from outside to inside and vise versa.

2. Why is memory not the same thing as input?

Memory is stored data, something thats an input is data but at this point it isn't yet saved.

3. Explain this sentence:

```text
Input is the incoming flow.
Memory is the working substance.
```


---

# 🖨️ Part 4 — Console Output

Look at this code:

```java
System.out.println("Hello world");
```

## Questions

1. What data exists in memory?

"Hello World"

2. Where does the data go?

It gets outputted to the console log

3. Is this input or output?

output

4. Complete the flow:

```text
String in memory
        ↓
System.out.println()
        ↓
Console output
```

## Challenge

Write one more example of console output.

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

The console

2. What reads the input?

Scanner object

3. Where is the user’s name stored after it is read?

in the String name

4. What output is produced?

"Hello "name"".

5. Complete the flow:

```text
User types name
        ↓
System.in recieves the input
        ↓
Scanner reads input
        ↓
String saved in memory, and java creates output
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
Input is the incoming flow of data
Memory is the working substance of data
Static means the data cant change in size
The better question is...
```

## Final answer frame

Complete:

```text
Input = what the data is coming from

Memory = where it is stored

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

Memory gets deleted when the java runtime ends. Files is data storage that is quite flexible and can be used by people to send to others.

2. What happens to normal runtime memory when the program stops?

It gets deleted.

3. What happens when Java reads a file?

the file starts on the disk, then it is read by java and converted into memory

4. What happens when Java writes a file?

The memory is converted by java and writen into a file which is put on the disk

Complete:

```text
Read = outside → memory

Write = memory → file
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

1. What file is being read?

"students.txt"

2. Which method reads the file?

readString()

3. What variable holds the file content?

content

4. Where does the file content live after it is read?

in java memory

5. What output happens after reading?

it is outputted to the console

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

1. What data starts in memory?

the content, so the string "Amina,David,Sara"

2. Which method writes the file?

writeString

3. What file is created or updated?

students.txt

4. Is this input or output?

output

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

1. Does this line read the file?

No

2. What does `Path.of("students.txt")` represent?

It points to the position of the students.txt file

3. Why is a path like an address?

Refers to the location of a file

4. What is the difference between a path and the data inside the file?

path is the location of the file, the data is a seperate thing that is what makes up the file.


Complete:

```text
Path = location of external data
```

---

# 🌊 Part 11 — Streams as Pipes

A stream moves data gradually.

## Questions

1. Why might reading a whole file at once be a problem?

It will take a while to process, or a whole file might be too large to be held at once.

2. What kinds of data might be too large to load all at once?

Videos especially

3. Why is a stream like a pipe?

It groups data into bytes allowing it to flow into the system

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

1. Are streams byte-based or text-based?

bytes

2. What does an `InputStream` bring into Java memory?

bytes from files

3. What does an `OutputStream` send out?

Bytes from memory

4. Complete:

```text
InputStream = pipe coming in

OutputStream = pipe coming out
```

---

# ✍️ Part 13 — Readers and Writers

Fill in the table.

| Tool | Works With | Example Use |
|---|---|---|
| InputStream | incoming bytes | images |
| OutputStream | outgoing bytes | pdfs |
| Reader | incoming characters | .txt files |
| Writer | outgoing characters | .cvs files |

## Questions

1. When would a `Reader` be more natural than an `InputStream`?

When reading in files based on characters, like txt files

2. When would an `InputStream` be more natural than a `Reader`?

When reading in files based on bytes, like pdf files

3. Complete:

```text
Streams = bytes

Readers/Writers = characters
```

---

# 🪣 Part 14 — Buffers

A buffer is temporary memory used while moving data.

## Questions

1. Why is moving data one tiny piece at a time inefficient?

imagine carrying drops of water one by one from one location to another. 

2. Why is a buffer like a bucket?

collects chunks of data and moves it as a group

3. What does `BufferedReader` suggest?

A chunk of data has been collected and is incoming to the system

4. What does `BufferedWriter` suggest?

a chunk of data has been collected and is leaving the system

5. Complete:

```text
Buffer = temporary memory used to make I/O smoother
```

## Reflection

Explain this metaphor:

```text
Instead of carrying water drop by drop,
carry a bucket.
```
Instead of carrying data bit by bit or byte by byte or character by character, group chunks of the data together before moving it, making it more efficient.

---

# 🧬 Part 15 — Serialization and Deserialization

Complete the definitions:

```text
Serialization = object → transferable format

Deserialization = transferable format → object
```

## Questions

1. Why can’t Java memory objects directly travel across a network?

objects are how java intepret stuff, however that isnt the same across a network

2. Why do objects need to be converted before storage or transfer?



3. What formats can objects be serialized into?

json, text or bytes

4. Why is serialization important for APIs?

JSON formats can travel across networks, and API often bridges across networks information.

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

The java object

2. Which one crosses the network?

The JSON

3. What does Spring Boot often do automatically?

Covert from a java object to json and vice versa

4. Complete:

```text
Java object → JSON response

JSON request → Java object
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

```json
{
  "ingredients": ["tomato", "pasta", "cheese"]
}
```

2. What Java object represents the request?

```java
public record MealRequest(
    List<String> ingredients
) {}
```

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
