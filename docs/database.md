
# FaceSort Database Design

This document explains how FaceSort manages application state using MongoDB and object storage references.

The database is designed to track asynchronous workflows, maintain clear ownership boundaries between users and data, and support scalable job processing.

Sensitive biometric data is not stored directly in the database. Instead, the system stores references to files located in secure object storage.

---
##DataBase Overview
![DataBase OverviewDiagram](../assets/database.drawio.png)
## Design Goals

- Maintain clear separation between metadata and large files
- Enable queue-based processing using explicit job states
- Ensure safe deletion and cleanup workflows
- Support future scaling with minimal schema changes
- Keep biometric processing external to the database layer

---

## Core Collections

### 1. users

Stores account-level information and profile metadata.

Typical fields include:

- user_id
- username
- telegram_id
- selfie_path (storage reference)
- created_at
- updated_at

Purpose:

- Identify ownership of friends and jobs
- Store profile configuration
- Maintain authentication-linked metadata

---

### 2. friends

Represents enrolled individuals whose faces can be recognized.

Typical fields include:

- _id
- user_id
- name
- telegram_chat_id
- telegram_activated
- original_image_path
- thumbnail_path
- embedding_path
- status (queued | processed)
- created_at
- updated_at

Design reasoning:

- Friend enrollment is asynchronous
- Original images are temporary and later removed
- Embeddings are stored as storage files rather than raw database data

---

### 3. jobs

Tracks batch processing tasks created by users.

Typical fields include:

- job_id
- user_id
- image_paths[]
- result_log
- status (queued | processing | matched | delivered | completed | error)
- created_at
- updated_at

Purpose:

- Enable long-running processing without blocking UI
- Allow worker polling instead of push-based execution
- Provide full visibility into system state

---

### 4. telegram_users

Stores verified Telegram connections.

Typical fields include:

- telegram_chat_id
- username
- activation_status
- linked_user_id

This collection allows validation of messaging availability before delivery.

---

## Storage Strategy

The database stores **references**, not heavy data.

Examples:

- Supabase file paths
- Thumbnail locations
- Embedding file paths

Reasons:

- Reduce database size
- Improve performance
- Simplify cleanup after processing

---

## Data Lifecycle

### Friend Enrollment

1. User uploads reference image
2. Friend document is created with status = queued
3. Worker generates embeddings and thumbnail
4. Paths are updated in database
5. Original image is deleted from storage

---

### Job Processing

1. User uploads batch images
2. Job document created with status = queued
3. Worker processes and writes result_log
4. Delivery system runs
5. Temporary image paths are removed

---

## Relationships Overview

High-level relationships:

- One user → many friends
- One user → many jobs
- Friends linked to Telegram IDs
- Jobs linked to storage paths

(Include database relationship diagram in /assets.)

---

## Security Considerations

- No raw face embeddings stored directly in MongoDB
- Storage access controlled via signed URLs
- Worker-only processes sensitive files
- API routes validate ownership before access

---

## Scalability Considerations

The schema allows:

- Multiple workers processing jobs simultaneously
- Partitioning jobs by user_id
- Future indexing strategies for faster queries
- Expansion into additional messaging platforms

---

## Suggested Visuals

It is recommended to include:

1. Database relationship diagram
   - users → friends → jobs mapping
   - telegram_users linkage

2. Screenshot of MongoDB collections (optional)
   - Blur or anonymize sensitive values

Place diagrams in `/assets` and reference them here once added.
