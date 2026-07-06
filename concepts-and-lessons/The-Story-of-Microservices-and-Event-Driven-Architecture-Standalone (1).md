# 🧩 The Story of Microservices and Event-Driven Architecture — From One Application to Reactive Systems

Most applications begin simply.

One frontend.

One backend.

One database.

One team.

One deployment.

One place to look when something breaks.

That is a good place to start.

But as a system grows, one big application can become harder to change, scale, deploy, and reason about.

At that point, architects begin asking new questions:

```text
Should this system stay as one application?

Should parts of it become separate services?

How should those services communicate?

Should one service call another directly?

Or should services react to events?
```

That is where microservices and event-driven architecture enter.

```text
Microservices = splitting a system into independently deployable services around business capabilities.

Event-driven architecture = letting services react to facts that happened instead of tightly controlling each other.
```

This document is standalone.

You do not need to understand Docker or AWS first.

We will start from the basic problem.

---

# 🧠 1️⃣ The Starting Point: One Application

Imagine Fridge2Meal as one backend application.

It contains:

```text
UserController
FridgeController
IngredientController
MealController
ImageController

UserService
FridgeService
IngredientService
MealService
ImageService

UserRepository
FridgeRepository
IngredientRepository
MealRepository
ImageRepository
```

The application might connect to one database:

```text
PostgreSQL
```

And the frontend talks to this backend:

```text
React Native / Expo frontend
        ↓
Spring Boot backend
        ↓
PostgreSQL database
```

This is a common early architecture.

It is easy to understand.

It is easy to run locally.

It is easy to teach.

It is usually the correct starting point.

---

# 🏛️ 2️⃣ What Is a Monolith?

A monolith is one application that contains many features.

Example:

```text
Fridge2Meal Backend
├── Users
├── Fridges
├── Ingredients
├── Image Upload
├── Meal Suggestions
├── Preferences
├── Saved Meals
└── Notifications
```

A monolith is not automatically bad.

A monolith simply means:

```text
Many parts of the system live inside one deployable application.
```

For small teams and early products, this is often good.

Benefits:

```text
simple to start
one codebase
one deployment
one database
easy local development
straightforward debugging
simple transactions
less infrastructure
```

A monolith becomes painful when it grows beyond what one application can comfortably handle.

---

# ⚠️ 3️⃣ When a Monolith Starts to Hurt

As the system grows, problems can appear.

```text
One small change requires redeploying the whole backend.

One bug can affect the whole application.

The codebase becomes tangled.

Different teams block each other.

Some features need more compute than others.

One part needs to scale but the whole app must scale with it.

Deployments become risky.

Debugging becomes slow.

The database becomes crowded with unrelated ownership.
```

Example:

```text
Image processing is heavy.

User profile management is light.

Meal suggestion may call AI services.

Notifications may run in the background.

Saved meals need normal database persistence.
```

These parts do not all have the same needs.

That pressure leads to service boundaries.

---

# 🧱 4️⃣ Before Microservices: Modular Monolith

Before splitting into microservices, a strong intermediate step is a modular monolith.

A modular monolith is still one application.

But the code is clearly separated into modules.

Example:

```text
fridge2meal-backend
├── user
├── fridge
├── ingredient
├── image
├── meal
└── notification
```

Each module has its own internal structure:

```text
controller
service
repository
dto
entity
exception
```

Example:

```text
ingredient
├── IngredientController
├── IngredientService
├── IngredientRepository
├── Ingredient
├── IngredientRequest
└── IngredientResponse
```

The modular monolith teaches the same boundary thinking as microservices, but without distributed complexity.

```text
Modular monolith = one deployment, clear internal boundaries.
```

This is usually the best bridge before microservices.

---

# 🧩 5️⃣ What Is a Microservice?

A microservice is an independently deployable service focused around a business capability.

Simple definition:

```text
Microservice = small independent service with a clear business responsibility.
```

Example services for Fridge2Meal:

```text
User Service
Fridge Service
Ingredient Service
Image Processing Service
Meal Suggestion Service
Notification Service
```

Each service may have:

```text
its own codebase
its own deployment
its own database or data ownership
its own logs
its own scaling rules
its own team ownership
its own API or event contracts
```

The key phrase is:

```text
independently deployable
```

If you cannot deploy it independently, it is probably not really a microservice yet.

---

# 🧠 6️⃣ Why Microservices Exist

Microservices exist because large systems create pressure.

A large system may need:

```text
different teams working independently
different parts scaling differently
different release speeds
clearer ownership
fault isolation
separate security boundaries
different technology choices
background processing
independent deployment
```

Example:

```text
Image Processing Service may need heavy CPU/GPU-style work.

User Service may only need normal database reads and writes.

Notification Service may need queues and retries.

Meal Suggestion Service may call external AI APIs.

Fridge Service may manage ingredient inventory.
```

These parts may evolve differently.

Microservices allow separation where separation is genuinely useful.

---

# ⚠️ 7️⃣ Microservices Are Not Automatically Better

Microservices solve some problems.

They create others.

Benefits:

```text
independent deployment
independent scaling
clear ownership
fault isolation
technology flexibility
smaller focused services
```

Costs:

```text
network communication
distributed debugging
data consistency problems
more deployment complexity
more infrastructure
more security boundaries
more monitoring needs
harder local development
more failure modes
```

A bad monolith is painful.

But badly designed microservices can be worse.

Beginner rule:

```text
Do not split because microservices sound modern.

Split when the boundary is real.
```

---

# 🧱 8️⃣ Service Boundaries

The most important microservices question is:

```text
Where should the boundary be?
```

A bad boundary creates constant communication between services.

A good boundary groups things that naturally belong together.

Bad split:

```text
Controller Service
Service Layer Service
Repository Service
```

This splits by technical layer.

That is usually wrong.

Better split:

```text
User Service
Ingredient Service
Image Processing Service
Meal Suggestion Service
Notification Service
```

This splits by business capability.

```text
Bad split = by code layer

Better split = by business capability
```

A service boundary should answer:

```text
What business capability does this own?

What data does it own?

What decisions does it make?

What events does it publish?

What requests does it accept?

What other services should not know about its internals?
```

---

# 🗄️ 9️⃣ Data Ownership

In a monolith, one database may contain all tables.

Example:

```text
users
fridges
ingredients
images
meals
recipes
preferences
```

In microservices, each service should own its own data.

Example:

```text
User Service owns user data.

Fridge Service owns fridge and ingredient inventory data.

Image Service owns image metadata.

Meal Service owns meal suggestion data.

Notification Service owns notification records.
```

Important rule:

```text
A service should not directly reach into another service’s database.
```

Bad:

```text
Meal Service directly queries User Service database tables.
```

Better:

```text
Meal Service asks User Service through an API
or reacts to an event published by User Service.
```

Microservices are not just separate code.

They are ownership boundaries.

```text
Service owns behaviour.

Service owns data.

Other services communicate through contracts.
```

---

# 🌐 1️⃣0️⃣ How Services Communicate

Services can communicate in two main ways:

```text
Synchronous communication

Asynchronous communication
```

---

## Synchronous Communication

One service calls another and waits for a response.

Example:

```text
Meal Service → HTTP call → User Service
```

Flow:

```text
Request
        ↓
Wait
        ↓
Response
```

Common tools:

```text
HTTP REST
GraphQL
gRPC
```

Benefits:

```text
simple to understand
immediate answer
easy request/response model
```

Weaknesses:

```text
caller must wait
failure can block the chain
services become tightly connected
slow services slow down callers
long call chains become fragile
```

Example chain:

```text
Frontend
        ↓
Meal Service
        ↓
Image Service
        ↓
AI Service
        ↓
Notification Service
```

If one link breaks, the whole chain may suffer.

---

## Asynchronous Communication

A service sends a message or event and does not wait for every result immediately.

Example:

```text
ImageUploaded event
        ↓
Image Processing Service reacts
        ↓
Meal Suggestion Service reacts later
```

Flow:

```text
Something happened
        ↓
Event/message published
        ↓
Interested services react
```

Common tools:

```text
queues
topics
event buses
streams
message brokers
```

Benefits:

```text
less tight coupling
better for background work
better for long-running processes
services can scale separately
new listeners can be added later
```

Weaknesses:

```text
more complex debugging
eventual consistency
needs retry handling
needs observability
message duplication can happen
ordering can be tricky
```

This leads to event-driven architecture.

---

# ⚡ 1️⃣1️⃣ What Is Event-Driven Architecture?

Event-driven architecture is a system style where services communicate by publishing and reacting to events.

Instead of one service saying:

```text
Do this now.
```

it says:

```text
This happened.
```

Other services decide whether they care.

```text
Command mindset:
Do this.

Event mindset:
This happened.
```

That shift matters.

A direct command creates control.

An event creates reaction.

```text
Command = instruction

Event = fact
```

Event-driven systems are built around facts.

---

# 🧠 1️⃣2️⃣ What Is an Event?

An event is a record that something happened.

Good event names are usually past tense.

Examples:

```text
UserRegistered
ImageUploaded
IngredientsDetected
MealSuggestionCreated
MealSaved
NotificationSent
PaymentFailed
```

Why past tense?

Because the event records something already true.

```text
ImageUploaded = the image upload happened.

MealSuggestionCreated = the meal suggestion was created.

UserRegistered = the user registered.
```

Bad event name:

```text
ProcessImage
```

That sounds like a command.

Better event name:

```text
ImageUploaded
```

That sounds like a fact.

---

# 📨 1️⃣3️⃣ Event Payload

An event usually carries useful data about what happened.

Example:

```json
{
  "eventType": "ImageUploaded",
  "imageId": "img_123",
  "userId": "user_456",
  "fridgeId": "fridge_789",
  "imageLocation": "uploads/fridge/img_123.jpg",
  "createdAt": "2026-07-06T10:30:00Z"
}
```

This event says:

```text
An image was uploaded.

Here is enough information for other services to react.
```

The event should carry enough context to be useful.

But it should not become a huge dump of everything in the system.

---

# 🔁 1️⃣4️⃣ Event-Driven Flow for Fridge2Meal

Imagine the image-to-meal feature as an event-driven flow.

```text
User uploads fridge image
        ↓
Image Service stores image
        ↓
ImageUploaded event is published
        ↓
Image Processing Service detects ingredients
        ↓
IngredientsDetected event is published
        ↓
Meal Suggestion Service creates suggestions
        ↓
MealSuggestionCreated event is published
        ↓
Notification Service may notify user
        ↓
Frontend can fetch result or receive update
```

Notice the difference.

One service is not controlling every other service.

Each service reacts to facts.

```text
ImageUploaded
        ↓
IngredientsDetected
        ↓
MealSuggestionCreated
```

The system becomes a chain of meaningful events.

---

# 🧩 1️⃣5️⃣ Why Events Fit Microservices

Microservices create boundaries.

Events reduce tight coupling across those boundaries.

Without events:

```text
Service A must know Service B directly.

Service A waits for Service B.

Service A may fail if Service B fails.

Adding Service C means changing Service A.
```

With events:

```text
Service A publishes that something happened.

Service B subscribes if it cares.

Service C can also subscribe later.

Service A does not need to know every future consumer.
```

This is loose coupling.

```text
Microservices split ownership.

Events loosen communication.
```

---

# 🧠 1️⃣6️⃣ Commands vs Events

## Command

A command asks something to happen.

```text
CreateMealSuggestion
SendNotification
ProcessImage
SaveIngredient
```

Command tone:

```text
Do this.
```

## Event

An event says something already happened.

```text
MealSuggestionCreated
NotificationSent
ImageProcessed
IngredientSaved
```

Event tone:

```text
This happened.
```

Why this matters:

```text
Commands create control relationships.

Events create reaction relationships.
```

A system with too many direct commands can become tightly chained.

Events allow services to react more independently.

---

# 📬 1️⃣7️⃣ Queues, Topics, Event Buses, and Streams

Event-driven systems need infrastructure to carry messages.

You do not need to memorise tools first.

Understand the shapes.

