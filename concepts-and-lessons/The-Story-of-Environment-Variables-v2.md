# 🔐 The Story of Environment Variables — Keeping Secrets Out of Code ⚙️🧠

## Why this matters now

The cohort has started running into a real project problem:

```text
Different students have different database usernames and passwords.
```

One person may have:

```properties
spring.datasource.username=postgres
spring.datasource.password=admin
```

Another person may have:

```properties
spring.datasource.username=fridge_user
spring.datasource.password=password
```

Another person may have:

```properties
spring.datasource.username=my_local_user
spring.datasource.password=my_secret_password
```

If everyone commits their own values into `application.properties`, the project becomes unstable.

One student fixes the app for their machine.

Then another student pulls the code and the app breaks on theirs.

This is not just annoying.

This is a real software engineering problem.

```text
Machine-specific configuration should not be hardcoded into shared code.
```

That is where environment variables enter.

---

# 🧠 1️⃣ The Core Problem

A shared codebase should be shared.

But not everything inside an application is truly shared.

Some values are different depending on the machine, environment, or deployment target.

Examples:

```text
Database username
Database password
Database URL
API keys
Secret tokens
Email credentials
Cloud credentials
Server ports
External API URLs
```

These values are called configuration.

Some configuration is safe to share.

Some configuration is sensitive.

Some configuration is local to one person’s machine.

If we hardcode those values into the repo, we create three problems:

```text
1. Other people’s machines break.
2. Secrets can accidentally be exposed.
3. The app becomes harder to move between environments.
```

So the deeper rule is:

```text
Code should be shared.
Secrets and local configuration should be supplied from outside.
```

---

# 🧱 2️⃣ Code vs Configuration

A professional backend separates:

```text
Application code
```

from:

```text
Environment-specific configuration
```

Code is the logic of the application.

Configuration is the set of values the application needs in order to run somewhere.

Example code:

```java
userRepository.save(user);
```

This should be shared.

Example configuration:

```properties
spring.datasource.password=myLocalPassword123
```

This should not be hardcoded into a shared repo.

Clean distinction:

```text
Code = what the app does
Config = where/how the app runs
Secret = sensitive config that must be protected
```

---

# 🌍 3️⃣ Different Environments Need Different Values

An application may run in different places:

```text
Student laptop
Trainer laptop
Test environment
Docker container
Cloud server
Production environment
```

Each place may need different values.

Example:

| Environment | Database URL | Username | Password |
|---|---|---|---|
| Student A laptop | localhost | postgres | local password |
| Student B laptop | localhost | fridge_user | different password |
| Docker | db | fridge_user | docker password |
| AWS | cloud database URL | app user | secure secret |

The code should not change every time the environment changes.

Instead, the environment should provide the values.

That is the core purpose of environment variables.

---

# 🔑 4️⃣ What Is an Environment Variable?

An environment variable is a value provided by the operating system or runtime environment.

It sits outside the code.

Example:

```text
DB_USERNAME=fridge_user
DB_PASSWORD=password
DB_URL=jdbc:postgresql://localhost:5432/fridge2meal
```

The application can read these values when it starts.

So instead of writing the real password directly inside `application.properties`, we write a placeholder.

Example:

```properties
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```

Meaning:

```text
When the app starts,
look outside the code for DB_URL, DB_USERNAME, and DB_PASSWORD.
```

Now each student can have their own local values without changing the shared code.

---

# ⚙️ 5️⃣ How Spring Boot Reads Environment Variables

Spring Boot can resolve placeholders like this:

```properties
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```

If the environment contains:

```text
DB_URL=jdbc:postgresql://localhost:5432/fridge2meal
DB_USERNAME=fridge_user
DB_PASSWORD=password
```

Spring Boot substitutes them at runtime.

Conceptual flow:

```text
application.properties contains placeholders
        ↓
Spring Boot starts
        ↓
Spring checks environment variables
        ↓
Values are injected into configuration
        ↓
App connects to database
```

So the codebase stays stable.

Each machine supplies its own values.

---

# 🧪 6️⃣ Safe Local Defaults

Sometimes we want a fallback value.

Spring Boot allows this:

```properties
spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/fridge2meal}
spring.datasource.username=${DB_USERNAME:fridge_user}
spring.datasource.password=${DB_PASSWORD:password}
```

