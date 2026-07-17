# PlantUML Syntax Reference

## General
All diagrams start with `@startuml` and end with `@enduml` (or `@startgantt`/`@endgantt` for Gantt).

## 1. Sequence Diagram

```plantuml
@startuml
title Order Flow

actor "Customer" as C
participant "Web" as W
participant "API" as A
database "DB" as DB

== Happy Path ==
C -> W: Place order
W -> A: POST /orders
A -> DB: INSERT
DB --> A: order_id
A --> W: 200 {order_id}
W --> C: Success

== Error Path ==
C -> W: Invalid card
W -> A: POST /orders
A --> W: 422 {error}
W --> C: Payment declined

note right of A: Validates payment\nbefore DB write

@enduml
```

Key syntax:
- `->` solid arrow (call)
- `-->` dashed arrow (return)
- `->>` async call
- `participant`, `actor`, `database`, `boundary`, `control`, `entity`, `collections`
- `== title ==` separator
- `note left/right of X: text`
- `alt/else/end` for conditions
- `loop N times/end` for loops
- `group label/end` for grouping
- `activate/deactivate X` for activation bars
- `|||` pause
- `destroy X` to end lifeline

## 2. Use Case Diagram

```plantuml
@startuml
title E-Commerce System

left to right direction

actor "Customer" as C
actor "Admin" as A

rectangle Store {
  (Browse Products)
  (Place Order)
  (Track Delivery)
}

rectangle Admin {
  (Manage Products)
  (View Reports)
}

C --> (Browse Products)
C --> (Place Order)
C --> (Track Delivery)
A --> (Manage Products)
A --> (View Reports)
A --> (Place Order)

@enduml
```

Key syntax:
- `actor` — actor
- `usecase` — use case (or just text in parens)
- `rectangle/lipackage` — system boundary
- `-->` — association
- `<|--` — generalization (inheritance)
- `left to right direction` — horizontal layout

## 3. Class Diagram

```plantuml
@startuml
title E-Commerce Domain

class Order {
  +id: UUID
  +status: Status
  +items: List<Item>
  +total: Money
  +createdAt: DateTime
  +place(): void
  +cancel(): void
}

class Customer {
  +name: String
  +email: String
  +orders: List<Order>
}

class Item {
  +productId: UUID
  +quantity: Int
  +price: Money
}

class Payment {
  +amount: Money
  +method: PaymentMethod
  +status: PaymentStatus
}

Order "1" --> "*" Item: contains
Customer "1" --> "*" Order: places
Order "1" --> "1" Payment: has
Payment <|-- CreditCardPayment
Payment <|-- PayPalPayment

enum Status {
  PENDING
  PAID
  SHIPPED
  DELIVERED
  CANCELLED
}

@enduml
```

Key syntax:
- `class X {}` — with fields/methods inside
- `+` public, `#` protected, `-` private
- `<|--` inheritance
- `*--` composition
- `o--` aggregation
- `-->` association
- `..>` dependency
- `interface X {}`
- `abstract class X {}`
- `enum X {}`
- Multiplicity: `"1"`, `"*"`, `"0..*"`, `"1..*"`

## 4. Activity Diagram

```plantuml
@startuml
title Order Processing

start

:Customer places order;
:Validate payment;

if (Payment OK?) then (yes)
  :Reserve stock;
  
  if (In stock?) then (yes)
    :Ship order;
    :Send confirmation;
  else (no)
    :Notify customer;
    :Refund payment;
  endif
  
else (no)
  :Decline order;
  :Notify customer;
endif

stop

@enduml
```

Key syntax:
- `start` / `stop` / `end`
- `:action;` — activity
- `if (cond) then (yes)/else (no)/endif` — branching
- `while (cond) is (body)/endwhile` — loops
- `repeat/while (cond) is (body)` — repeat-until
- `fork/end fork` — parallel
- `|#FF0000|` — color
- `partition` — swimlane
- `note right: text`
- `detach` / `kill`

## 5. Component Diagram

```plantuml
@startuml
title System Architecture

package "Frontend" {
  [React SPA] as frontend
}

package "Backend" {
  [API Gateway] as gateway
  [Order Service] as orders
  [Payment Service] as payments
  [User Service] as users
}

package "Data" {
  database "PostgreSQL" as db
  database "Redis" as cache
}

package "External" {
  [Payment Gateway] as pg
  [Email Service] as email
}

frontend --> gateway : HTTPS
gateway --> orders
gateway --> payments
gateway --> users
orders --> db
orders --> cache
payments --> pg
orders --> email : SMTP

@enduml
```

Key syntax:
- `[component]`
- `database "name"`
- `package "name" {}`
- `interface "name"`
- `-->` — connection
- `..>` — dependency

## 6. State Diagram

```plantuml
@startuml
title Order States

[*] --> PENDING : Order placed
PENDING --> PAID : Payment received
PENDING --> CANCELLED : User cancels
PAID --> SHIPPED : Fulfilled
SHIPPED --> DELIVERED : Confirmed
DELIVERED --> [*] : Completed
SHIPPED --> RETURNED : Return requested
RETURNED --> REFUNDED : Refund processed
CANCELLED --> [*]

PENDING : Awaiting payment
PAID : Payment confirmed
SHIPPED : In transit
DELIVERED : Received by customer

@enduml
```

Key syntax:
- `[*]` — start/end state
- `-->` — transition
- `:State description`
- `state "Name" as alias {}` — composite states
- `fork/end fork` — concurrent regions
- `hide empty description`

## 7. Deployment Diagram

```plantuml
@startuml
title Infrastructure

actor "User" as U

node "Browser" {
  [React App]
}

node "VPS" {
  [Nginx]
  [Node.js Server]
}

node "DB Server" {
  database "PostgreSQL"
}

cloud "AWS" {
  [S3 Bucket]
}

U --> [React App]
[React App] --> [Nginx] : HTTPS
[Nginx] --> [Node.js Server]
[Node.js Server] --> "PostgreSQL"
[Node.js Server] --> [S3 Bucket]

@enduml
```

Key syntax:
- `actor`, `node`, `cloud`, `database`, `queue`
- `folder`, `frame`, `artifact`, `component`
- Can nest nodes inside other nodes

## 8. Gantt Chart

```plantuml
@startgantt
title Project Timeline

Project starts 2026-07-01

[Analysis] lasts 5 days
[Design] lasts 7 days
[Development] lasts 14 days
[Testing] lasts 5 days
[Deploy] lasts 2 days

[Design] starts at [Analysis]'s end
[Development] starts at [Design]'s end
[Testing] starts at [Development]'s end
[Deploy] starts at [Testing]'s end

-- Milestones --
[MVP] happens at [Testing]'s end

@endgantt
```

Key syntax:
- `@startgantt` / `@endgantt`
- `[task] lasts N days`
- `[task2] starts at [task1]'s end`
- `-- separator --`
- `[milestone] happens at date`
- `{start}`, `{end}` for constraints
- Colors: `[#Red] task`

## Styling (skinparam)

```plantuml
skinparam backgroundColor Azure
skinparam componentStyle rectangle
skinparam defaultFontName Arial
skinparam defaultFontSize 12
skinparam arrowColor Blue
skinparam actorBorderColor Black
skinparam packageBackgroundColor White
```

Common skinparams:
- `backgroundColor`
- `componentStyle` — `rectangle` or `uml2`
- `monochrome true` — black and white
- `shadowing false` — disable shadows
- `roundcorner N` — corner radius
- `sequenceMessageAlign center/left/right`
- `maxMessageSize N`
- `titleFontSize N`
- `titleFontName Font`
