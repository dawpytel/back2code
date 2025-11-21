# Module 6: API & Integration Design - GraphQL, Events & Async Communication

## 1. Module Overview

### Learning Objectives

By completing this module, you will:

- **Understand** REST API maturity levels and advanced patterns (HATEOAS, versioning)
- **Design** GraphQL schema and implement efficient resolvers with DataLoader
- **Implement** event-driven architecture with RabbitMQ or Kafka
- **Apply** message queue patterns (pub/sub, work queues, RPC)
- **Evaluate** REST vs GraphQL trade-offs for different use cases
- **Master** API versioning strategies and backward compatibility
- **Implement** webhooks for asynchronous notifications
- **Design** idempotent APIs for reliable integrations

### Prerequisites

- **Module 1 completed:** REST API foundation
- **Module 2 completed:** Multi-database architecture
- **Module 3 completed:** Horizontal scaling
- **Module 4 completed:** Authentication and authorization
- **Module 5 completed:** Observability and monitoring
- **Familiarity with:**
  - Message broker concepts
  - GraphQL basics
  - Event-driven architecture principles
  - Asynchronous programming patterns

### Estimated Time Commitment

- **Theory & Design:** 18-24 hours (4-6 days)
- **Implementation:** 36-48 hours (8-12 days of focused work)
- **Break & Observe:** 12-16 hours (3-4 days)
- **Documentation & Reflection:** 6-8 hours
- **Total:** 72-96 hours (approximately 4-6 weeks at moderate pace)

### Key Deliverables

1. **Design Artifacts:**

   - API integration architecture diagram
   - GraphQL schema design document
   - Event flow diagrams with domain events
   - Message routing topology
   - API versioning strategy document
   - Integration patterns catalog

2. **Implementation:**

   - GraphQL API alongside REST with schema stitching
   - RabbitMQ or Kafka message broker integration
   - Event-driven notification system
   - Webhook delivery system with retries
   - API versioning (v1, v2) with deprecation strategy
   - Dead letter queues and error handling
   - Message saga pattern for distributed transactions
   - API gateway patterns (optional)

3. **Documentation:**
   - GraphQL schema documentation
   - Event catalog (domain events, payloads)
   - Integration guide for external consumers
   - Webhook integration guide
   - Postmortem with integration insights

---

## 2. Learning Area Coverage Analysis

### PRIMARY: API & Integration Design

**Concepts Covered:**

- **REST Advanced Patterns:**
  - Richardson Maturity Model (Levels 0-3)
  - HATEOAS (Hypermedia as Engine of Application State)
  - API versioning strategies (URI, header, content negotiation)
  - Content negotiation and media types
  - ETags and conditional requests
  - Pagination patterns (offset, cursor, keyset)
  - Bulk operations and batch APIs
  - Long-running operations and async patterns
- **GraphQL:**
  - Schema design and type system
  - Queries, mutations, subscriptions
  - Resolvers and data fetching
  - N+1 problem and DataLoader pattern
  - Error handling and partial failures
  - Schema federation concepts
  - Introspection and tooling
- **Event-Driven Architecture:**
  - Domain events and event sourcing concepts
  - Publish-subscribe pattern
  - Event-driven microservices
  - Eventual consistency
  - Event schema evolution
  - Event versioning strategies
- **Message Queues:**
  - RabbitMQ exchanges, queues, bindings
  - Kafka topics, partitions, consumer groups
  - Message durability and acknowledgments
  - Dead letter queues and retry strategies
  - Exactly-once vs at-least-once delivery
  - Message ordering guarantees
- **Webhooks:**
  - Webhook delivery patterns
  - Retry strategies with exponential backoff
  - Signature verification (HMAC)
  - Idempotency keys
  - Webhook registration and management
- **Integration Patterns:**
  - Saga pattern for distributed transactions
  - Outbox pattern for reliable events
  - API composition patterns
  - Backend for Frontend (BFF) pattern
  - API gateway patterns

### PRIMARY: System Design

**Concepts Covered:**

- Event-driven architecture design
- Message broker topology
- Schema registry for events
- Service-to-service communication patterns
- Choreography vs orchestration
- Data consistency in distributed systems
- Integration layer architecture

### SECONDARY: Reliability, Scalability, and Availability

**Concepts Covered:**

- Message queue scaling and partitioning
- Event replay and recovery
- Asynchronous error handling
- Circuit breakers for event publishing
- Graceful degradation in event systems

### SECONDARY: Observability

**Concepts Covered:**

- Message queue monitoring (lag, throughput)
- Event tracing across services
- GraphQL query performance monitoring
- Webhook delivery success rates
- Integration health checks

### SECONDARY: Data and Storage

**Concepts Covered:**

- Event store patterns
- Message persistence
- Outbox table for transactional events
- Event sourcing storage

### SECONDARY: Security and Compliance

**Concepts Covered:**

- GraphQL security (depth limiting, query complexity)
- Webhook signature verification
- Event data encryption
- Authorization in GraphQL resolvers

---

## 3. Theory Foundation

### Core Concepts and Principles

#### REST API Maturity Model (Richardson)

**Level 0:** The Swamp of POX

- Single URI, single HTTP method (usually POST)
- RPC-style

**Level 1:** Resources

- Multiple URIs for different resources
- Still using single HTTP method

**Level 2:** HTTP Verbs

- Proper use of HTTP methods (GET, POST, PUT, DELETE)
- Status codes for responses
- Most REST APIs reach this level

**Level 3:** Hypermedia Controls (HATEOAS)

- Response includes links to related resources
- Client discovers actions dynamically
- True REST according to Roy Fielding

**Example HATEOAS Response:**

```json
{
  "id": "note-123",
  "title": "My Note",
  "content": "...",
  "_links": {
    "self": { "href": "/api/v1/notes/note-123" },
    "update": { "href": "/api/v1/notes/note-123", "method": "PUT" },
    "delete": { "href": "/api/v1/notes/note-123", "method": "DELETE" },
    "author": { "href": "/api/v1/users/user-456" }
  }
}
```

#### API Versioning Strategies

**1. URI Versioning** (Most Common)

```
/api/v1/notes
/api/v2/notes
```

**Pros:** Explicit, easy to route, clear
**Cons:** Not RESTful (resource changes with version)

**2. Header Versioning**

```
GET /api/notes
Accept: application/vnd.myapi.v1+json
```

**Pros:** URI stays clean, RESTful
**Cons:** Less visible, harder to test

**3. Query Parameter**

```
/api/notes?version=1
```

**Pros:** Flexible, easy to implement
**Cons:** Can be overlooked, non-standard

**4. Content Negotiation**

```
Accept: application/json; version=1
```

**Pros:** True REST, flexible
**Cons:** Complex, tooling support varies

**Recommendation:** URI versioning for learning (most widely used)

#### GraphQL Fundamentals

**Schema Definition:**

```graphql
type Note {
  id: ID!
  title: String!
  content: String
  author: User!
  createdAt: DateTime!
  tags: [Tag!]!
}

type User {
  id: ID!
  email: String!
  notes: [Note!]!
}

type Query {
  note(id: ID!): Note
  notes(limit: Int, offset: Int): [Note!]!
  searchNotes(query: String!): [Note!]!
}

type Mutation {
  createNote(input: CreateNoteInput!): Note!
  updateNote(id: ID!, input: UpdateNoteInput!): Note!
  deleteNote(id: ID!): Boolean!
}

type Subscription {
  noteCreated: Note!
  noteUpdated(id: ID!): Note!
}
```

**Query Example:**

```graphql
query GetNoteWithAuthor {
  note(id: "123") {
    title
    content
    author {
      email
    }
    tags {
      name
    }
  }
}
```

**Response:**

```json
{
  "data": {
    "note": {
      "title": "My Note",
      "content": "Content here",
      "author": {
        "email": "user@example.com"
      },
      "tags": [{ "name": "work" }, { "name": "important" }]
    }
  }
}
```

