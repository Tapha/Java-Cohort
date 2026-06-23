# ⚙️ The Story of Jenkins — How Teams Automate Proof Before Change Enters the System 🧪🚦

The Fridge2Meal project now has many moving parts.

You have code.

You have tickets.

You have pull requests.

You have JUnit tests.

You have Docker giving runtime boundaries.

You have a backend, frontend, database, and APIs.

Now a new problem appears:

```text
How do we stop broken code from entering the shared project?
```

One student runs tests locally.

Another forgets.

One machine has the right setup.

Another does not.

One pull request looks good.

But the app no longer builds.

One feature works.

But a previous feature breaks.

This is where Jenkins enters.

```text
Jenkins is an automation server that runs repeatable checks whenever code changes.
```

Jenkins is not just a tool.

Jenkins is the team’s automated proof machine.

---

# 🧠 1️⃣ The Real Problem: Humans Forget Checks

A good team may agree:

```text
Run the tests before pushing.
Run the app before merging.
Check the build.
Check the backend.
Check the frontend.
Check the Docker setup.
```

But humans forget.

Humans rush.

Humans assume.

Humans test only their own part.

A shared system needs a stronger guard.

```text
Jenkins automates the checks the team should not have to remember manually.
```

That is the core idea.

---

# 🔁 2️⃣ From Manual Proof to Automated Proof

Before Jenkins:

```text
Developer writes code
        ↓
Developer maybe runs tests
        ↓
Developer opens PR
        ↓
Reviewer maybe runs app
        ↓
Code gets merged
```

Risk:

```text
Maybe nobody ran the full check.
```

With Jenkins:

```text
Developer pushes code
        ↓
Jenkins detects change
        ↓
Jenkins checks out code
        ↓
Jenkins builds project
        ↓
Jenkins runs tests
        ↓
Jenkins reports pass/fail
        ↓
Team decides whether to merge
```

Jenkins turns proof into a repeatable pipeline.

---

# 🎟️ 3️⃣ Connection to Tickets, PRs, and Review

Earlier we said:

```text
Ticket = promise
PR = evidence
Review = proof check
Merge = shared system mutation
```

Jenkins strengthens that chain.

```text
Ticket = promise
Implementation = attempt
JUnit = executable proof
Docker = repeatable runtime
Jenkins = automated proof runner
```

A PR says:

```text
I believe this change is ready.
```

Jenkins answers:

```text
The project builds and the tests pass.
```

Or:

```text
The project does not build.
The tests fail.
Do not merge yet.
```

Jenkins gives the team an objective signal.

---

# 🧱 4️⃣ Jenkins as a Pipeline

A Jenkins pipeline is a sequence of automated steps.

Example:

```text
Checkout code
        ↓
Install dependencies
        ↓
Build backend
        ↓
Run backend tests
        ↓
Build frontend
        ↓
Run frontend checks
        ↓
Package app
        ↓
Maybe deploy
```

The pipeline is the delivery path written as code.

Instead of the team remembering the process, Jenkins runs the process.

```text
Pipeline = automated delivery checklist
```

---

# 🧾 5️⃣ Jenkinsfile

Jenkins pipelines are often defined in a file called:

```text
Jenkinsfile
```

This file lives in the repository.

That means the pipeline becomes part of the project.

Just like:

```text
pom.xml defines Java build dependencies
docker-compose.yml defines runtime services
Jenkinsfile defines automation steps
```

A simple Jenkinsfile might look like this:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh './mvnw clean package'
                }
            }
        }

        stage('Run Backend Tests') {
            steps {
                dir('backend') {
                    sh './mvnw test'
                }
            }
        }
    }
}
```

This says:

```text
Get the code.
Go into the backend folder.
Build the backend.
Run the tests.
```

---

# 🧠 6️⃣ Jenkins and Maven

For the Java backend, Jenkins will usually run Maven commands.

Common commands:

```bash
./mvnw clean
./mvnw test
./mvnw package
./mvnw clean package
```

What they mean:

| Command | Meaning |
|---|---|
| `clean` | remove old build output |
| `test` | run tests |
| `package` | compile and package app |
| `clean package` | clean, test, and package |

Important:

```text
Maven is the Java build tool.

Jenkins is the automation runner.

Jenkins tells Maven what to do.
```

So:

```text
Maven knows how to build Java.

Jenkins knows when and where to run the build.
```

---

# 🧪 7️⃣ Jenkins and JUnit

JUnit tests prove behaviour.

Jenkins runs those tests automatically.

Without Jenkins:

```text
Tests only run if a developer remembers.
```

With Jenkins:

```text
Tests run every time the pipeline is triggered.
```

This matters because tests become a gate.

```text
If tests pass, the change may continue.

If tests fail, the change should stop.
```

JUnit creates the proof.

Jenkins repeats the proof.

---

# 🐳 8️⃣ Jenkins and Docker

Docker gives runtime boundaries.

Jenkins can use Docker to create repeatable environments.

Example:

```text
Start PostgreSQL container
Run backend tests
Stop PostgreSQL container
```

A future pipeline could do:

```text
docker compose up -d
./mvnw test
docker compose down
```

That means Jenkins can test against a known PostgreSQL environment.

Docker reduces:

```text
works on my machine
```

Jenkins reduces:

```text
I forgot to check
```

Together:

```text
Docker gives repeatable runtime.