---

## Queue

A queue holds messages until a consumer processes them.

Mental model:

```text
Queue = task line
```

Use when:

```text
A job should be processed once.
```

Example:

```text
ImageProcessingQueue
```

Flow:

```text
ImageUploaded message
        ↓
Queue
        ↓
Image worker processes it
```

---

## Topic

A topic broadcasts a message to multiple subscribers.

Mental model:

```text
Topic = announcement channel
```

Use when:

```text
Many services may care about the same event.
```

Example:

```text
MealSuggestionCreated topic
        ↓
Notification Service
Analytics Service
Saved Meals Service
```

---

## Event Bus

An event bus routes events to targets based on rules.

Mental model:

```text
Event bus = central event router
```

Use when:

```text
Different event types should trigger different services.
```

Example:

```text
ImageUploaded → Image Processing Service

MealSaved → Analytics Service

UserRegistered → Welcome Email Service
```

---

## Stream

A stream stores ordered events over time.

Mental model:

```text
Stream = ordered event river
```

Use when:

```text
events are high-volume
ordering matters
analytics or replay matters
```

Streams are more advanced than simple queues or topics.

---

# ☁️ 1️⃣8️⃣ Where Cloud Platforms Fit

A cloud platform provides infrastructure for running systems.

Examples of cloud platforms:

```text
AWS
Azure
Google Cloud
```

A cloud platform can provide:

```text
servers
databases
file storage
networking
security
logs
queues
event buses
container runtimes
serverless functions
monitoring
deployment tools
```

So microservices and event-driven systems often use cloud services because they need:

```text
many running services
secure communication
message infrastructure
logs and metrics
scaling
retries
monitoring
deployment automation
```

Cloud is not required to understand the idea.

But cloud platforms make these architectures practical at scale.

---

# ☁️ 1️⃣9️⃣ AWS Examples for Event-Driven Systems

AWS is one cloud platform.

It has services that support event-driven architecture.

| Need | AWS Service | Mental Model |
|---|---|---|
| Process messages one at a time | SQS | queue |
| Broadcast to subscribers | SNS | topic |
| Route events by rules | EventBridge | event bus |
| Process event automatically | Lambda | event handler |
| Store files that trigger events | S3 Events | file event source |
| High-volume ordered event data | Kinesis | stream |
| Kafka-compatible streaming | MSK | managed Kafka |
| Coordinate workflows | Step Functions | state machine/workflow |
| Run always-on services | EC2 / ECS / EKS | compute/container runtime |
| Store data | RDS / DynamoDB | database |
| Store files | S3 | object storage |
| Observe logs/metrics | CloudWatch | visibility |

AWS is not the same thing as microservices.

AWS provides tools that can run microservices and event-driven systems.

---

# 🐳 2️⃣0️⃣ Where Docker Fits

Docker is a packaging tool.

It packages an application and its runtime into a container image.

Simple mental model:

```text
Docker = app runtime in a box
```

Docker is useful because a service can run consistently across environments.

```text
Developer laptop
        ↓
test environment
        ↓
production cloud
```

Microservices often use Docker because each service can be packaged and deployed separately.

But Docker is not required to understand microservices.

You can have:

```text
microservices without Docker

Docker without microservices

microservices with Docker
```

Docker packages services.

It does not decide service boundaries.

---

# 🔌 2️⃣1️⃣ Synchronous vs Event-Driven Fridge2Meal

## Synchronous Version

```text
Frontend uploads image
        ↓
Backend receives image
        ↓
Backend calls vision adapter
        ↓
Backend generates meal suggestion
        ↓
Backend returns response
```

Benefits:

```text
simple
easy to understand
easy for early course project
immediate response
```

Weaknesses:

```text
request may take too long
backend must coordinate everything
failure can block full request
harder to scale image processing separately
```

## Event-Driven Version

```text
Frontend uploads image
        ↓
ImageUploaded event
        ↓
Image worker processes image
        ↓
IngredientsDetected event
        ↓
Meal service creates suggestion
        ↓
MealSuggestionCreated event
        ↓
Frontend fetches result or receives notification
```