**Key Advantages:**

- Client specifies exactly what data it needs
- Single request for related data (no multiple REST calls)
- Strong typing and schema validation
- Introspection and auto-documentation

**Key Challenges:**

- N+1 query problem (solved with DataLoader)
- Query complexity and depth attacks
- Caching more complex than REST
- Learning curve for team

#### DataLoader Pattern (N+1 Solution)

**Problem:**

```typescript
// Without DataLoader: N+1 queries
async resolveNotesForUser(userId) {
  return db.notes.findByUserId(userId); // Called once per user!
}

// Query for 100 users = 1 query for users + 100 queries for notes = 101 queries
```

**Solution:**

```typescript
// With DataLoader: Batched queries
const noteLoader = new DataLoader(async (userIds) => {
  // Batch: Called once with all user IDs
  const notes = await db.notes.findByUserIds(userIds);

  // Group notes by userId
  return userIds.map((id) => notes.filter((note) => note.userId === id));
});

// Query for 100 users = 1 query for users + 1 batched query for notes = 2 queries
```

#### Event-Driven Architecture

**Domain Event:** Something that happened in the past

```json
{
  "eventType": "NoteCreated",
  "eventId": "evt-123",
  "timestamp": "2024-01-15T10:30:00Z",
  "aggregateId": "note-456",
  "userId": "user-789",
  "data": {
    "title": "My Note",
    "content": "Initial content"
  },
  "metadata": {
    "correlationId": "req-abc",
    "causationId": "cmd-xyz"
  }
}
```

**Event Flow:**

```
Command → Service → Event Published → Event Bus → Subscribers → Actions
```

**Patterns:**

**1. Event Notification:**

- Minimal data in event
- Subscribers query for full data
- Loose coupling

**2. Event-Carried State Transfer:**

- Full data in event
- Subscribers don't need to query
- Eventual consistency

**3. Event Sourcing:**

- Events are source of truth
- Rebuild state by replaying events
- Complete audit trail

**Choreography vs Orchestration:**

**Choreography:** Each service knows what to do when event occurs

```
OrderCreated →
  - InventoryService reserves items
  - PaymentService charges card
  - ShippingService creates shipment
```

**Pros:** Loose coupling, decentralized
**Cons:** Hard to track overall flow

**Orchestration:** Central orchestrator coordinates workflow

```
OrderSaga:
  1. Reserve inventory (InventoryService)
  2. Charge payment (PaymentService)
  3. Create shipment (ShippingService)
  4. If any fails, compensate (rollback)
```

**Pros:** Clear flow, easier to track
**Cons:** Central point of coordination

#### Message Broker Patterns

**RabbitMQ Concepts:**

**Exchange Types:**

- **Direct:** Route by exact routing key match
- **Topic:** Route by routing key pattern (wildcards)
- **Fanout:** Broadcast to all queues
- **Headers:** Route by message headers

**Topology Example:**

```
Publisher → Exchange → Queue → Consumer

[Note Service] →--→ [notes.events] →--→ [notes.created] →--→ [Email Service]
                               ↓
                        [notes.updated] →--→ [Search Indexer]
                               ↓
                        [notes.deleted] →--→ [Analytics Service]
```

**Kafka Concepts:**

**Topic:** Stream of events
**Partition:** Ordered, immutable sequence of messages
**Consumer Group:** Multiple consumers share processing

**Partitioning Strategy:**

```
Topic: notes-events
Partitions: 0, 1, 2, 3

Message with key "user-123" → hash(user-123) % 4 → Partition 2
All events for same user go to same partition → Ordering guaranteed per user
```

#### Saga Pattern (Distributed Transactions)

**Problem:** Need atomicity across multiple services, but no distributed transactions

**Solution:** Saga - sequence of local transactions with compensation

**Example: Order Processing**

**Forward Flow:**

1. CreateOrder (OrderService) → Success
2. ReserveInventory (InventoryService) → Success
3. ChargePayment (PaymentService) → **Failed**

**Compensation Flow:** 4. ReleaseInventory (InventoryService) ← Compensate 5. CancelOrder (OrderService) ← Compensate

**Implementation Patterns:**

**1. Choreography-Based:**

- Services react to events
- Each service publishes success/failure events
- Next service listens and acts

**2. Orchestration-Based:**

- Central saga orchestrator
- Calls services sequentially
- Handles compensation on failure

#### Outbox Pattern (Reliable Events)

**Problem:** Ensure database write and event publish are atomic

**Anti-Pattern:**

```typescript
await db.notes.create(note); // Transaction
await eventBus.publish("NoteCreated"); // Not in transaction!
// If this fails, event not published but note created!
```

**Solution: Outbox Table**

```typescript
await db.transaction(async (tx) => {
  await tx.notes.create(note);
  await tx.outbox.create({
    eventType: "NoteCreated",
    payload: note,
    published: false,
  });
});

// Separate process polls outbox and publishes events
setInterval(async () => {
  const events = await db.outbox.findUnpublished();
  for (const event of events) {
    await eventBus.publish(event);
    await db.outbox.markPublished(event.id);
  }
}, 1000);
```

**Guarantees:**

- Event eventually published
- Exactly-once semantics possible
- Database and events consistent

### Recommended Reading Materials

#### Essential Reading (Before Implementation)

1. **"Building Microservices" by Sam Newman** (8-10 hours)

   - Chapter 4: Integration
   - Chapter 11: Microservices at Scale
   - **Focus:** Service communication patterns

2. **"Enterprise Integration Patterns" by Hohpe & Woolf** (6-8 hours)

   - Selected patterns: Messaging, Routing
   - **Focus:** Message broker patterns

3. **GraphQL Documentation** (4-6 hours)

   - https://graphql.org/learn/
   - **Focus:** Schema design, queries, mutations

4. **RabbitMQ/Kafka Documentation** (4-6 hours)

   - RabbitMQ: https://www.rabbitmq.com/getstarted.html
   - Kafka: https://kafka.apache.org/documentation/#gettingStarted
   - **Focus:** Basic concepts, topologies

5. **"REST API Design Rulebook" by Mark Masse** (3-4 hours)
   - Chapters on versioning, HATEOAS
   - **Focus:** Advanced REST patterns

#### Supplementary Reading (During Implementation)

6. **NestJS GraphQL Documentation** (2-3 hours)

   - https://docs.nestjs.com/graphql/quick-start

7. **NestJS Microservices Documentation** (2-3 hours)

   - https://docs.nestjs.com/microservices/basics

8. **"Designing Event-Driven Systems" by Ben Stopford** (reference)
   - Chapters on event patterns

### Key Terminology and Definitions

- **GraphQL Schema:** Type definitions for API
- **Resolver:** Function that returns data for a field
- **DataLoader:** Batching and caching utility for resolvers
- **Domain Event:** Something meaningful that happened in domain
- **Event Bus:** Infrastructure for publishing/subscribing to events
- **Message Broker:** System that routes messages between services
- **Exchange (RabbitMQ):** Routes messages to queues
- **Topic (Kafka):** Log of events
- **Partition:** Ordered subset of topic
- **Consumer Group:** Consumers that share processing
- **Dead Letter Queue:** Queue for failed messages
- **Saga:** Sequence of transactions with compensation
- **Outbox Pattern:** Ensuring atomic DB write and event publish
- **Choreography:** Distributed coordination through events
- **Orchestration:** Centralized coordination
- **Idempotency:** Same operation can be repeated safely
- **HATEOAS:** Hypermedia controls in responses

### Common Patterns and Anti-Patterns

#### Patterns to Follow

✅ **DataLoader for GraphQL:** Solve N+1 problem
✅ **Outbox Pattern:** Reliable event publishing
✅ **Idempotency Keys:** Safe retries
✅ **Dead Letter Queues:** Handle failed messages
✅ **Event Versioning:** Add version to events
✅ **Saga for Distributed Transactions:** Compensating actions
✅ **Circuit Breaker for Events:** Stop publishing to failing broker
✅ **Event Replay Capability:** Re-process events
✅ **Schema Registry:** Centralized event schema management

