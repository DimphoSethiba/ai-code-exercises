# Task Manager Codebase Understanding Summary

## Project Overview

### Current Understanding of the Application

The application appears to be a command-line task management system that allows users to create, update, delete, prioritize, and track tasks. Tasks can contain statuses, priorities, tags, due dates, and timestamps. The application stores task data locally in a JSON file.

### Technologies and Frameworks Identified

* Node.js
* JavaScript
* Commander.js (CLI framework)
* UUID (unique ID generation)
* Jest (testing framework)
* JSON file persistence using Node.js `fs`

### Folder Structure Pattern

The project follows a layered architecture pattern:

* `cli.js` → presentation/CLI layer
* `app.js` → business logic/service layer
* `models.js` → domain entities/models
* `storage.js` → persistence/repository layer
* `tests/` → automated tests

### Main Entry Point

The primary application entry point is:

```txt
cli.js
```

Execution flow:

```txt
CLI Command
   ↓
Commander.js
   ↓
TaskManager (app.js)
   ↓
TaskStorage (storage.js)
   ↓
tasks.json
```

### Questions to Ask the Team

1. Are task status transitions supposed to follow strict workflow rules?
2. Is the application expected to scale beyond local JSON storage?
3. What testing standards and coverage expectations exist?
4. Which areas of the codebase are still evolving?
5. Are there plans to add APIs or a UI in the future?

### Small Exploration Exercise

Trace the full lifecycle of task creation:

1. Find the `create` command in `cli.js`
2. Follow the call into `TaskManager.createTask()`
3. Inspect how `Task` objects are created in `models.js`
4. See how tasks are saved in `storage.js`
5. Verify persistence inside `tasks.json`

---

# Exercise Part 1: Understanding Task Creation and Status Updates

## Main Components Involved

* `cli.js`
* `app.js`
* `models.js`
* `storage.js`

## Task Creation Flow

```txt
User CLI Command
   ↓
Commander.js command handler
   ↓
TaskManager.createTask()
   ↓
new Task()
   ↓
TaskStorage.addTask()
   ↓
save() → tasks.json
```

## Task Status Update Flow

```txt
CLI status command
   ↓
TaskManager.updateTaskStatus()
   ↓
Task.markAsDone() or updateTask()
   ↓
TaskStorage.save()
   ↓
tasks.json updated
```

## Data Storage and Retrieval

* Tasks are stored in `tasks.json`
* `storage.js` handles reading/writing
* `JSON.stringify()` serializes tasks
* `JSON.parse()` reconstructs data
* `TaskStorage.load()` recreates `Task` objects

## Design Patterns Discovered

* Layered architecture
* Repository-style persistence
* Object-oriented domain models
* Command pattern through Commander.js

---

# Exercise Part 2: Understanding Task Prioritization

## Initial Understanding

Initially, task priorities appeared to be simple numeric values used for filtering and display.

## What Was Discovered

The application uses a domain-specific priority model:

```js
LOW: 1
MEDIUM: 2
HIGH: 3
URGENT: 4
```

Priorities affect:

* filtering
* CLI display formatting
* statistics generation
* future business-rule possibilities

## Key Insights

* Priorities are centralized in `TaskPriority`
* Priority logic is reused throughout the system
* Priority formatting is handled separately from business logic

## Misconceptions Clarified

* Priorities are not just UI labels
* They are part of the domain model and workflow logic

---

# Exercise Part 3: Mapping Data Flow for Task Completion

## Data Flow Diagram

```txt
CLI "status" command
      ↓
TaskManager.updateTaskStatus()
      ↓
Task.markAsDone()
      ↓
status = DONE
completedAt = current date
updatedAt = current date
      ↓
TaskStorage.save()
      ↓
tasks.json updated
```

## State Changes During Completion

* `status` changes to `DONE`
* `completedAt` timestamp is set
* `updatedAt` timestamp changes

## Persistence Process

* Tasks are stored in memory
* Entire task collection is rewritten to `tasks.json`

## Potential Failure Points

* Invalid task IDs
* Corrupted JSON file
* Invalid date handling
* Missing save operations
* Synchronous file operation failures

---

# Reflection and Presentation Notes

## High-Level Architecture

The application uses a layered architecture with separation between:

* user interaction
* business logic
* domain models
* persistence

## Key Features Explained

### Task Creation

Creates a `Task` object and persists it through `TaskStorage`.

### Task Prioritization

Uses enum-style priority levels for filtering and workflow classification.

### Task Completion

Updates task state and timestamps before saving changes to persistent storage.

## Interesting Design Pattern

The repository-style `TaskStorage` abstraction cleanly separates persistence logic from business logic.

## Most Challenging Aspect

Understanding how data flowed between layers and where business rules belonged.

## How the AI Prompts Helped

The prompts helped:

* trace execution flow
* identify architectural layers
* recognize reusable patterns
* distinguish between domain logic and service logic
* map persistence behavior

## Strategies Developed for Future Codebases

* Start from application entry points
* Trace one feature end-to-end
* Identify domain entities first
* Search by business concepts, not only feature names
* Use architectural patterns to organize understanding