The format is:

```text
${VARIABLE_NAME:default_value}
```

Meaning:

```text
Use the environment variable if it exists.
If it does not exist, use the default value.
```

This is useful for local learning projects.

But be careful.

Do not put real production secrets as defaults.

For this cohort, local defaults may be acceptable.

For real systems, secrets should be injected securely.

---

# 🧾 7️⃣ Recommended Fridge2Meal application.properties

Use this pattern:

```properties
spring.application.name=fridge2meal-backend

spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/fridge2meal}
spring.datasource.username=${DB_USERNAME:fridge_user}
spring.datasource.password=${DB_PASSWORD:password}

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true

spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
```

This means:

```text
The shared repo has safe placeholders.
Each learner can override values locally.
```

If your local PostgreSQL username/password match the defaults, it works immediately.

If they do not, set environment variables on your machine.

---

# 🪟 8️⃣ Setting Environment Variables on Windows

## Temporary PowerShell variables

These work only in the current terminal session.

```powershell
$env:DB_URL="jdbc:postgresql://localhost:5432/fridge2meal"
$env:DB_USERNAME="fridge_user"
$env:DB_PASSWORD="password"
```

Then run the backend from the same PowerShell window:

```powershell
.\mvnw.cmd spring-boot:run
```

If you close the terminal, those temporary values disappear.

---

## Permanent user environment variables

In PowerShell:

```powershell
setx DB_URL "jdbc:postgresql://localhost:5432/fridge2meal"
setx DB_USERNAME "fridge_user"
setx DB_PASSWORD "password"
```

Then close and reopen IntelliJ/PowerShell so the new values are loaded.

Important:

```text
setx affects future terminals, not the current one.
```

---

# 🧠 9️⃣ Why Not Just Commit Everyone’s Password?

Because the repo is shared.

If one student commits their local database password, it may break everyone else.

If someone commits a real secret, it may expose sensitive data.

Bad pattern:

```properties
spring.datasource.username=mustapha_local_user
spring.datasource.password=myPersonalPassword
```

Good pattern:

```properties
spring.datasource.username=${DB_USERNAME:fridge_user}
spring.datasource.password=${DB_PASSWORD:password}
```

The shared code now says:

```text
I need a username and password,
but each environment can provide its own.
```

That is much cleaner.

---

# 🔒 1️⃣0️⃣ What Should Never Be Committed?

Avoid committing:

```text
Real passwords
API keys
JWT secrets
AWS access keys
Private tokens
Production database URLs
Personal credentials
.env files containing secrets
```

These should be kept outside the repo or stored in secure secret managers.

For this cohort, the key rule is:

```text
Do not commit personal machine-specific credentials.
```

---

# 📄 1️⃣1️⃣ The Role of .env Files

Some projects use `.env` files.

Example `.env`:

```env
DB_URL=jdbc:postgresql://localhost:5432/fridge2meal
DB_USERNAME=fridge_user
DB_PASSWORD=password
```

A `.env` file is a local configuration file.

It is useful because it keeps local values in one place.

But usually:

```text
.env should not be committed.
.env.example can be committed.
```

Example `.env.example`:

```env
DB_URL=jdbc:postgresql://localhost:5432/fridge2meal
DB_USERNAME=your_username_here
DB_PASSWORD=your_password_here
```

This gives learners a template without exposing real secrets.

Clean pattern:

```text
.env = real local values, ignored by Git
.env.example = safe template, committed
```

---

# 🧬 1️⃣2️⃣ The Deeper Pattern: A Small Fractal of Docker

This is where the idea gets deeper.

Environment variables are not just something Docker can use.

They are a smaller version of the same architectural principle that Docker later expresses at a larger scale.

The principle is:

```text
Separate the thing from the place it runs.
```

At the environment-variable level:

```text
Code stays the same.
Runtime values change.
```

At the Docker level:

```text
Application package stays the same.
Runtime environment becomes controlled and portable.
```

So environment variables are a small fractal of Docker’s deeper idea.

They both solve the same class of problem:

```text
How do we make software portable across different environments?
```

Environment variables solve it at the configuration-value level.

Docker solves it at the runtime-environment level.

Same pattern.

Different scale.