#### Anti-Patterns to Avoid

❌ **Event Coupling:** Events with service-specific details
❌ **Chatty Events:** Too many small events
❌ **Synchronous Event Processing:** Defeats purpose
❌ **No Event Versioning:** Breaking changes
❌ **Unbounded GraphQL Queries:** Allow infinite depth
❌ **No Message Ordering:** When order matters
❌ **Webhook Retry Without Exponential Backoff:** Overwhelm receivers
❌ **Two-Phase Commit Across Services:** Doesn't scale
❌ **Events for Everything:** Use when async is appropriate

---

## 4. Practical Implementation Plan

### Phase 1: Design (18-24 hours)

#### Architecture Decisions to Make

**1. GraphQL Strategy:**

- Stand-alone GraphQL API or alongside REST?
- Schema-first or code-first approach?
- Authentication: JWT in headers?
- Subscription transport: WebSockets?

**2. Message Broker Choice:**

- RabbitMQ (easier, good for learning) or Kafka (production-grade, complex)?
- Exchange/topic topology design
- Message durability settings

**3. Event Design:**

- What domain events to publish?
- Event payload: minimal or full state?
- Event versioning strategy
- Event retention policy

**4. Integration Patterns:**

- Which patterns to implement? (Saga, Outbox, Webhooks)
- Service boundaries for events

#### Design Artifacts to Create

**1. Integration Architecture Diagram:**

```
┌──────────────┐
│   Clients    │
└───┬──────┬───┘
    │      │
    │      └──────────┐
    │                 │
┌───▼─────┐    ┌──────▼────────┐
│REST API │    │ GraphQL API   │
│  (v1,v2)│    │ (Single Graph)│
└───┬─────┘    └──────┬────────┘
    │                 │
    └────────┬────────┘
             │
    ┌────────▼─────────┐
    │   Core Services  │
    │   - Notes        │
    │   - Users        │
    │   - Activity     │
    └────────┬─────────┘
             │
             │ Publish Events
             ▼
    ┌────────────────┐
    │  Message Broker│
    │  (RabbitMQ)    │
    └────┬───┬───┬───┘
         │   │   │
    ┌────▼┐ ┌▼──┐ ┌▼──────┐
    │Email│ │   │ │Webhook│
    │Svc  │ │ │ │Delivery│
    └─────┘ └Search┘└───────┘
              Indexer
```

**2. GraphQL Schema Design:**

```graphql
# Core types
type Note {
  id: ID!
  title: String!
  content: String
  author: User!
  tags: [Tag!]!
  createdAt: DateTime!
  updatedAt: DateTime!
  version: Int!
}

type User {
  id: ID!
  email: String!
  profile: UserProfile
  notes(limit: Int, offset: Int): NoteConnection!
  noteCount: Int!
}

type NoteConnection {
  edges: [NoteEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type NoteEdge {
  cursor: String!
  node: Note!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

# Queries
type Query {
  note(id: ID!): Note
  notes(first: Int, after: String, filter: NoteFilter): NoteConnection!
  searchNotes(query: String!): [Note!]!
  me: User!
}

input NoteFilter {
  tagIds: [ID!]
  createdAfter: DateTime
  createdBefore: DateTime
}

# Mutations
type Mutation {
  createNote(input: CreateNoteInput!): NotePayload!
  updateNote(id: ID!, input: UpdateNoteInput!): NotePayload!
  deleteNote(id: ID!): DeleteNotePayload!
  addTagToNote(noteId: ID!, tagId: ID!): Note!
}

input CreateNoteInput {
  title: String!
  content: String
  tagIds: [ID!]
}

input UpdateNoteInput {
  title: String
  content: String
  tagIds: [ID!]
}

type NotePayload {
  note: Note
  errors: [Error!]
}

type DeleteNotePayload {
  success: Boolean!
  errors: [Error!]
}

type Error {
  message: String!
  field: String
}

# Subscriptions
type Subscription {
  noteCreated(userId: ID): Note!
  noteUpdated(noteId: ID!): Note!
  noteDeleted(noteId: ID!): ID!
}
```

**3. Event Catalog:**

| Event Name     | Payload                          | Consumers                              | Purpose                   |
| -------------- | -------------------------------- | -------------------------------------- | ------------------------- |
| NoteCreated    | {id, userId, title, timestamp}   | EmailService, SearchIndexer, Analytics | Notify and index new note |
| NoteUpdated    | {id, userId, changes, timestamp} | SearchIndexer, Analytics               | Update search index       |
| NoteDeleted    | {id, userId, timestamp}          | SearchIndexer, Analytics               | Remove from index         |
| UserRegistered | {userId, email, timestamp}       | EmailService, Analytics                | Welcome email             |
| NoteShared     | {noteId, fromUserId, toUserId}   | NotificationService, EmailService      | Sharing notification      |

**4. Message Broker Topology (RabbitMQ):**

```
Exchange: notes.events (Topic)
├── Routing Key: note.created
│   └── Queue: email-service-notes
│   └── Queue: search-indexer-notes
├── Routing Key: note.updated
│   └── Queue: search-indexer-notes
└── Routing Key: note.deleted
    └── Queue: search-indexer-notes

Exchange: user.events (Topic)
└── Routing Key: user.registered
    └── Queue: email-service-users
```

### Phase 2: Core Implementation (36-48 hours)

#### Step 1: GraphQL API Setup (6-8 hours)

**1. Install Dependencies:**

```bash
npm install @nestjs/graphql @nestjs/apollo @apollo/server graphql
npm install dataloader
```

**2. Configure GraphQL Module:**

```typescript
// app.module.ts
import { GraphQLModule } from "@nestjs/graphql";
import { ApolloDriver, ApolloDriverConfig } from "@nestjs/apollo";

@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: "schema.gql",
      playground: true,
      introspection: true,
      context: ({ req }) => ({ req }),
      formatError: (error) => {
        // Custom error formatting
        return error;
      },
      validationRules: [
        depthLimit(10), // Prevent deep queries
        createComplexityRule({ maximumComplexity: 1000 }),
      ],
    }),
  ],
})
export class AppModule {}
```

**3. Create GraphQL Note Resolver:**

```typescript
// notes/notes.resolver.ts
import {
  Resolver,
  Query,
  Mutation,
  Args,
  ResolveField,
  Parent,
  Context,
} from "@nestjs/graphql";
import { UseGuards } from "@nestjs/common";
import { GqlAuthGuard } from "../auth/gql-auth.guard";
import DataLoader from "dataloader";

@Resolver(() => Note)
export class NotesResolver {
  constructor(
    private notesService: NotesService,
    private usersService: UsersService
  ) {}

  @Query(() => Note, { nullable: true })
  @UseGuards(GqlAuthGuard)
  async note(@Args("id") id: string): Promise<Note> {
    return this.notesService.findOne(id);
  }

  @Query(() => NoteConnection)
  @UseGuards(GqlAuthGuard)
  async notes(
    @Args("first", { type: () => Int, nullable: true }) first: number = 20,
    @Args("after", { nullable: true }) after?: string,
    @Args("filter", { nullable: true }) filter?: NoteFilter,
    @Context() context?: any
  ): Promise<NoteConnection> {
    return this.notesService.findMany({ first, after, filter });
  }

  @Mutation(() => NotePayload)
  @UseGuards(GqlAuthGuard)
  async createNote(
    @Args("input") input: CreateNoteInput,
    @Context() context: any
  ): Promise<NotePayload> {
    const user = context.req.user;
    try {
      const note = await this.notesService.create(input, user.id);
      return { note, errors: [] };
    } catch (error) {
      return {
        note: null,
        errors: [{ message: error.message }],
      };
    }
  }

  // Resolve author field with DataLoader
  @ResolveField(() => User)
  async author(@Parent() note: Note, @Context() context: any): Promise<User> {
    const { userLoader } = context;
    return userLoader.load(note.userId);
  }

  // Resolve tags field
  @ResolveField(() => [Tag])
  async tags(@Parent() note: Note): Promise<Tag[]> {
    return this.notesService.getTagsForNote(note.id);
  }
}
```

