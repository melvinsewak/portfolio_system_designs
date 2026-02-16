# Food Delivery Service - Sequence Diagram

## Overview
Key user workflows and API interactions for food delivery service.

DoorDash, UberEats - Restaurant and menu management, order placement and tracking, delivery logistics optimization

## Sequence Diagrams

### 1. User Authentication Flow

```
User          Web App       API Gateway    Auth Service    Database
  │              │               │              │             │
  │─────Login────>│               │              │             │
  │              │──Request──────>│              │             │
  │              │               │──Validate────>│             │
  │              │               │              │──Query─────>│
  │              │               │              │<───Result───│
  │              │               │<─JWT Token───│             │
  │              │<─Response─────│              │             │
  │<───Token─────│               │              │             │
  │              │               │              │             │
```

### 2. Main Operation Flow

```
Client        API Gateway    Service A     Service B     Database    Queue
  │               │             │             │             │          │
  │──Request─────>│             │             │             │          │
  │               │──Auth───────>│            │             │          │
  │               │             │──Validate──>│             │          │
  │               │             │            │──Read───────>│          │
  │               │             │            │<──Data──────│          │
  │               │             │<──Result───│             │          │
  │               │             │─────Publish Event───────────────────>│
  │               │<──Response──│             │             │          │
  │<──Result─────│             │             │             │          │
  │               │             │             │             │          │
```

### 3. Async Processing Flow

```
Client    API Gateway    Service    Queue    Worker    Database    Notification
  │           │            │         │         │          │             │
  │──Request─>│            │         │         │          │             │
  │           │──Process──>│         │         │          │             │
  │           │            │──Queue─>│         │          │             │
  │           │<──JobID────│         │         │          │             │
  │<──JobID───│            │         │         │          │             │
  │           │            │         │         │          │             │
  │           │            │         │──Poll──>│          │             │
  │           │            │         │         │──Update─>│             │
  │           │            │         │         │          │             │
  │           │            │         │         │──Notify────────────────>│
  │           │            │         │         │          │             │
  │<──────────────────Notification────────────────────────────────────────│
```

### 4. Data Query Flow

```
Client    API Gateway    Service    Cache     Database
  │           │            │          │          │
  │──Query───>│            │          │          │
  │           │──Request──>│          │          │
  │           │            │──Check──>│          │
  │           │            │<──Miss───│          │
  │           │            │──────────Query─────>│
  │           │            │<────────Result──────│
  │           │            │──Store──>│          │
  │           │<──Response─│          │          │
  │<──Data────│            │          │          │
```

### 5. Error Handling Flow

```
Client    API Gateway    Service A    Service B    Database
  │           │             │            │            │
  │──Request─>│             │            │            │
  │           │──Forward───>│            │            │
  │           │             │──Call─────>│            │
  │           │             │            │──Query────>│
  │           │             │            │<──Error────│
  │           │             │<──Error────│            │
  │           │             │──Retry────>│            │
  │           │             │            │──Query────>│
  │           │             │            │<──Success──│
  │           │             │<──Success──│            │
  │           │<──Response──│            │            │
  │<──Result──│             │            │            │
```

## Workflow Descriptions

### Authentication
1. User submits credentials
2. API Gateway forwards to Auth Service
3. Auth Service validates against database
4. JWT token generated and returned
5. Client stores token for subsequent requests

### Main Operations
1. Client sends authenticated request
2. API Gateway validates JWT
3. Request routed to appropriate service
4. Service processes business logic
5. Database operations performed
6. Events published to message queue
7. Response returned to client

### Asynchronous Processing
1. Client initiates long-running operation
2. Job queued with unique ID
3. Client receives job ID immediately
4. Worker processes job asynchronously
5. Status updates written to database
6. Notification sent upon completion

### Caching Strategy
1. Check cache for requested data
2. On cache hit, return immediately
3. On cache miss, query database
4. Store result in cache
5. Return data to client

### Error Recovery
1. Initial request fails
2. Retry logic activated
3. Exponential backoff applied
4. Circuit breaker prevents cascade
5. Fallback response if needed