Benefits:

```text
services are less tightly coupled
long-running jobs can happen in background
parts can scale separately
new listeners can be added later
better for complex workflows
```

Weaknesses:

```text
more complex
harder debugging
eventual consistency
needs queues/event bus
requires strong observability
requires idempotency and retry handling
```

For learning, start synchronous.

Then explain why event-driven appears as systems mature.

---

# ⏳ 2️⃣2️⃣ Eventual Consistency

In a simple monolith, one transaction may update everything immediately.

In event-driven systems, services may update at different times.

Example:

```text
Image uploaded.
Ingredient detection not finished yet.
Meal suggestion not ready yet.
Notification not sent yet.
```

The system is moving toward consistency.

But not every part is updated at the exact same moment.

```text
Eventual consistency = the system becomes consistent after events finish propagating.
```

This affects UI.

The frontend may need states like:

```text
uploaded
processing
ingredients detected
suggestion ready
failed
```

Event-driven systems make time visible.

---

# 🧠 2️⃣3️⃣ Event-Driven State in the UI

If Fridge2Meal becomes event-driven, frontend state changes too.

Instead of:

```text
uploading → success
```

we may have:

```text
idle
        ↓
uploading
        ↓
uploaded
        ↓
processingImage
        ↓
detectingIngredients
        ↓
generatingMeal
        ↓
suggestionReady
```

Or failure states:

```text
imageProcessingFailed
mealGenerationFailed
timeout
```

The UI must represent the real process.

This connects directly to React and Redux.

```text
Backend events change system state.

Frontend state reflects where the process currently is.
```

---

# 🧪 2️⃣4️⃣ Why Event-Driven Systems Need Strong Testing

Event-driven systems have more moving parts.

You need to test:

```text
event is published
event has correct payload
consumer receives event
consumer handles duplicate event safely
consumer handles failure
retry works
dead-letter queue works
state eventually updates
logs show correlation across services
```

A simple endpoint test is not enough.

Testing must include flow over time.

---

# 🧨 2️⃣5️⃣ New Problems Event-Driven Architecture Creates

Event-driven architecture is powerful.

But it creates new responsibilities.

## Duplicate Events

The same event may be delivered more than once.

A service must handle this safely.

This is called idempotency.

```text
Idempotent handler = safe to run more than once.
```

## Failed Events

A consumer may fail.

The system needs:

```text
retries
dead-letter queues
alerts
logs
```

## Event Ordering

Events may not always arrive in the expected order.

The system must handle this or use infrastructure that supports ordering when required.

## Schema Changes

Events have shapes.

If the event payload changes, consumers may break.

This requires event schema discipline.

## Debugging

A request may become many events across services.

You need:

```text
correlation IDs
central logs
distributed tracing
clear event names
clear state transitions
```

Event-driven systems need strong visibility.

---

# 🔁 2️⃣6️⃣ Outbox Pattern

A common event-driven problem:

```text
What if the database save succeeds,
but publishing the event fails?
```

Example:

```text
Meal suggestion saved
but MealSuggestionCreated event not published.
```

Now the system is inconsistent.

One solution is the outbox pattern.

Simple idea:

```text
Save the business data and the event record in the same database transaction.

A separate process later publishes the event.
```

Flow:

```text
Service saves meal suggestion
        ↓
Service writes event to outbox table
        ↓
Transaction commits
        ↓
Outbox publisher reads event
        ↓
Event is published
        ↓
Consumers react
```

This helps connect database consistency with event publishing.

This is advanced, but students should know the problem exists.

---

# 🧠 2️⃣7️⃣ Event Sourcing

Event sourcing is a deeper version of event thinking.

Instead of storing only current state, the system stores every event that caused the state.

Normal state storage:

```text
current cart total = 30
```

Event-sourced storage:

```text
CartCreated
ItemAdded
ItemAdded
ItemRemoved
DiscountApplied
```

Current state is rebuilt from event history.

```text
State = result of events over time
```

Event sourcing is powerful, but complex.

Do not confuse event-driven architecture with event sourcing.