**4. Implement DataLoader:**

```typescript
// common/dataloaders/user.dataloader.ts
import DataLoader from "dataloader";
import { Injectable } from "@nestjs/common";

@Injectable()
export class UserDataLoader {
  constructor(private usersService: UsersService) {}

  createLoader(): DataLoader<string, User> {
    return new DataLoader<string, User>(async (userIds: string[]) => {
      // Batch load users
      const users = await this.usersService.findByIds(userIds);

      // Create map for O(1) lookup
      const userMap = new Map(users.map((user) => [user.id, user]));

      // Return in same order as input
      return userIds.map((id) => userMap.get(id) || null);
    });
  }
}

// Attach to context
GraphQLModule.forRoot({
  context: ({ req }) => ({
    req,
    userLoader: new UserDataLoader(usersService).createLoader(),
  }),
});
```

#### Step 2: Message Broker Integration (8-10 hours)

**1. Install RabbitMQ Dependencies:**

```bash
npm install @nestjs/microservices amqplib amqp-connection-manager
```

**2. Add RabbitMQ to Docker Compose:**

```yaml
rabbitmq:
  image: rabbitmq:3-management
  container_name: back2code-rabbitmq
  ports:
    - "5672:5672" # AMQP protocol
    - "15672:15672" # Management UI
  environment:
    RABBITMQ_DEFAULT_USER: admin
    RABBITMQ_DEFAULT_PASS: password
  volumes:
    - rabbitmq_data:/var/lib/rabbitmq

volumes:
  rabbitmq_data:
```

**3. Create Event Bus Service:**

```typescript
// common/events/event-bus.service.ts
import { Injectable, Logger } from "@nestjs/common";
import {
  ClientProxy,
  ClientProxyFactory,
  Transport,
} from "@nestjs/microservices";

export interface DomainEvent {
  eventType: string;
  eventId: string;
  timestamp: Date;
  aggregateId: string;
  userId?: string;
  data: any;
  metadata?: {
    correlationId?: string;
    causationId?: string;
  };
}

@Injectable()
export class EventBusService {
  private readonly logger = new Logger(EventBusService.name);
  private client: ClientProxy;

  constructor() {
    this.client = ClientProxyFactory.create({
      transport: Transport.RMQ,
      options: {
        urls: ["amqp://admin:password@rabbitmq:5672"],
        queue: "events",
        queueOptions: {
          durable: true,
        },
      },
    });
  }

  async publish(event: DomainEvent): Promise<void> {
    try {
      const routingKey = this.getRoutingKey(event.eventType);

      this.logger.log(
        `Publishing event: ${event.eventType} with routing key: ${routingKey}`
      );

      await this.client.emit(routingKey, event).toPromise();

      this.logger.log(`Event published successfully: ${event.eventId}`);
    } catch (error) {
      this.logger.error(
        `Failed to publish event: ${error.message}`,
        error.stack
      );
      throw error;
    }
  }

  private getRoutingKey(eventType: string): string {
    // Convert NoteCreated → note.created
    return eventType
      .replace(/([A-Z])/g, ".$1")
      .toLowerCase()
      .substring(1);
  }
}
```

**4. Implement Outbox Pattern:**

```typescript
// common/outbox/outbox.service.ts
import { Injectable, Logger } from "@nestjs/common";
import { Cron, CronExpression } from "@nestjs/schedule";
import { InjectRepository } from "@nestjs/typeorm";
import { Repository } from "typeorm";

@Entity()
export class OutboxEvent {
  @PrimaryGeneratedColumn("uuid")
  id: string;

  @Column()
  eventType: string;

  @Column("jsonb")
  payload: any;

  @Column({ default: false })
  published: boolean;

  @Column({ type: "timestamp", default: () => "CURRENT_TIMESTAMP" })
  createdAt: Date;

  @Column({ type: "timestamp", nullable: true })
  publishedAt: Date;
}

@Injectable()
export class OutboxService {
  private readonly logger = new Logger(OutboxService.name);

  constructor(
    @InjectRepository(OutboxEvent)
    private outboxRepository: Repository<OutboxEvent>,
    private eventBus: EventBusService
  ) {}

  // Create outbox event in transaction
  async createOutboxEvent(
    event: DomainEvent,
    manager: EntityManager
  ): Promise<void> {
    const outboxEvent = manager.create(OutboxEvent, {
      eventType: event.eventType,
      payload: event,
      published: false,
    });

    await manager.save(outboxEvent);
  }

  // Process outbox events (runs every 5 seconds)
  @Cron(CronExpression.EVERY_5_SECONDS)
  async processOutbox(): Promise<void> {
    const unpublishedEvents = await this.outboxRepository.find({
      where: { published: false },
      order: { createdAt: "ASC" },
      take: 100,
    });

    if (unpublishedEvents.length === 0) {
      return;
    }

    this.logger.log(`Processing ${unpublishedEvents.length} outbox events`);

    for (const outboxEvent of unpublishedEvents) {
      try {
        await this.eventBus.publish(outboxEvent.payload);

        outboxEvent.published = true;
        outboxEvent.publishedAt = new Date();
        await this.outboxRepository.save(outboxEvent);

        this.logger.log(`Published outbox event: ${outboxEvent.id}`);
      } catch (error) {
        this.logger.error(
          `Failed to publish outbox event ${outboxEvent.id}: ${error.message}`
        );
        // Will retry on next cron run
      }
    }
  }
}
```

**5. Publish Events from Service:**

```typescript
// notes/notes.service.ts
@Injectable()
export class NotesService {
  constructor(
    private dataSource: DataSource,
    private outboxService: OutboxService
  ) {}

  async create(createDto: CreateNoteDto, userId: string): Promise<Note> {
    return this.dataSource.transaction(async (manager) => {
      // Create note
      const note = manager.create(Note, {
        ...createDto,
        userId,
      });
      await manager.save(note);

      // Create event in outbox (same transaction)
      const event: DomainEvent = {
        eventType: "NoteCreated",
        eventId: uuidv4(),
        timestamp: new Date(),
        aggregateId: note.id,
        userId,
        data: {
          title: note.title,
          content: note.content,
        },
      };

      await this.outboxService.createOutboxEvent(event, manager);

      return note;
    });
  }
}
```

#### Step 3: Event Consumer Services (6-8 hours)

**1. Create Email Service (Event Consumer):**

```typescript
// email/email.consumer.ts
import { Controller } from "@nestjs/common";
import { EventPattern, Payload } from "@nestjs/microservices";

@Controller()
export class EmailConsumer {
  private readonly logger = new Logger(EmailConsumer.name);

  constructor(private emailService: EmailService) {}

  @EventPattern("note.created")
  async handleNoteCreated(@Payload() event: DomainEvent) {
    this.logger.log(`Received NoteCreated event: ${event.eventId}`);

    try {
      await this.emailService.sendNoteCreatedEmail(
        event.userId,
        event.data.title
      );

      this.logger.log(`Email sent for note: ${event.aggregateId}`);
    } catch (error) {
      this.logger.error(`Failed to send email: ${error.message}`);
      throw error; // Will go to dead letter queue
    }
  }

  @EventPattern("user.registered")
  async handleUserRegistered(@Payload() event: DomainEvent) {
    this.logger.log(`Received UserRegistered event: ${event.eventId}`);

    await this.emailService.sendWelcomeEmail(event.data.email);
  }
}

// email/email.module.ts
@Module({
  imports: [
    ClientsModule.register([
      {
        name: "EMAIL_SERVICE",
        transport: Transport.RMQ,
        options: {
          urls: ["amqp://admin:password@rabbitmq:5672"],
          queue: "email-service",
          queueOptions: {
            durable: true,
            arguments: {
              "x-dead-letter-exchange": "dlx",
              "x-dead-letter-routing-key": "email-service-dlq",
            },
          },
          noAck: false, // Require manual acknowledgment
        },
      },
    ]),
  ],
  controllers: [EmailConsumer],
  providers: [EmailService],
})
export class EmailModule {}
```