---

# 🐳 1️⃣3️⃣ Environment Variables as a Fractal of Docker

Docker says:

```text
Do not depend on one developer’s machine.

Package the app so it can run consistently elsewhere.
```

Environment variables say:

```text
Do not depend on one developer’s local credentials.

Supply machine-specific values from outside the code.
```

The shared structure is:

```text
Keep the core artifact stable.
Inject environment-specific details from outside.
```

At smaller scale:

```text
application.properties
        ↓
uses placeholders
        ↓
local machine supplies DB_USERNAME / DB_PASSWORD
```

At larger scale:

```text
Docker image
        ↓
contains packaged app
        ↓
container runtime supplies environment variables, ports, volumes, networks
```

So yes, environment variables can be taught as a fractal of Docker.

Not because Docker invented them.

Not because they only exist inside Docker.

But because they express the same portability pattern in miniature.

```text
Environment variables externalize configuration.

Docker externalizes and standardizes the whole runtime environment.
```

The first is the seed pattern.

The second is the larger containerized expression.

---

# 🧭 1️⃣4️⃣ Same Pattern Across Local, Docker, and Cloud

The same design appears at multiple levels.

| Level | Stable Thing | External Values |
|---|---|---|
| Local app | codebase | local DB username/password |
| Spring Boot app | `application.properties` placeholders | env vars |
| Docker container | image | env vars, ports, volumes, networks |
| Cloud service | deployed app artifact | secrets, config, managed DB URL |

This is the fractal:

```text
Stable core
        +
external runtime-specific configuration
        =
portable software
```

The details change.

The pattern repeats.

---

# 🧭 1️⃣5️⃣ Recommended Team Rule

For Fridge2Meal, use this team rule:

```text
Do not commit personal database usernames or passwords.

Shared configuration should use placeholders.

Each learner should provide local values through environment variables.
```

Recommended shared `application.properties`:

```properties
spring.application.name=fridge2meal-backend

spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/fridge2meal}
spring.datasource.username=${DB_USERNAME:fridge_user}
spring.datasource.password=${DB_PASSWORD:password}

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true

spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
```

Recommended local setup:

```powershell
$env:DB_URL="jdbc:postgresql://localhost:5432/fridge2meal"
$env:DB_USERNAME="your_local_username"
$env:DB_PASSWORD="your_local_password"
```

Then run:

```powershell
.\mvnw.cmd spring-boot:run
```

---

# 🧨 1️⃣6️⃣ Common Errors

## Error: password authentication failed

Your `DB_USERNAME` or `DB_PASSWORD` does not match PostgreSQL.

Check the values.

---

## Error: database does not exist

Your `DB_URL` points to a database that has not been created.

Create the database:

```sql
CREATE DATABASE fridge2meal;
```

---

## Error: environment variable not picked up

You may have set it in a different terminal.

Set the variable and run the backend from the same PowerShell session.

Or restart IntelliJ/PowerShell after using `setx`.

---

## Error: someone else’s database config broke my app

The repo probably contains hardcoded credentials.

Replace them with placeholders:

```properties
spring.datasource.username=${DB_USERNAME:fridge_user}
spring.datasource.password=${DB_PASSWORD:password}
```

---

# ✅ Completion Checklist

You are done when:

```text
application.properties uses environment placeholders
No personal DB password is committed
Your local environment variables are set
Backend starts successfully
Flyway connects successfully
Hibernate validates successfully
The same repo works across multiple students’ machines
```

---

# 🚀 Final Compression

```text
Code = shared application logic
Config = values the app needs to run
Secret = sensitive configuration
Environment variable = value supplied from outside the code
application.properties = Spring configuration file
Placeholder = ${VARIABLE_NAME}
Default value = ${VARIABLE_NAME:fallback}
.env = local values
.env.example = safe template
Docker = larger expression of portable runtime configuration
```

---

# 🧠 Ultimate Compression

```text
Hardcoded credentials make the codebase fragile.

Environment variables make the codebase portable.

Docker is the same portability principle scaled up from config values to runtime environments.
```

Environment variables are a small fractal of Docker:

```text
Keep the core stable.
Inject the environment from outside.
Run the same thing in different places.
```

Configuration is not an afterthought.

It is how software knows where it is.
