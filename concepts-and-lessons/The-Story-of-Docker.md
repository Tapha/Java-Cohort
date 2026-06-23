# 🐳 The Story of Docker — How Developers Package Runtime Reality 📦⚙️

The Fridge2Meal project now has moving parts.

The frontend runs with Node and Expo.

The backend runs with Java and Spring Boot.

The database runs with PostgreSQL.

Flyway creates the schema.

The API receives requests.

JUnit can prove behaviour.

But another problem now appears:

```text
What happens when the project works on one laptop
but fails on another?
```

One learner has Java 17.

Another has Java 21.

One learner has PostgreSQL running.

Another forgot the password.

One learner has the database on port 5432.

Another has a conflict.

One machine has the right dependencies.

Another machine does not.

This is the classic developer problem:

```text
It works on my machine.
```

Docker exists to reduce that problem.

Docker gives software a controlled runtime box.

```text
Docker = a way to package an application with the environment it needs to run.
```

---

# 🧠 1️⃣ The Real Problem: Runtime Drift

Code does not run in empty space.

Code runs inside an environment.

That environment includes:

```text
Operating system
Java version
Node version
PostgreSQL version
Environment variables
Ports
Files
Network access
Installed tools
Runtime configuration
```

If the environment changes, the app can break.

Same code.

Different machine.

Different result.

That is runtime drift.

```text
Runtime drift = the environment slowly becomes different across machines.
```

Docker helps by making the runtime more repeatable.

---

# 📦 2️⃣ Docker’s Core Idea

Docker lets us package software into containers.

A container is like a lightweight isolated runtime box.

Inside that box, the app gets the tools and configuration it expects.

```text
Host machine
        ↓
Docker
        ↓
Container
        ↓
Application runtime
```

So instead of saying:

```text
Install PostgreSQL manually.
Make sure the right version is running.
Make sure the port is correct.
Make sure the username/password match.
```

we can say:

```text
Run this Docker container.
```

That is the power.

---

# 🧱 3️⃣ Container vs Virtual Machine

A virtual machine is like running a whole computer inside your computer.

A container is lighter.

It shares the host operating system kernel, but isolates the application environment.

Simple mental model:

```text
Virtual Machine = whole simulated computer

Container = isolated runtime box for a process/app
```

For development, containers are useful because they are:

```text
repeatable
portable
disposable
fast to start
easy to recreate
```

You can destroy a container and rebuild it.

The source of truth is the configuration.

Not the mysterious state of one laptop.

---

# 🗄️ 4️⃣ Why Docker Matters for Fridge2Meal

Fridge2Meal currently depends on multiple pieces:

```text
Frontend
Backend
PostgreSQL
Flyway
Environment variables
Ports
```

The database is the first obvious Docker use case.

Instead of every learner manually installing and configuring PostgreSQL, the project can define a database container.

```text
Docker runs PostgreSQL
Spring Boot connects to it
Flyway migrates it
The backend uses it
```

That means the team can share the same database setup.

Same version.

Same port.

Same database name.

Same username.

Same password.

Same startup command.

This reduces chaos.

---

# 🧠 5️⃣ Image vs Container

Docker has two important words:

```text
Image
Container
```

An image is the blueprint.

A container is the running instance.

```text
Image = recipe / blueprint / class

Container = running object / instance
```

Java analogy:

```text
Class = blueprint
Object = running instance

Docker Image = blueprint
Docker Container = running instance
```

Example:

```text
postgres:16 = Docker image

running PostgreSQL database = Docker container
```

You can create many containers from the same image.

---

# 🧾 6️⃣ Dockerfile

A `Dockerfile` describes how to build an image.

It answers:

```text
What base environment should we start from?
What files should we copy in?
What dependencies should we install?
What command should run when the container starts?
```

Example for a Java backend:

```dockerfile
FROM eclipse-temurin:17-jdk

WORKDIR /app

COPY . .

RUN ./mvnw clean package -DskipTests

CMD ["java", "-jar", "target/fridge2meal-backend.jar"]
```

This says:

```text
Start from Java 17
Work inside /app
Copy the project
Build the project
Run the jar
```

The Dockerfile is like a recipe for the backend runtime.

---

# ⚙️ 7️⃣ Docker Compose

Docker Compose is used when an app needs multiple containers.

Fridge2Meal may need:

```text
backend container
postgres container
maybe frontend container later
```

Instead of running each manually, Compose defines them together.

```text
docker-compose.yml = multi-container project map
```

Example:

```yaml
services:
  postgres:
    image: postgres:16
    container_name: fridge2meal-postgres
    environment:
      POSTGRES_DB: fridge2meal
      POSTGRES_USER: fridge_user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - fridge2meal-postgres-data:/var/lib/postgresql/data

volumes:
  fridge2meal-postgres-data:
```

This gives the project a repeatable database runtime.

Run:

```bash
docker compose up
```

Stop:

```bash
docker compose down
```

---

# 🌊 8️⃣ Containers Are Disposable, Data Is Not

A container can be deleted.

But sometimes we want data to survive.

That is why Docker uses volumes.

```text
Container = disposable runtime
Volume = persistent storage
```

For PostgreSQL:

```yaml
volumes:
  - fridge2meal-postgres-data:/var/lib/postgresql/data
```

This means:

```text
PostgreSQL data should survive even if the container is restarted.
```

Without a volume, you may lose the database when the container is removed.

This is the key distinction:

```text
Container = process/runtime
Volume = remembered data
```

This connects to our memory story.

```text
Java memory disappears when the app stops.

Database data persists.

Docker containers can disappear.

Docker volumes preserve state.
```

---

# 🔌 9️⃣ Ports

A container has its own internal network.

To reach it from your machine, you map ports.

Example:

```yaml
ports:
  - "5432:5432"
```

This means:

```text
host port 5432 → container port 5432
```

PostgreSQL inside the container listens on 5432.

Your Spring Boot app connects through your machine’s 5432.

For a backend:

```yaml
ports:
  - "8080:8080"
```

This means:

```text
localhost:8080 on your machine
connects to port 8080 inside the backend container
```

Ports are doorways.

---

# 🔐 1️⃣0️⃣ Environment Variables

Docker lets us pass configuration into containers.

Example:

```yaml
environment:
  POSTGRES_DB: fridge2meal
  POSTGRES_USER: fridge_user
  POSTGRES_PASSWORD: password
```

Environment variables keep configuration outside code.

Spring Boot also uses configuration:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/fridge2meal
spring.datasource.username=fridge_user
spring.datasource.password=password
```

In a containerized system, the backend may connect to the database service name instead of `localhost`.

Example:

```properties
spring.datasource.url=jdbc:postgresql://postgres:5432/fridge2meal
```

Why `postgres`?

Because in Docker Compose, services can talk to each other using service names.

```text
backend container → postgres container
```

Inside Compose:

```text
postgres = network name for the database service
```

---

# 🧠 1️⃣1️⃣ The localhost Trap

This is one of the most common Docker confusions.

If Spring Boot runs directly on your laptop:

```text
localhost = your laptop
```

So this works:

```properties
jdbc:postgresql://localhost:5432/fridge2meal
```

But if Spring Boot runs inside a container:

```text
localhost = the backend container itself
```

So `localhost:5432` means:

```text
Look for PostgreSQL inside the backend container.
```

But PostgreSQL is in a different container.

So the backend should use:

```properties
jdbc:postgresql://postgres:5432/fridge2meal
```

This means:

```text
Connect to the postgres service container.
```

Important compression:

```text
localhost changes meaning depending on where the code is running.
```

---

# 🧱 1️⃣2️⃣ Docker and SOLID

Docker is not part of SOLID directly.

But the same principle appears again:

```text
Separate responsibilities.
Control boundaries.
Reduce hidden coupling.
```

In code:

```text
Controller should not do service logic.
Service should not directly depend on low-level implementation details.
```

In runtime:

```text
Application should not depend on mystery laptop setup.
Database should be a defined service.
Configuration should be explicit.
Runtime should be repeatable.
```

Docker gives runtime boundaries.

SOLID gives code boundaries.

Agile gives work boundaries.

JUnit gives behaviour proof.

```text
SOLID controls code structure.
Agile controls work structure.
JUnit controls behaviour proof.
Docker controls runtime environment.
```

---

# 🧪 1️⃣3️⃣ Docker and Testing

Docker helps testing because it can create known environments.

Example:

```text
Start PostgreSQL container
Run backend
Run tests
Destroy environment
```

This is powerful because tests become less dependent on one developer’s machine.

A future professional setup might use:

```text
Docker Compose
Test database
CI pipeline
Automated tests
```

For now, the main learning is:

```text
Docker can give the team the same database environment.
```

That alone is a major step.

---

# 🚀 1️⃣4️⃣ Fridge2Meal First Docker Target

The first useful Docker task is not containerizing everything.

The first useful Docker task is:

```text
Run PostgreSQL in Docker.
```

Why?

Because the database is the biggest setup pain.

Start with this:

```yaml
services:
  postgres:
    image: postgres:16
    container_name: fridge2meal-postgres
    environment:
      POSTGRES_DB: fridge2meal
      POSTGRES_USER: fridge_user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - fridge2meal-postgres-data:/var/lib/postgresql/data