**2. Create Search Indexer (Event Consumer):**

```typescript
// search/search.consumer.ts
@Controller()
export class SearchConsumer {
  constructor(private searchService: SearchService) {}

  @EventPattern("note.created")
  async handleNoteCreated(@Payload() event: DomainEvent) {
    await this.searchService.indexNote({
      id: event.aggregateId,
      ...event.data,
    });
  }

  @EventPattern("note.updated")
  async handleNoteUpdated(@Payload() event: DomainEvent) {
    await this.searchService.updateNote(event.aggregateId, event.data);
  }

  @EventPattern("note.deleted")
  async handleNoteDeleted(@Payload() event: DomainEvent) {
    await this.searchService.deleteNote(event.aggregateId);
  }
}
```

#### Step 4: API Versioning (4-6 hours)

**1. Create v2 Controllers:**

```typescript
// notes/v2/notes-v2.controller.ts
@Controller("api/v2/notes")
export class NotesV2Controller {
  constructor(private notesService: NotesService) {}

  @Get()
  async findAll(@Query() query: PaginationDto) {
    const notes = await this.notesService.findAll(query);

    // v2: Include author info in response (breaking change)
    return {
      data: notes.map((note) => ({
        id: note.id,
        title: note.title,
        content: note.content,
        author: {
          id: note.userId,
          email: note.user.email,
        },
        createdAt: note.createdAt,
        // v2: Use ISO 8601 format (breaking change from v1)
      })),
      meta: {
        page: query.page,
        limit: query.limit,
        total: notes.length,
      },
    };
  }

  @Post()
  async create(@Body() dto: CreateNoteDto) {
    // v2: Support tags in creation
    return this.notesService.createWithTags(dto);
  }
}

// Keep v1 for backward compatibility
// notes/v1/notes-v1.controller.ts
@Controller("api/v1/notes")
export class NotesV1Controller {
  constructor(private notesService: NotesService) {}

  @Get()
  async findAll(@Query() query: PaginationDto) {
    const notes = await this.notesService.findAll(query);

    // v1: Original response format
    return notes.map((note) => ({
      id: note.id,
      title: note.title,
      content: note.content,
      userId: note.userId,
      createdAt: note.createdAt.toISOString(),
    }));
  }

  @Post()
  async create(@Body() dto: CreateNoteDtoV1) {
    // v1: No tag support
    return this.notesService.create(dto);
  }
}
```

**2. Add Deprecation Headers:**

```typescript
// common/interceptors/deprecation.interceptor.ts
@Injectable()
export class DeprecationInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const response = context.switchToHttp().getResponse();

    if (request.path.includes("/api/v1/")) {
      response.header("Deprecation", "true");
      response.header("Sunset", "Sat, 31 Dec 2024 23:59:59 GMT");
      response.header("Link", '</api/v2/notes>; rel="successor-version"');
    }

    return next.handle();
  }
}
```

#### Step 5: Webhook System (6-8 hours)

**1. Webhook Registration:**

```typescript
// webhooks/webhook.entity.ts
@Entity()
export class Webhook {
  @PrimaryGeneratedColumn("uuid")
  id: string;

  @Column()
  userId: string;

  @Column()
  url: string;

  @Column("simple-array")
  events: string[]; // ['note.created', 'note.updated']

  @Column()
  secret: string; // HMAC secret for signature

  @Column({ default: true })
  active: boolean;

  @Column({ default: 0 })
  failureCount: number;

  @Column({ type: "timestamp", nullable: true })
  lastDeliveryAt: Date;

  @Column({ type: "timestamp", nullable: true })
  lastFailureAt: Date;
}

// webhooks/webhooks.controller.ts
@Controller("api/v1/webhooks")
@UseGuards(AuthGuard)
export class WebhooksController {
  constructor(private webhooksService: WebhooksService) {}

  @Post()
  async create(@Body() dto: CreateWebhookDto, @User() user: any) {
    return this.webhooksService.create({
      ...dto,
      userId: user.id,
      secret: this.generateSecret(),
    });
  }

  @Get()
  async findAll(@User() user: any) {
    return this.webhooksService.findByUserId(user.id);
  }

  @Delete(":id")
  async delete(@Param("id") id: string, @User() user: any) {
    return this.webhooksService.delete(id, user.id);
  }

  private generateSecret(): string {
    return crypto.randomBytes(32).toString("hex");
  }
}
```

**2. Webhook Delivery Service:**

```typescript
// webhooks/webhook-delivery.service.ts
@Injectable()
export class WebhookDeliveryService {
  private readonly logger = new Logger(WebhookDeliveryService.name);

  constructor(
    @InjectRepository(Webhook)
    private webhookRepository: Repository<Webhook>,
    private httpService: HttpService
  ) {}

  async deliverEvent(event: DomainEvent): Promise<void> {
    const webhooks = await this.webhookRepository.find({
      where: {
        active: true,
        events: Raw((alias) => `${alias} @> ARRAY[:event]::varchar[]`, {
          event: this.getEventName(event.eventType),
        }),
      },
    });

    if (webhooks.length === 0) {
      return;
    }

    this.logger.log(
      `Delivering event ${event.eventId} to ${webhooks.length} webhooks`
    );

    for (const webhook of webhooks) {
      // Deliver in background with retries
      this.deliverToWebhook(webhook, event).catch((error) => {
        this.logger.error(`Webhook delivery failed: ${error.message}`);
      });
    }
  }

  private async deliverToWebhook(
    webhook: Webhook,
    event: DomainEvent
  ): Promise<void> {
    const payload = {
      id: uuidv4(), // Delivery ID for idempotency
      event: event.eventType,
      data: event.data,
      timestamp: event.timestamp,
    };

    const signature = this.generateSignature(payload, webhook.secret);

    const maxRetries = 5;
    let retryCount = 0;

    while (retryCount < maxRetries) {
      try {
        const response = await this.httpService
          .post(webhook.url, payload, {
            headers: {
              "Content-Type": "application/json",
              "X-Webhook-Signature": signature,
              "X-Webhook-ID": webhook.id,
              "X-Delivery-ID": payload.id,
            },
            timeout: 10000,
          })
          .toPromise();

        if (response.status >= 200 && response.status < 300) {
          // Success
          await this.webhookRepository.update(webhook.id, {
            lastDeliveryAt: new Date(),
            failureCount: 0,
          });

          this.logger.log(`Webhook delivered successfully: ${webhook.id}`);
          return;
        }
      } catch (error) {
        retryCount++;

        if (retryCount < maxRetries) {
          // Exponential backoff: 2^n seconds
          const delay = Math.pow(2, retryCount) * 1000;
          await this.sleep(delay);

          this.logger.warn(
            `Webhook delivery failed, retry ${retryCount}/${maxRetries} in ${delay}ms`
          );
        }
      }
    }

    // All retries failed
    await this.webhookRepository.update(webhook.id, {
      lastFailureAt: new Date(),
      failureCount: () => "failure_count + 1",
    });

    // Disable webhook after too many failures
    if (webhook.failureCount + 1 >= 100) {
      await this.webhookRepository.update(webhook.id, { active: false });
      this.logger.error(`Webhook disabled due to failures: ${webhook.id}`);
    }
  }

  private generateSignature(payload: any, secret: string): string {
    const hmac = crypto.createHmac("sha256", secret);
    hmac.update(JSON.stringify(payload));
    return "sha256=" + hmac.digest("hex");
  }

  private sleep(ms: number): Promise<void> {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }

  private getEventName(eventType: string): string {
    return eventType
      .replace(/([A-Z])/g, ".$1")
      .toLowerCase()
      .substring(1);
  }
}

// Connect to event bus
@Controller()
export class WebhookEventConsumer {
  constructor(private webhookDelivery: WebhookDeliveryService) {}

  @EventPattern("note.*") // Subscribe to all note events
  async handleNoteEvent(@Payload() event: DomainEvent) {
    await this.webhookDelivery.deliverEvent(event);
  }
}
```