```text
Event-driven = services communicate using events.

Event sourcing = system stores events as the source of truth.
```

They can be used together, but they are not the same thing.

---

# 🧩 2️⃣8️⃣ Fridge2Meal Architecture Evolution

## Stage 1: Monolith

```text
One Spring Boot backend
One PostgreSQL database
React Native frontend
```

Good for learning.

## Stage 2: Modular Monolith

```text
One backend
Clear modules:
- user
- fridge
- ingredient
- meal
- image
```

Better internal boundaries.

Still one deployment.

## Stage 3: Service Extraction

```text
Image processing becomes separate service
Meal suggestion becomes separate service
Notification becomes separate service
```

Only extract where pressure is real.

## Stage 4: Event-Driven Services

```text
ImageUploaded
IngredientsDetected
MealSuggestionCreated
NotificationSent
```

Services react to events.

## Stage 5: Cloud-Native Event System

```text
services run on cloud compute
files stored in object storage
events carried through queues/topics/event bus
logs and metrics centralised
security controlled by permissions and network boundaries
```

The system has moved from one application to a network of services reacting to events.

---

# 🧠 2️⃣9️⃣ How This Connects to React and Redux

Frontend state management has a similar shape.

Redux actions say:

```text
cart/itemAdded
meal/uploadStarted
meal/uploadSucceeded
```

Backend/domain events say:

```text
ItemAddedToCart
ImageUploaded
MealSuggestionCreated
```

They are not the same thing.

But they share a discipline:

```text
Name the change.

Process the change through rules.

Make the system easier to trace.
```

Redux:

```text
Component dispatches action
        ↓
Reducer updates state
        ↓
UI redraws
```

Event-driven backend:

```text
Service publishes event
        ↓
Consumer reacts
        ↓
System state changes
```

Different layer.

Similar movement:

```text
from hidden change
to named, traceable change
```

---

# 🧭 3️⃣0️⃣ Teaching Sequence

Teach this in order:

```text
1. One application
2. Monolith
3. Modular monolith
4. Service boundary
5. Microservice
6. Synchronous HTTP communication
7. Problems with direct service chains
8. Event
9. Command vs event
10. Queue/topic/event bus
11. Event-driven flow
12. Eventual consistency
13. Cloud services for events
14. Observability and failure handling
```

This prevents students from thinking:

```text
Microservices = just make many tiny apps.
```

Better understanding:

```text
Microservices are about independent ownership boundaries.

Events are about decoupled communication across those boundaries.
```

---

# ⚠️ 3️⃣1️⃣ Common Beginner Confusions

## Microservices are not just small controllers

A microservice is an independently deployable business capability.

## Microservices are not always better

They add distributed complexity.

## Docker is not microservices

Docker packages services.

It does not decide service boundaries.

## Cloud is not microservices

Cloud platforms provide tools that can run microservices.

You still design the architecture.

## Events are not commands

Command:

```text
Do this.
```

Event:

```text
This happened.
```

## Event-driven does not mean instant consistency

Many event-driven systems are eventually consistent.

## A queue is not the same as a topic

Queue usually sends a message to one consumer.

Topic can broadcast to many subscribers.

## Event sourcing is not the same as event-driven architecture

Event-driven is communication.

Event sourcing is storage/history model.

---

# 🚀 Final Compression

```text
Monolith = one application containing many features

Modular monolith = one application with clear internal boundaries

Microservice = independently deployable service around a business capability

Service boundary = line around ownership of behaviour and data

Synchronous call = request and wait

Command = do this

Event = this happened

Event-driven architecture = services react to events

Queue = task line

Topic = announcement channel

Event bus = event router

Stream = ordered event river

Eventual consistency = system becomes consistent after events propagate

Cloud platform = infrastructure for running services

Docker = optional packaging tool
```

---

# 🌌 Ultimate Compression

```text
A monolith keeps the system together.

Microservices split ownership.

Events loosen the grip between services.
```

The old system says:

```text
Service A tells Service B what to do.
```

The event-driven system says:

```text
Service A announces what happened.
Other services decide how to react.
```

That is the move toward event-driven architecture.
