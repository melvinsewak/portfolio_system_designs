# Social Media Platform - ER Diagram

## Overview
Database schema and entity relationships for social media platform.

Facebook, Twitter - News feed generation, posts, comments, likes, real-time notifications, social graph management

## Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        Core Entities                             │
└─────────────────────────────────────────────────────────────────┘

┌──────────────────┐         ┌──────────────────┐
│     Users        │         │    Profiles      │
├──────────────────┤         ├──────────────────┤
│ PK: id (UUID)    │────────<│ PK: id           │
│     email        │    1:1  │ FK: user_id      │
│     password_hash│         │     first_name   │
│     status       │         │     last_name    │
│     created_at   │         │     avatar_url   │
│     updated_at   │         │     bio          │
└──────────────────┘         └──────────────────┘
        │
        │ 1:N
        │
        ▼
┌──────────────────┐
│    Sessions      │
├──────────────────┤
│ PK: id           │
│ FK: user_id      │
│     token        │
│     expires_at   │
│     created_at   │
└──────────────────┘


┌──────────────────┐         ┌──────────────────┐
│  Main Entity     │         │  Related Entity  │
├──────────────────┤         ├──────────────────┤
│ PK: id           │────────<│ PK: id           │
│ FK: user_id      │    1:N  │ FK: main_id      │
│     title        │         │     data         │
│     description  │         │     status       │
│     status       │         │     created_at   │
│     created_at   │         │     updated_at   │
│     updated_at   │         └──────────────────┘
└──────────────────┘
        │
        │ N:M
        │
        ▼
┌──────────────────┐         ┌──────────────────┐
│  Categories      │         │  Entity_Category │
├──────────────────┤         ├──────────────────┤
│ PK: id           │────────<│ FK: entity_id    │
│     name         │         │ FK: category_id  │
│     slug         │         │     created_at   │
│     description  │         └──────────────────┘
└──────────────────┘


┌──────────────────┐
│   Transactions   │
├──────────────────┤
│ PK: id           │
│ FK: user_id      │
│     amount       │
│     currency     │
│     status       │
│     type         │
│     created_at   │
│     updated_at   │
└──────────────────┘


┌──────────────────┐
│   Audit_Logs     │
├──────────────────┤
│ PK: id           │
│ FK: user_id      │
│     entity_type  │
│     entity_id    │
│     action       │
│     old_value    │
│     new_value    │
│     ip_address   │
│     user_agent   │
│     created_at   │
└──────────────────┘
```

## Entity Descriptions

### Users
Primary user account entity containing authentication information.

**Columns**:
- `id` (UUID, PK): Unique identifier
- `email` (VARCHAR, UNIQUE): User email address
- `password_hash` (VARCHAR): Hashed password
- `status` (ENUM): active, inactive, suspended
- `created_at` (TIMESTAMP): Account creation time
- `updated_at` (TIMESTAMP): Last update time

**Indexes**:
- PRIMARY KEY on `id`
- UNIQUE INDEX on `email`
- INDEX on `status`, `created_at`

### Profiles
User profile information and preferences.

**Columns**:
- `id` (BIGINT, PK): Profile ID
- `user_id` (UUID, FK): Reference to users.id
- `first_name` (VARCHAR): First name
- `last_name` (VARCHAR): Last name
- `avatar_url` (VARCHAR): Profile picture URL
- `bio` (TEXT): User biography

**Relationships**:
- One-to-one with Users

### Sessions
Active user sessions for authentication.

**Columns**:
- `id` (BIGINT, PK): Session ID
- `user_id` (UUID, FK): Reference to users.id
- `token` (VARCHAR): Session token
- `expires_at` (TIMESTAMP): Expiration time
- `created_at` (TIMESTAMP): Creation time

**Indexes**:
- INDEX on `user_id`
- UNIQUE INDEX on `token`
- INDEX on `expires_at`

### Transactions
Financial or activity transactions.

**Columns**:
- `id` (BIGINT, PK): Transaction ID
- `user_id` (UUID, FK): Reference to users.id
- `amount` (DECIMAL): Transaction amount
- `currency` (VARCHAR): Currency code
- `status` (ENUM): pending, completed, failed
- `type` (VARCHAR): Transaction type
- `created_at` (TIMESTAMP): Transaction time

**Indexes**:
- INDEX on `user_id`, `created_at`
- INDEX on `status`

### Audit_Logs
Comprehensive audit trail for all system changes.

**Columns**:
- `id` (BIGINT, PK): Log entry ID
- `user_id` (UUID, FK): User who made the change
- `entity_type` (VARCHAR): Type of entity changed
- `entity_id` (VARCHAR): ID of entity changed
- `action` (VARCHAR): Action performed
- `old_value` (JSON): Previous value
- `new_value` (JSON): New value
- `ip_address` (INET): Client IP
- `user_agent` (TEXT): Client user agent
- `created_at` (TIMESTAMP): Log time

## Database Optimization

### Indexing Strategy
- Primary keys on all tables
- Foreign key indexes for joins
- Composite indexes for common queries
- Partial indexes for filtered queries

### Partitioning
- Time-based partitioning for logs and transactions
- Range partitioning for large tables

### Replication
- Master-slave replication for read scaling
- Multi-master for high availability

### Backup Strategy
- Daily full backups
- Hourly incremental backups
- Point-in-time recovery capability