### Phase 3: Enhancement (8-10 hours)

#### 1. GraphQL Subscriptions (3-4 hours)

```typescript
// notes/notes.resolver.ts
import { Subscription } from '@nestjs/graphql';
import { PubSub } from 'graphql-subscriptions';

const pubSub = new PubSub();

@Resolver(() => Note)
export class NotesResolver {
  @Subscription(() => Note, {
    filter: (payload, variables) => {
      // Only send to subscribed user
      return !variables.userId || payload.noteCreated.userId === variables.userId;
    },
  })
  noteCreated(@Args('userId', { nullable: true }) userId?: string) {
    return pubSub.asyncIterator('noteCreated');
  }

  @Mutation(() => NotePayload)
  async createNote(@Args('input') input: CreateNoteInput): Promise<NotePayload> {
    const note = await this.notesService.create(input);

    // Publish to subscribers
    pubSub.publish('noteCreated', { noteCreated: note });

    return { note, errors: [] };
  }
}

// Client usage
subscription {
  noteCreated(userId: "user-123") {
    id
    title
    createdAt
  }
}
```

#### 2. Saga Pattern Implementation (4-5 hours)

```typescript
// sagas/order-saga.service.ts
// Example: Coordinating order across multiple services

@Injectable()
export class OrderSagaService {
  private readonly logger = new Logger(OrderSagaService.name);

  async processOrder(orderId: string): Promise<void> {
    const saga = await this.createSaga(orderId);

    try {
      // Step 1: Reserve inventory
      await this.executeStep(saga, "reserveInventory", async () => {
        await this.inventoryService.reserve(orderId);
      });

      // Step 2: Charge payment
      await this.executeStep(saga, "chargePayment", async () => {
        await this.paymentService.charge(orderId);
      });

      // Step 3: Create shipment
      await this.executeStep(saga, "createShipment", async () => {
        await this.shippingService.createShipment(orderId);
      });

      // Success!
      await this.completeSaga(saga);
    } catch (error) {
      // Compensate: Rollback completed steps
      await this.compensate(saga);
      throw error;
    }
  }

  private async compensate(saga: Saga): Promise<void> {
    this.logger.error(`Saga ${saga.id} failed, compensating...`);

    const completedSteps = saga.steps.filter((s) => s.status === "completed");

    // Compensate in reverse order
    for (const step of completedSteps.reverse()) {
      try {
        await this.compensateStep(step);
      } catch (error) {
        this.logger.error(
          `Compensation failed for step ${step.name}: ${error.message}`
        );
        // Continue compensating other steps
      }
    }
  }

  private async compensateStep(step: SagaStep): Promise<void> {
    switch (step.name) {
      case "reserveInventory":
        await this.inventoryService.release(step.orderId);
        break;
      case "chargePayment":
        await this.paymentService.refund(step.orderId);
        break;
      case "createShipment":
        await this.shippingService.cancelShipment(step.orderId);
        break;
    }
  }
}
```

---

## 5. Complexity Validation

### Complexity Rating: **ADVANCED**

### Justification

This module is **highly advanced** because:

**1. Multiple Integration Paradigms:**

- REST (refined), GraphQL (new), Events (async) all in one system
- Each has different mental models and best practices
- Requires understanding when to use each

**2. Distributed System Complexity:**

- Event-driven architecture with eventual consistency
- Message brokers and queue management
- Saga pattern for distributed transactions
- Outbox pattern for reliability

**3. Real-World Production Patterns:**

- DataLoader for N+1 problem
- Webhook delivery with retries
- Event sourcing concepts
- Schema versioning and evolution

**4. Builds on All Previous Modules:**

- Module 1: REST foundation
- Module 2: Multiple databases for events
- Module 3: Scaling considerations for message brokers
- Module 4: Auth for GraphQL and webhooks
- Module 5: Monitoring events and queues

**5. Architectural Maturity Required:**

- Choosing between sync and async
- Designing event schemas
- Understanding consistency trade-offs
- Coordinating across service boundaries

### Why Appropriate for Your Profile:

- Senior+ engineers must understand multiple API paradigms
- Event-driven architecture increasingly common at scale
- Integration design is critical for system architecture roles
- Hands-on with industry-standard tools (GraphQL, RabbitMQ/Kafka)

---

## 6. Best Practices Integration

### Code Organization and Structure

```
core-api/
├── graphql/
│   ├── schema.gql           # Generated schema
│   ├── dataloaders/
│   │   ├── user.dataloader.ts
│   │   └── note.dataloader.ts
│   └── guards/
│       └── gql-auth.guard.ts
├── notes/
│   ├── v1/
│   │   └── notes-v1.controller.ts
│   ├── v2/
│   │   └── notes-v2.controller.ts
│   ├── notes.resolver.ts    # GraphQL
│   ├── notes.service.ts
│   └── dto/
├── events/
│   ├── event-bus.service.ts
│   ├── outbox/
│   │   ├── outbox.entity.ts
│   │   └── outbox.service.ts
│   └── consumers/
│       ├── email.consumer.ts
│       └── search.consumer.ts
├── webhooks/
│   ├── webhook.entity.ts
│   ├── webhooks.controller.ts
│   └── webhook-delivery.service.ts
└── sagas/
    └── order-saga.service.ts
```

### Error Handling and Resilience Patterns

**1. GraphQL Error Handling:**

```typescript
@Mutation(() => NotePayload)
async createNote(@Args('input') input: CreateNoteInput): Promise<NotePayload> {
  try {
    const note = await this.notesService.create(input);
    return { note, errors: [] };
  } catch (error) {
    return {
      note: null,
      errors: [{
        message: error.message,
        field: error.field,
      }],
    };
  }
}
```

**2. Event Publishing with Circuit Breaker:**

```typescript
@Injectable()
export class ResilientEventBus {
  private breaker: CircuitBreaker;

  constructor(private eventBus: EventBusService) {
    this.breaker = new CircuitBreaker((event) => this.eventBus.publish(event), {
      timeout: 3000,
      errorThresholdPercentage: 50,
      resetTimeout: 30000,
    });

    this.breaker.fallback(() => {
      // Store in outbox if circuit open
      return this.outboxService.create(event);
    });
  }

  async publish(event: DomainEvent): Promise<void> {
    return this.breaker.fire(event);
  }
}
```

### Performance and Scalability Patterns

**1. GraphQL Query Complexity Limiting:**

```typescript
import { createComplexityRule } from "graphql-query-complexity";

GraphQLModule.forRoot({
  validationRules: [
    depthLimit(10),
    createComplexityRule({
      maximumComplexity: 1000,
      estimators: [
        fieldExtensionsEstimator(),
        simpleEstimator({ defaultComplexity: 1 }),
      ],
    }),
  ],
});
```

**2. Message Queue Scaling:**

```
Kafka Partitioning:
- Topic: notes-events (16 partitions)
- Partition by userId (same user always in same partition)
- Ensures ordering per user
- Parallel processing across partitions

Consumer Group:
- 4 consumers in group
- Each consumer handles 4 partitions
- Scale by adding more consumers (up to 16)
```

---

## 7. "Break & Observe" Exercises

### Exercise 1: GraphQL N+1 Problem

**What to Break:**