Jenkins gives repeatable process.
```

---

# 🚦 9️⃣ CI/CD

Jenkins is often used for CI/CD.

## CI = Continuous Integration

Continuous Integration means:

```text
Every code change is regularly integrated and checked.
```

CI asks:

```text
Does the code still build?

Do the tests still pass?

Can this change safely join the shared codebase?
```

## CD = Continuous Delivery / Deployment

Continuous Delivery means:

```text
The app can be prepared for release automatically.
```

Continuous Deployment means:

```text
The app can be deployed automatically after passing checks.
```

For this course, focus first on CI.

```text
CI first.
Deployment later.
```

The first goal is:

```text
Every PR should prove the backend still builds and tests pass.
```

---

# 🧭 1️⃣0️⃣ Jenkins in the Fridge2Meal Project

For Fridge2Meal, Jenkins can eventually check:

```text
Backend compiles
Backend tests pass
Frontend dependencies install
Frontend build/check passes
Docker Compose starts services
Flyway migrations run
API tests pass
```

But do not start with everything.

Start small.

First Jenkins goal:

```text
Run backend Maven tests automatically.
```

That means:

```text
Checkout repo
Go into backend
Run ./mvnw test
Report success/failure
```

This is enough to teach the core idea.

---

# 🧱 1️⃣1️⃣ The First Fridge2Meal Jenkins Pipeline

A beginner pipeline could be:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Backend Tests') {
            steps {
                dir('backend') {
                    sh './mvnw test'
                }
            }
        }
    }
}
```

If using Windows agents, the command may be:

```groovy
bat 'mvnw.cmd test'
```

So the Windows version:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Backend Tests') {
            steps {
                dir('backend') {
                    bat 'mvnw.cmd test'
                }
            }
        }
    }
}
```

Important:

```text
sh = Unix/Linux shell
bat = Windows batch command
```

---

# 🔍 1️⃣2️⃣ Pipeline Stages

Stages make the pipeline readable.

Examples:

```text
Checkout
Build
Test
Package
Docker
Deploy
```

A stage should have a clear responsibility.

This is like SRP again.

Bad pipeline stage:

```text
Do Everything
```

Better:

```text
Checkout
Build Backend
Run Backend Tests
Build Frontend
Run Frontend Checks
Package
```

Even pipelines need clean structure.

```text
SOLID mindset applies to automation too.
```

---

# 🧨 1️⃣3️⃣ What Happens When Jenkins Fails?

A Jenkins failure is not an insult.

It is a signal.

It might mean:

```text
The code does not compile.
A test failed.
A dependency is missing.
A path is wrong.
The wrong command was used.
The environment is not configured.
Docker is not available.
```

The right response is:

```text
Read the logs.
Find the failed stage.
Find the exact error.
Fix the cause.
Push again.
```

Jenkins gives feedback.

The logs are the map.

---

# 📜 1️⃣4️⃣ Jenkins Logs

When a pipeline fails, look for:

```text
Which stage failed?
Which command failed?
What was the exact error?
Was it compile, test, dependency, path, or environment?
```

Example:

```text
Stage: Backend Tests
Command: ./mvnw test
Error: Compilation failure
```

This means:

```text
The problem is in Java compilation, not Jenkins itself.
```

Example:

```text
mvnw: Permission denied
```

This means:

```text
The Maven wrapper script may not be executable on Linux.
```

Fix:

```bash
chmod +x mvnw
```

Then commit the permission change if needed.

---

# 🧠 1️⃣5️⃣ Jenkins Is Not Magic

Jenkins does not know your project automatically.

You must tell it:

```text
Where the code is
What commands to run
What counts as success
What should happen after failure
What environment is needed
```

If the Jenkinsfile is wrong, Jenkins will run the wrong process.

This is like a bad ticket.

Bad ticket:

```text
Do backend.
```

Bad Jenkinsfile:

```text
Run vague or wrong commands.
```

Good ticket gives clear work.

Good Jenkinsfile gives clear automation.

---

# 🧩 1️⃣6️⃣ Jenkins and the Shared Repo

When the Jenkinsfile is in the shared repo, the pipeline is visible to the whole team.

That means:

```text
Everyone can see how the project is checked.
Everyone can suggest improvements.
Everyone knows what must pass before merge.
```

The pipeline becomes part of team discipline.

```text
The repo contains code.

The repo contains tests.

The repo contains runtime config.

