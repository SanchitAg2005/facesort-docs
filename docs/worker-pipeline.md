
# FaceSort Worker Processing Pipeline

This document explains how the local worker processes queued tasks, performs face recognition workflows, and manages delivery and cleanup operations.

The worker is designed as a continuous polling system that executes tasks asynchronously without blocking the frontend or backend API.

---

## Worker Pipeline Overview

![Worker Pipeline Diagram](../assets/workerpipeline.png)

The worker operates independently and communicates with the backend through structured job states.

Its primary role is to handle computational workloads while keeping cloud infrastructure lightweight.

---

## Core Responsibilities

The worker performs the following high-level operations:

- Poll backend for queued tasks
- Download required images from object storage
- Load stored friend embeddings
- Perform face processing (conceptual)
- Build structured result logs
- Trigger delivery workflows
- Clean up temporary files

The worker does not manage user authentication or UI logic.

---

## Continuous Processing Loop

The worker follows a repeatable lifecycle:

1. Send heartbeat signal to backend.
2. Request queued friends or jobs.
3. If no tasks exist, return to idle polling.
4. If tasks exist, begin processing pipeline.

This loop allows the system to run even when tasks arrive unpredictably.

---

## Stage 1 — Task Retrieval

- Worker requests queued items from backend API.
- Backend validates job ownership and status.
- Required storage paths are returned.

Design reasoning:

- Pull-based architecture avoids dependency on external triggers.
- Simplifies deployment across environments.

---

## Stage 2 — Asset Preparation

- Worker downloads images using signed URLs.
- Embedding references are loaded from storage.
- Temporary workspace is created locally.

Heavy files are never stored permanently on the worker.

---

## Stage 3 — Face Processing (Conceptual)

During this stage:

- Faces are detected from images.
- Embeddings are extracted.
- Matching is performed against known friends.

Implementation details are intentionally abstracted in this documentation.

---

## Stage 4 — Result Logging

After matching:

- Worker builds a structured result_log.
- Backend job status updates to `matched`.
- Delivery stage becomes available.

Result logs contain only references to matched assets.

---

## Stage 5 — Delivery Trigger

- Worker initiates messaging workflows.
- Photos are delivered through Telegram.
- Delivery confirmations are recorded.

Delivery is separated from recognition logic to maintain modularity.

---

## Stage 6 — Cleanup

After delivery:

- Temporary files are deleted from worker storage.
- Batch images are removed from object storage.
- Job status updates to `completed`.

Cleanup prevents unnecessary storage costs and protects user data.

---

## Error Handling Strategy

If an issue occurs:

- Worker updates job status to `error`.
- Processing stops for that task only.
- Worker resumes polling loop.

Failures do not crash the overall system.

---

## Design Decisions

### Pull-Based Worker Model

The worker polls the backend instead of receiving pushed tasks to:

- Avoid firewall or networking issues
- Support offline or delayed execution
- Allow multiple workers in the future

### Local Processing

Compute-heavy operations run locally to:

- Reduce cloud costs
- Maintain privacy boundaries
- Enable flexible hardware scaling

---

## Scalability Notes

Future enhancements may include:

- Multiple workers operating concurrently
- GPU-enabled worker deployments
- Distributed job queues
- Additional delivery integrations
