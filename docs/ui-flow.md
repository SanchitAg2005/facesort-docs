
# FaceSort UI Interaction Flow

This document explains how user actions in the interface trigger backend workflows, storage operations, and worker processing.

The goal of the UI design is to keep interactions simple while allowing complex background processing to run asynchronously.

---

## UI Flow Overview

![UI Flow Diagram](../assets/uiflow.png)

The interface acts as a control layer that creates jobs, manages friends, and displays system status.  
All heavy processing happens outside the browser.

---

## Key Design Principles

- The UI never performs face-recognition tasks directly.
- Every user action creates or updates a tracked system state.
- Long-running operations are handled asynchronously.
- Status indicators reflect real backend progress.

---

## Flow 1 — Friend Enrollment

### Step-by-step interaction:

1. User clicks **Add Friend** in the dashboard.
2. The frontend prepares image data and sends a request to the backend API.
3. Backend creates a new friend record with `status = queued`.
4. The reference image is uploaded to object storage.
5. UI immediately shows the friend as "Queued".
6. The local worker later processes the friend in the background.

Why this design:

- Prevents blocking the user interface.
- Allows batch processing by the worker.
- Maintains clear state transitions.

---

## Flow 2 — Batch Photo Upload

### Step-by-step interaction:

1. User clicks **Upload Batch Photos**.
2. Frontend sends images through authenticated API requests.
3. Backend creates a new job document.
4. Images are stored in object storage.
5. Job status appears as `queued` in the UI.

The UI does not wait for processing to finish — it only reflects job status updates.

---

## Flow 3 — Background Processing Feedback

While the worker operates:

- The UI periodically fetches updated job states.
- Status transitions include:
  - queued
  - processing
  - matched
  - delivered
  - completed

This allows users to monitor progress without refreshing or blocking the interface.

---

## Flow 4 — Delivery Updates

After matching is complete:

1. The backend receives delivery confirmations.
2. Job status updates propagate to the UI.
3. Users see final delivery results and completion state.

Temporary assets are removed during backend cleanup stages.

---

## UI Responsibilities vs Backend Responsibilities

### UI Handles:

- User input
- Upload initiation
- Status display
- Navigation

### Backend Handles:

- Job orchestration
- Storage coordination
- Worker communication
- Delivery updates

This separation keeps the frontend lightweight and secure.

---

## Error Handling Behavior

If an operation fails:

- Backend marks job status as `error`.
- UI displays failure state without exposing internal details.
- Users can retry actions without refreshing the entire system.

---

## Suggested Visual Additions

Recommended screenshots to include in `/assets`:

- Dashboard showing job status cards
- Add Friend modal or flow
- Batch upload interface

Avoid including sensitive identifiers in screenshots.