The repo contains delivery automation.
```

Professional projects keep these things close together.

---

# 🚧 1️⃣7️⃣ First Pipeline Goal for Learners

The first Jenkins learning target should be:

```text
Create a pipeline that runs backend JUnit tests.
```

Acceptance criteria:

```text
Jenkins can access the repo.
Jenkins can run the backend test command.
The pipeline shows pass when tests pass.
The pipeline shows fail when tests fail.
Students can read the console output.
```

Out of scope for first Jenkins task:

```text
Full deployment
Kubernetes
Cloud release
Complex credentials
Production database
Frontend deployment
```

Start with CI.

---

# 🔄 1️⃣8️⃣ Jenkins Flow With Pull Requests

In professional teams, Jenkins may run when:

```text
A branch is pushed.
A PR is opened.
A PR is updated.
Code is merged to main.
```

For the class, the mental model is:

```text
Student creates PR
        ↓
Peer review checks code and behaviour
        ↓
Jenkins checks build and tests
        ↓
Team decides whether to merge
```

Peer review and Jenkins are different.

Peer review checks judgement.

Jenkins checks repeatable commands.

Both are needed.

---

# 🧠 1️⃣9️⃣ Human Review vs Jenkins Review

| Human Reviewer | Jenkins |
|---|---|
| Checks clarity | Runs commands |
| Checks architecture | Runs tests |
| Checks naming | Builds project |
| Checks maintainability | Reports pass/fail |
| Checks ticket fit | Repeats process reliably |
| Gives judgement | Gives automated signal |

Jenkins does not replace developers.

Jenkins protects developers from forgetting mechanical checks.

---

# 📌 2️⃣0️⃣ Jenkins and Technical Debt

A pipeline can reveal technical debt.

Examples:

```text
Tests are flaky
Build depends on local files
Project only works in one IDE
Environment variables are hidden
Docker setup is not documented
Tests require manual database setup
```

These are not just Jenkins problems.

They are project maturity problems.

Jenkins exposes them.

That is good.

A failing pipeline can be painful, but it makes hidden fragility visible.

---

# 🧱 2️⃣1️⃣ How Jenkins Connects Everything So Far

```text
Agile = decides what work should happen
Ticket = defines expected change
Branch = isolates the attempt
JUnit = proves behaviour
Docker = stabilizes runtime
Pull Request = proposes the change
Review = human inspection
Jenkins = automated inspection
Merge = shared system mutation
```

Jenkins sits between PR and merge as an automated gate.

```text
No proof, no merge.
```

That is the professional instinct.

---

# 🧪 2️⃣2️⃣ Minimal Jenkinsfile Examples

## Linux/Mac Agent

```groovy
pipeline {
    agent any

    stages {
        stage('Backend Tests') {
            steps {
                dir('backend') {
                    sh './mvnw test'
                }
            }
        }
    }
}
```

## Windows Agent

```groovy
pipeline {
    agent any

    stages {
        stage('Backend Tests') {
            steps {
                dir('backend') {
                    bat 'mvnw.cmd test'
                }
            }
        }
    }
}
```

## Backend Build + Test

```groovy
pipeline {
    agent any

    stages {
        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh './mvnw clean package'
                }
            }
        }

        stage('Run Backend Tests') {
            steps {
                dir('backend') {
                    sh './mvnw test'
                }
            }
        }
    }
}
```

Note:

```text
If package already runs tests, you may not need a separate test stage.
But a separate test stage can be clearer for learners.
```

---

# ⚠️ 2️⃣3️⃣ Common Jenkins Beginner Errors

## Wrong folder

Error:

```text
pom.xml not found
```

Meaning:

```text
Jenkins is running Maven in the wrong directory.
```

Fix:

```groovy
dir('backend') {
    sh './mvnw test'
}
```

## Wrong command for operating system

Linux/Mac:

```groovy
sh './mvnw test'
```

Windows:

```groovy
bat 'mvnw.cmd test'
```

## Maven wrapper missing

Check:

```text
backend/mvnw
backend/mvnw.cmd
```

## Tests fail

This is not a Jenkins problem.

Read the test failure.

Fix the code or the test.

## Build works locally but not Jenkins

Possible causes:

```text
Hidden local environment variable
Wrong Java version
Missing file
Path difference
Case-sensitive file names
Different OS
```

Jenkins reveals environment assumptions.

---

# 🚦 2️⃣4️⃣ What Learners Should Be Ready to Do

After this doc, learners should be able to:

```text
Explain what Jenkins solves
Explain CI
Explain pipeline
Explain Jenkinsfile
Explain stages
Explain how Jenkins runs Maven tests
Explain Jenkins vs JUnit
Explain Jenkins vs Docker
Read a failed pipeline log
Create a simple backend test pipeline
Understand why Jenkins protects main branch
```

---

# 🚀 Final Compression

```text
Jenkins = automated proof runner
Pipeline = repeatable delivery checklist
Jenkinsfile = pipeline written as code
Stage = named step in the pipeline
CI = regularly checking integrated code
JUnit = creates executable proof
Maven = builds/tests Java project
Docker = repeatable runtime
Jenkins = runs the proof process automatically
```

---

# 🌌 Ultimate Compression

```text
Agile decides what should change.

JUnit proves behaviour.

Docker stabilizes runtime.

Jenkins repeats the proof every time code changes.
```

Jenkins is how the team stops relying on memory, mood, and luck before merging code.
