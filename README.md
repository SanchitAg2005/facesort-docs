# facesort-docs

# FaceSort — AI-Based Photo Sorting & Delivery System

FaceSort is a system designed to automatically identify people in photos and deliver relevant images to them, using face recognition and a privacy-first, modular architecture.

The project focuses on **system design, scalability, and real-world AI deployment**, combining a modern web interface with a hybrid cloud–local processing pipeline.

This repository documents the **architecture, workflows, and design decisions** behind FaceSort.  
Core algorithms and proprietary implementations are intentionally excluded.

---
![UI Diagram](assets/image.png)
---

## Project Motivation

Managing large numbers of group photos is inefficient and manual.  
FaceSort aims to solve this by:

- Detecting individuals across image batches
- Grouping photos by person
- Delivering results automatically via messaging platforms
- Preserving privacy and minimizing cloud-side computation

The system is built with real-world constraints in mind: limited compute, cost efficiency, and user privacy.

---

## High-Level Capabilities

- AI-powered face recognition pipeline (CPU-friendly)
- Hybrid cloud + local worker architecture
- Job-based batch photo processing
- Automated delivery using Telegram
- Scalable, queue-driven backend design
- Secure handling of user and image data

---

## System Overview

At a high level, FaceSort consists of:

- A **web frontend** for user interaction
- A **backend API** for orchestration and data management
- **Object storage** for images and embeddings
- A **local worker** for compute-heavy face recognition
- A **messaging layer** for automated delivery

The system is intentionally modular so that each component can evolve independently.

(Detailed architecture diagrams are provided in the `/docs` section.)

---

## User Workflow (Conceptual)

1. User logs in and manages their profile
2. Friends are enrolled with a reference image and contact ID
3. Batch photos are uploaded via the UI
4. A processing job is created and queued
5. A local worker performs face recognition and matching
6. Matched photos are delivered to respective recipients
7. Temporary data is cleaned up automatically

Each step is tracked via explicit job and status states.

---

## Technology Stack

**Frontend**
- Next.js (App Router)
- Client-side image preprocessing

**Backend**
- FastAPI
- REST-based job orchestration
- Secure API routes

**AI / ML**
- ONNX Runtime
- Face detection + embedding pipeline
- CPU-optimized inference

**Data & Storage**
- MongoDB (users, friends, jobs, metadata)
- Supabase Storage (images, embeddings)

**Messaging**
- Telegram Bot API

---

## Design Principles

- **Privacy-first**: Sensitive processing runs locally whenever possible
- **Separation of concerns**: UI, API, worker, and storage are decoupled
- **Fail-safe workflows**: Queues and status tracking prevent silent failures
- **Cost awareness**: Designed to run on free or low-cost infrastructure
- **Scalability**: Worker-based architecture allows horizontal growth

---

## Engineering Challenges Addressed

- CPU-only face recognition without GPU dependency
- Secure transfer of large image batches
- Avoiding cloud-side biometric processing
- Fair matching logic for multiple identities per image
- Reliable cleanup of temporary data
- Handling offline or delayed worker execution

---

## What This Repository Contains

- System architecture documentation
- UI interaction flows
- Database and storage design
- Worker pipeline lifecycle (conceptual)
- Design rationale and scalability notes

## What This Repository Does NOT Contain

- Face recognition model weights
- Matching algorithms or thresholds
- Worker implementation code
- Deployment secrets or configs

---

## Intended Audience

- Recruiters and reviewers evaluating system design skills
- Engineers interested in scalable AI pipelines
- Researchers looking at applied face recognition systems
- Collaborators discussing architecture and workflows

---

## Note on Code Availability

Core algorithms, worker logic, and matching implementations are intentionally kept private to protect system integrity and prevent misuse.

This repository focuses on **how the system is designed and why**, not on replicating the implementation.

---

## Author

Developed and designed by a student engineer with a focus on AI systems, backend architecture, and real-world deployment constraints.