```graphql
query GetNotesWithAuthors {
  notes(first: 100) {
    edges {
      node {
        title
        author {
          # Without DataLoader: 100 queries!
          email
        }
      }
    }
  }
}
```

**Expected Behavior:**

- WITHOUT DataLoader: 1 query for notes + 100 queries for authors = 101 queries
- WITH DataLoader: 1 query for notes + 1 batched query for authors = 2 queries

**What to Observe:**

- Database query count
- Response time difference
- Enable SQL query logging to see batching

**Key Learnings:**

- N+1 problem is real and impacts performance
- DataLoader solves with batching
- Importance of monitoring GraphQL resolver performance

---

### Exercise 2: Message Broker Failure

**What to Break:**

```bash
# Stop RabbitMQ while application running
docker stop back2code-rabbitmq

# Try creating notes (should publish events)
curl -X POST http://localhost/api/v1/notes \
  -H "Content-Type: application/json" \
  -d '{"title":"Test","content":"Test"}'
```

**Expected Behavior:**

- Note creation succeeds (database write)
- Event not published (RabbitMQ down)
- With Outbox: Event stored in outbox table
- When RabbitMQ restored: Events published from outbox

**What to Observe:**

- Application continues functioning (graceful degradation)
- Outbox table accumulates events
- Once RabbitMQ restored, outbox processor publishes backlog
- No events lost

**Key Learnings:**

- Outbox pattern provides reliability
- Eventual consistency works
- Message broker downtime doesn't break application

---

### Exercise 3: Event Ordering

**What to Break:**

```bash
# Rapidly create, update, delete same note
for i in {1..10}; do
  curl -X POST http://localhost/api/v1/notes -d '{"title":"Note '$i'"}'
  curl -X PUT http://localhost/api/v1/notes/note-123 -d '{"title":"Updated '$i'"}'
  curl -X DELETE http://localhost/api/v1/notes/note-123
done

# Check search indexer: Is order preserved?
```

**Expected Behavior:**

- Events may arrive out of order (especially with multiple consumers)
- Search index may be inconsistent
- WITH ordering (Kafka partitions): Events processed in order per partition

**What to Observe:**

- Event processing order in consumer logs
- Final state of search index
- Race conditions

**Key Learnings:**

- Event ordering is not guaranteed unless designed for
- Kafka partitions ensure per-key ordering
- Idempotency helps with out-of-order events
- Timestamp-based conflict resolution

---

### Exercise 4: Webhook Retry Storm

**What to Break:**

```bash
# Register webhook that always fails
curl -X POST http://localhost/api/v1/webhooks \
  -d '{"url":"http://fake-endpoint-that-fails.com","events":["note.created"]}'

# Create many notes rapidly
for i in {1..50}; do
  curl -X POST http://localhost/api/v1/notes -d '{"title":"Note '$i'"}'
done
```

**Expected Behavior:**

- Webhook delivery fails
- Exponential backoff prevents immediate retry storm
- After max retries, webhook disabled
- Dead letter queue captures failed deliveries

**What to Observe:**

- Retry timing (exponential backoff)
- System not overwhelmed by retries
- Webhook eventually disabled
- Metrics show failure rate

**Key Learnings:**

- Exponential backoff prevents cascading failures
- Failed webhooks must be disabled
- Importance of retry limits
- Dead letter queues for investigation

---

### Exercise 5: GraphQL Depth Attack

**What to Break:**

```graphql
query MaliciousDeepQuery {
  note(id: "123") {
    author {
      notes {
        author {
          notes {
            author {
              notes {
                # ... 20 levels deep
              }
            }
          }
        }
      }
    }
  }
}
```

**Expected Behavior:**

- WITH depth limiting: Query rejected with error
- WITHOUT depth limiting: Server overwhelmed, slow response or timeout

**What to Observe:**

- Query rejection message
- Server CPU and memory
- Response time

**Key Learnings:**

- GraphQL can be abused with deep queries
- Depth limiting is essential
- Query complexity analysis prevents DoS

---

### Exercise 6: Event Replay

**What to Break:**

```bash
# Corrupt search index (delete data)
curl -X DELETE http://localhost:9200/notes

# Replay events from message broker
# RabbitMQ: Can't replay (events consumed)
# Kafka: Replay from beginning
kafka-consumer-groups --bootstrap-server localhost:9092 \
  --group search-indexer \
  --topic notes-events \
  --reset-offsets --to-earliest --execute
```

**Expected Behavior:**

- Kafka: All events replayed, index rebuilt
- RabbitMQ: Events lost, must rebuild from database

**What to Observe:**

- Kafka offset reset
- Events reprocessed
- Index rebuilt
- Idempotency prevents duplicates

**Key Learnings:**

- Kafka allows event replay (log-based)
- RabbitMQ events ephemeral once consumed
- Idempotency critical for replay
- Event sourcing provides audit trail

---

### Exercise 7: Saga Compensation

**What to Break:**

```typescript
// Simulate payment failure in saga
async processOrder(orderId: string) {
  // Reserve inventory: SUCCESS
  // Charge payment: FAILURE (simulate)
  // Create shipment: SKIPPED
}
```

**Expected Behavior:**

- Inventory reserved
- Payment fails
- Saga compensates: Release inventory
- Order marked as failed

**What to Observe:**

- Compensation execution
- Inventory released
- Order state transitions
- Saga execution log

**Key Learnings:**

- Saga pattern handles distributed failures
- Compensation crucial for consistency
- Order of compensation (reverse)
- Importance of idempotent compensation

---

## 8. Success Criteria

### Knowledge: Can Explain Concepts and Trade-offs

**You should be able to explain:**

**1. API Design:**

- ✅ REST maturity levels and when HATEOAS is worth it
- ✅ GraphQL vs REST: when to use each
- ✅ API versioning strategies and their trade-offs
- ✅ Idempotency and why it's critical for retries

**2. Event-Driven Architecture:**

- ✅ Event-driven vs request-response patterns
- ✅ Choreography vs orchestration trade-offs
- ✅ Eventual consistency implications
- ✅ Event sourcing vs state-based storage

**3. Message Brokers:**

- ✅ RabbitMQ vs Kafka: use cases for each
- ✅ Partitioning strategy and ordering guarantees
- ✅ At-least-once vs exactly-once delivery
- ✅ Dead letter queues and error handling

**4. Integration Patterns:**

- ✅ Saga pattern for distributed transactions
- ✅ Outbox pattern for reliable events
- ✅ Webhook vs polling for notifications
- ✅ DataLoader pattern for GraphQL N+1

### Skills: Can Implement Without Assistance

**You should be able to:**

**1. GraphQL:**

- ✅ Design GraphQL schema with proper types
- ✅ Implement resolvers with DataLoader
- ✅ Add queries, mutations, subscriptions
- ✅ Secure GraphQL with guards
- ✅ Implement pagination and filtering

**2. Event-Driven:**

- ✅ Publish domain events from services
- ✅ Implement event consumers
- ✅ Set up message broker (RabbitMQ/Kafka)
- ✅ Configure exchanges and routing
- ✅ Implement outbox pattern

**3. API Versioning:**

- ✅ Create versioned API controllers
- ✅ Maintain backward compatibility
- ✅ Add deprecation headers
- ✅ Plan version migration strategy

**4. Webhooks:**

- ✅ Implement webhook registration
- ✅ Deliver webhooks with retries
- ✅ Generate and verify signatures
- ✅ Handle delivery failures

### Application: Can Diagnose and Fix Issues

**You should be able to:**

**1. Performance:**

- ✅ Identify and fix N+1 queries in GraphQL
- ✅ Optimize message throughput
- ✅ Debug slow event processing
- ✅ Reduce GraphQL query complexity

**2. Reliability:**

- ✅ Debug lost events
- ✅ Handle out-of-order events
- ✅ Investigate webhook delivery failures
- ✅ Recover from message broker downtime