volumes:
  fridge2meal-postgres-data:
```

Then run:

```bash
docker compose up -d
```

Check containers:

```bash
docker ps
```

Stop:

```bash
docker compose down
```

---

# 🧾 1️⃣5️⃣ What Happens With Flyway?

Flyway is still useful.

Docker starts the database.

Flyway creates/migrates the schema.

```text
Docker = database runtime exists

Flyway = database structure is created/updated
```

So the flow becomes:

```text
docker compose up -d
        ↓
PostgreSQL container starts
        ↓
Spring Boot starts
        ↓
Spring connects to PostgreSQL
        ↓
Flyway runs migrations
        ↓
Tables appear
```

Docker does not replace Flyway.

Docker gives the database a place to run.

Flyway gives the database its structure.

---

# 🧨 1️⃣6️⃣ Common Docker Errors

## Docker is not running

Error:

```text
Cannot connect to the Docker daemon
```

Fix:

```text
Start Docker Desktop.
```

## Port already in use

Error:

```text
port is already allocated
```

Meaning:

```text
Something else is already using that port.
```

Fix options:

```yaml
ports:
  - "5433:5432"
```

Then Spring connects to:

```properties
jdbc:postgresql://localhost:5433/fridge2meal
```

## Wrong password

Error:

```text
password authentication failed
```

Check:

```text
POSTGRES_USER
POSTGRES_PASSWORD
application.properties
```

## Existing volume has old credentials

PostgreSQL initializes credentials only when the volume is first created.

If you change username/password later, the old volume may still preserve old setup.

Fix:

```bash
docker compose down -v
docker compose up -d
```

Warning:

```text
docker compose down -v deletes the database volume.
Data will be lost.
```

Use carefully.

## Backend cannot connect to database

Ask:

```text
Is backend running on laptop or inside Docker?
```

If laptop:

```properties
localhost
```

If inside Compose:

```properties
postgres
```

---

# 🧭 1️⃣7️⃣ Docker Commands to Know

| Command | Meaning |
|---|---|
| `docker ps` | show running containers |
| `docker ps -a` | show all containers |
| `docker images` | show downloaded images |
| `docker compose up` | start services |
| `docker compose up -d` | start services in background |
| `docker compose down` | stop/remove services |
| `docker compose down -v` | stop/remove services and volumes |
| `docker logs <container>` | show container logs |
| `docker exec -it <container> bash` | enter a running container |
| `docker volume ls` | list volumes |

These are your first Docker survival commands.

---

# 🧠 1️⃣8️⃣ Docker Mental Model

Use this map:

```text
Dockerfile = recipe for one image

Image = blueprint

Container = running instance

Volume = persistent storage

Port mapping = doorway from host to container

Environment variable = runtime setting

Docker Compose = map of multiple services

Service name = network address inside Compose
```

That is enough to start thinking clearly.

---

# 📌 1️⃣9️⃣ Fridge2Meal Runtime Map

Without Docker:

```text
Laptop
├── Java installed manually
├── Node installed manually
├── PostgreSQL installed manually
├── Backend runs manually
└── Frontend runs manually
```

With Docker for PostgreSQL:

```text
Laptop
├── Java backend runs manually
├── Node frontend runs manually
└── Docker
    └── PostgreSQL container
```

Later with more Docker:

```text
Laptop
└── Docker Compose
    ├── backend container
    ├── postgres container
    └── maybe frontend container
```

Do not rush to containerize everything.

Start with the database.

Then expand.

---

# 🚦 2️⃣0️⃣ What Learners Should Be Ready to Do

After this doc, you should be ready to:

```text
Explain what Docker solves
Explain image vs container
Explain container vs volume
Explain port mapping
Explain why localhost can be confusing
Run PostgreSQL using Docker Compose
Connect Spring Boot to Dockerized PostgreSQL
Let Flyway create the schema
Debug common Docker/database connection errors
```

---

# 🚀 Final Compression

```text
Docker = controlled runtime packaging
Image = blueprint
Container = running instance
Volume = persistent data
Port = doorway
Environment variable = runtime configuration
Docker Compose = multi-service map
Runtime drift = works on one machine but not another
Flyway = schema control
PostgreSQL container = repeatable database runtime
```

---

# 🌌 Ultimate Compression

```text
SOLID gives code boundaries.

Agile gives work boundaries.

JUnit gives proof boundaries.

Docker gives runtime boundaries.
```

Docker is how a team stops the app from depending on the hidden mood of one laptop.