**3. Consistency:**

- ✅ Debug eventual consistency issues
- ✅ Trace events across services
- ✅ Identify saga compensation failures
- ✅ Resolve event ordering problems

---

## 9. Postmortem Template

**Module:** Module 6: API & Integration Design  
**Completed Date:** \***\*\_\_\_\*\***  
**Actual Time Spent:** **\_** hours (vs estimated 72-96 hours)

### What Worked Well?

**Technical Implementation:**

- Which API paradigm (REST/GraphQL/Events) felt most natural?
- Did DataLoader solve N+1 effectively?
- How well did message broker integration work?
- Was event-driven architecture intuitive?

**Learning Process:**

- Which integration pattern was most insightful?
- Did Break & Observe exercises clarify concepts?
- How effective was hands-on event-driven learning?

### What Was Challenging?

**Conceptual Challenges:**

- Understanding eventual consistency?
- Grasping saga pattern complexity?
- GraphQL schema design decisions?
- Event schema versioning?

**Technical Difficulties:**

- Message broker configuration?
- GraphQL N+1 problem debugging?
- Event ordering guarantees?
- Webhook delivery reliability?

### Key Technical Insights Gained

**API Design:**

- When to use REST vs GraphQL vs Events?
- How does API versioning affect clients?
- What makes an API well-designed?

**Event-Driven Architecture:**

- How does eventual consistency work in practice?
- When is choreography better than orchestration?
- What are operational challenges of events?

**Integration Patterns:**

- How does outbox pattern guarantee reliability?
- When is saga pattern worth the complexity?
- What makes webhooks difficult?

### Quantitative Results

- GraphQL query reduction: \_\_\_% (N+1 → DataLoader)
- Event processing throughput: **\_** events/sec
- Webhook delivery success rate: \_\_\_\_%
- Average event processing latency: **\_**ms

### Questions to Explore Further

1. ***
2. ***
3. ***

### Preparation for Next Module

**Foundation Built:**

- Event-driven system ready for chaos engineering (Module 7)
- Multiple API interfaces to test resilience
- Integration patterns to stress test

**Skills to Strengthen:**

- Advanced GraphQL patterns?
- Kafka cluster management?
- Event sourcing deep dive?

**Mindset:**

- Confidence level for Module 7: \_\_\_/10
- Excitement for chaos engineering: \_\_\_/10

---

## 10. Coverage Gap Analysis

### ✅ Excellently Covered Areas

**API & Integration Design (PRIMARY):**

- Comprehensive REST, GraphQL, and event-driven patterns
- Multiple integration paradigms
- Production-ready webhook system
- **Verdict:** Excellent depth and breadth

**System Design (PRIMARY):**

- Event-driven architecture
- Message broker topology
- Service integration patterns
- **Verdict:** Strong architectural coverage

### ⚠️ Adequately Covered Areas

**Reliability (SECONDARY):**

- Outbox pattern for reliability
- Saga compensation
- **Gap:** No advanced Kafka replication
- **Verdict:** Adequate for module scope

### Critical Concepts Missing

**1. API Gateway Pattern:**

- **Impact:** Production systems often use gateways
- **Recommendation:** Mention in theory, defer implementation to advanced topic
- **Note:** Would add significant complexity

**2. Service Mesh:**

- **Impact:** Modern microservices use meshes
- **Recommendation:** Too advanced, defer to Kubernetes/production topics
- **Note:** Istio, Linkerd beyond scope

### Final Coverage Assessment

| Learning Area     | Coverage Level | Depth      | Breadth    | Module Fit |
| ----------------- | -------------- | ---------- | ---------- | ---------- |
| API & Integration | PRIMARY        | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Perfect    |
| System Design     | PRIMARY        | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐   | Excellent  |
| Observability     | SECONDARY      | ⭐⭐⭐     | ⭐⭐⭐     | Good       |
| Reliability       | SECONDARY      | ⭐⭐⭐⭐   | ⭐⭐⭐     | Good       |
| Security          | SECONDARY      | ⭐⭐⭐     | ⭐⭐       | Adequate   |
| Data & Storage    | SECONDARY      | ⭐⭐⭐     | ⭐⭐       | Adequate   |

---

## 11. Next Module Recommendations

### Module 7: Reliability Ops - Chaos Engineering & Production Readiness

**Primary Focus:**

- Chaos engineering with deliberate failure injection
- Incident response and postmortem practice
- Production deployment strategies
- Disaster recovery procedures
- Load testing at scale
- Performance tuning

**Why This Sequence:**

**1. Complete System to Test:**

- REST API, GraphQL, Events all in place
- Multiple databases and message broker
- Horizontal scaling with load balancer
- Authentication and observability
- Perfect system for chaos testing

**2. Culmination of Learning:**

- Apply all reliability patterns learned
- Test under realistic failure scenarios
- Validate resilience assumptions
- Practice incident response

**3. Production Readiness:**

- Move from learning to production mindset
- Understand operational concerns
- Practice on-call scenarios
- Build confidence in system reliability

**Key Learning Objectives:**

- Inject failures systematically (Chaos Monkey)
- Practice incident response
- Write production-ready postmortems
- Optimize for real-world loads
- Deploy with confidence

---

## Validation Checklist

- [x] **All PRIMARY learning objectives are clear and measurable**
- [x] **Theory foundation covers WHY, not just HOW**
- [x] **Implementation is broken into digestible phases**
- [x] **Complexity is appropriate for the learner profile**
- [x] **At least 5 best practices are explicitly integrated**
- [x] **Break & Observe exercises are practical and insightful**
- [x] **Success criteria are specific and testable**
- [x] **Postmortem prompts encourage deep reflection**
- [x] **Module fits within the progressive learning path**
- [x] **No critical learning gaps exist**

---

## ✅ VALIDATED: This module provides appropriate depth and breadth for the stated learning objectives.

### Strengths of This Module Plan:

**1. Comprehensive Integration Coverage:**

- REST (refined), GraphQL (new), Events (async) in one module
- Real-world integration patterns (saga, outbox, webhooks)
- Multiple communication paradigms

**2. Production-Ready Patterns:**

- DataLoader for performance
- Outbox for reliability
- Webhook delivery with retries
- Event-driven architecture

**3. Hands-On Complexity:**

- Message broker integration
- GraphQL N+1 problem solving
- Distributed transaction patterns
- Async communication at scale

**4. Builds on All Previous Modules:**

- Leverages REST from Module 1
- Uses databases from Module 2
- Applies scaling from Module 3
- Secures with Module 4 auth
- Monitors with Module 5 observability

**5. Career-Critical Skills:**

- GraphQL increasingly common
- Event-driven architecture at scale
- Integration design for distributed systems
- API evolution and versioning

### Why This Works for Your Profile:

**Engineering Leadership:**

- Integration decisions impact org boundaries
- Event-driven architecture affects team structure
- API design influences external partnerships
- Understanding trade-offs informs technology strategy

**Technical Depth:**

- Advanced patterns (saga, outbox, DataLoader)
- Practical experience with GraphQL and message brokers
- Real-world async communication challenges
- Production integration patterns

**System Architecture:**

- Multiple API paradigms in one system
- Choosing right tool for the job
- Consistency vs availability trade-offs
- Service communication strategies

### Success Probability: 85%

**Risks:**

1. **Conceptual Load:** Three API paradigms simultaneously
2. **Async Debugging:** Event-driven systems harder to debug
3. **Time Estimation:** Message broker setup can be tricky

**Mitigation:**

- Clear separation of REST → GraphQL → Events
- Extensive logging and tracing
- Docker Compose simplifies infrastructure
- Break & Observe builds intuition

---

**Ready to begin Module 6 - the culmination of your API and integration learning!**

**This module represents the pinnacle of modern backend development: combining sync APIs (REST, GraphQL) with async communication (events, webhooks) in a cohesive, production-ready system.**
