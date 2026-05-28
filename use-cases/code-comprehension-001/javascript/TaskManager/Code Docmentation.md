This code implements a command-line task management application in Node.js that allows users to create, update, organize, and persist tasks using a JSON file as storage. At a high level, the system separates responsibilities into four layers: the CLI layer (`cli.js`) handles user commands and displays output, the business logic layer (`app.js`) manages task operations and validation, the model layer (`models.js`) defines the structure and behavior of tasks, and the storage layer (`storage.js`) handles reading and writing tasks to disk. The logic flow begins when a CLI command is entered, which is parsed by Commander.js and routed to the `TaskManager`. The manager validates inputs such as dates, creates or updates `Task` objects, and delegates persistence operations to `TaskStorage`. Tasks are serialized into JSON and later reconstructed into proper class instances when loaded back into memory. The implementation assumes valid user input in several places, particularly for task titles, priorities, and statuses, and relies on defensive checks such as verifying valid dates and ensuring tasks exist before updating or deleting them. Edge cases include invalid date formats, missing tasks, duplicate tags, and the fact that JSON serialization converts `Date` objects into strings, requiring manual restoration during loading. Complex sections such as object hydration, filtering logic, and overdue detection would benefit from inline comments explaining why dates are reconstructed using `new Date()`, why only known properties are updated in the `update()` method, and how overdue tasks are determined by combining due dates with completion status. Potential improvements while preserving the original functionality include replacing synchronous file operations with asynchronous versions to avoid blocking the Node.js event loop, adding stronger validation for invalid priorities or empty titles, implementing sorting and search functionality, and introducing schema validation or unit tests for better maintainability and reliability.

# Comprehensive JSDoc Documentation for Task Manager Application

## `TaskManager.createTask()`

```js
/**
 * Creates a new task and stores it in persistent storage.
 *
 * This method validates the optional due date, constructs a new `Task`
 * instance, and saves it using the storage layer.
 *
 * @param {string} title - The title of the task.
 * @param {string} [description=""] - Optional detailed description of the task.
 * @param {number} [priorityValue=2] - Priority level of the task.
 * Accepted values:
 * 1 = LOW
 * 2 = MEDIUM
 * 3 = HIGH
 * 4 = URGENT
 * @param {string|null} [dueDateStr=null] - Due date in `YYYY-MM-DD` format.
 * @param {string[]} [tags=[]] - Array of tags associated with the task.
 *
 * @returns {string|null} Returns the generated task ID if successful,
 * or `null` if date validation fails.
 *
 * @throws {Error} Throws internally when the due date is invalid.
 * The error is caught and handled inside the method.
 *
 * @example
 * const manager = new TaskManager();
 *
 * const taskId = manager.createTask(
 *   'Finish documentation',
 *   'Write JSDoc comments for all functions',
 *   3,
 *   '2026-06-01',
 *   ['docs', 'backend']
 * );
 *
 * console.log(taskId);
 *
 * @note
 * - Invalid dates return `null` and print an error message.
 * - The function does not validate empty titles.
 * - Priority values outside 1-4 are not currently restricted.
 */
```

---

# `TaskManager.listTasks()`

```js
/**
 * Retrieves tasks from storage with optional filtering.
 *
 * Supports filtering by:
 * - task status
 * - priority
 * - overdue state
 *
 * If multiple filters are provided, the method prioritizes:
 * 1. overdue filter
 * 2. status filter
 * 3. priority filter
 *
 * @param {string|null} [statusFilter=null] - Filter tasks by status.
 * Example values:
 * - 'todo'
 * - 'in_progress'
 * - 'review'
 * - 'done'
 *
 * @param {number|null} [priorityFilter=null] - Filter tasks by priority level.
 *
 * @param {boolean} [showOverdue=false] - Whether to return only overdue tasks.
 *
 * @returns {Task[]} Array of matching task objects.
 *
 * @example
 * const overdueTasks = manager.listTasks(null, null, true);
 *
 * @example
 * const highPriorityTasks = manager.listTasks(null, 3);
 *
 * @example
 * const completedTasks = manager.listTasks('done');
 *
 * @note
 * - Only one filter type is applied at a time.
 * - Overdue filtering takes highest priority.
 * - Returns an empty array if no tasks match.
 */
```

---

# `TaskManager.updateTaskStatus()`

```js
/**
 * Updates the workflow status of a task.
 *
 * If the new status is `DONE`, the task is marked completed using
 * `markAsDone()` which also sets completion timestamps.
 *
 * @param {string} taskId - Unique identifier of the task.
 * @param {string} newStatusValue - New task status.
 * Valid values:
 * - 'todo'
 * - 'in_progress'
 * - 'review'
 * - 'done'
 *
 * @returns {boolean} Returns `true` if update succeeds,
 * otherwise `false`.
 *
 * @example
 * manager.updateTaskStatus(taskId, 'in_progress');
 *
 * @example
 * manager.updateTaskStatus(taskId, 'done');
 *
 * @note
 * - Setting status to `done` automatically updates `completedAt`.
 * - Returns `false` if the task does not exist.
 * - No validation currently prevents invalid status strings.
 */
```

---

# `TaskManager.getStatistics()`

```js
/**
 * Computes aggregate statistics for all stored tasks.
 *
 * Statistics include:
 * - total task count
 * - tasks grouped by status
 * - tasks grouped by priority
 * - overdue task count
 * - tasks completed within the last 7 days
 *
 * @returns {Object} Statistics summary object.
 *
 * @returns {number} returns.total Total number of tasks.
 * @returns {Object<string, number>} returns.byStatus Task counts grouped by status.
 * @returns {Object<string, number>} returns.byPriority Task counts grouped by priority.
 * @returns {number} returns.overdue Number of overdue tasks.
 * @returns {number} returns.completedLastWeek Number of tasks completed in the last 7 days.
 *
 * @example
 * const stats = manager.getStatistics();
 *
 * console.log(stats.total);
 * console.log(stats.overdue);
 *
 * @example
 * // Example response
 * {
 *   total: 12,
 *   byStatus: {
 *     todo: 5,
 *     in_progress: 3,
 *     review: 2,
 *     done: 2
 *   },
 *   byPriority: {
 *     1: 2,
 *     2: 5,
 *     3: 3,
 *     4: 2
 *   },
 *   overdue: 1,
 *   completedLastWeek: 4
 * }
 *
 * @note
 * - Recently completed tasks are determined using a rolling 7-day window.
 * - Uses in-memory filtering operations.
 * - Performance scales linearly with task count.
 */
```

---

# `Task.update()`

```js
/**
 * Updates one or more properties of a task.
 *
 * Only existing task properties are updated.
 * Unknown properties are ignored.
 *
 * @param {Object} updates - Object containing fields to update.
 *
 * @returns {void}
 *
 * @example
 * task.update({
 *   title: 'Updated title',
 *   priority: 4
 * });
 *
 * @note
 * - Automatically refreshes `updatedAt` timestamp.
 * - Prevents accidental creation of unknown properties.
 * - Uses shallow property assignment.
 */
```

---

# `Task.markAsDone()`

```js
/**
 * Marks the task as completed.
 *
 * Updates:
 * - status
 * - completedAt timestamp
 * - updatedAt timestamp
 *
 * @returns {void}
 *
 * @example
 * task.markAsDone();
 *
 * @note
 * - Completion timestamp uses current system time.
 * - Does not validate current workflow state.
 */
```

---

# `Task.isOverdue()`

```js
/**
 * Determines whether the task is overdue.
 *
 * A task is considered overdue when:
 * - it has a due date
 * - the due date has passed
 * - the task is not completed
 *
 * @returns {boolean} Returns `true` if overdue,
 * otherwise `false`.
 *
 * @example
 * if (task.isOverdue()) {
 *   console.log('Task requires attention');
 * }
 *
 * @note
 * - Tasks without due dates are never overdue.
 * - Completed tasks are excluded from overdue checks.
 * - Uses the current system date/time.
 */
```

---

# `TaskStorage.load()`

```js
/**
 * Loads tasks from the JSON storage file.
 *
 * Reads persisted task data from disk, recreates Task instances,
 * restores Date objects, and hydrates all task properties.
 *
 * @returns {void}
 *
 * @throws {Error} Logs file parsing or file system errors.
 *
 * @example
 * const storage = new TaskStorage('tasks.json');
 * storage.load();
 *
 * @note
 * - Automatically called during storage initialization.
 * - Restores serialized date strings into Date objects.
 * - Invalid JSON files may cause loading errors.
 * - Uses synchronous file system operations.
 */
```

---

# `TaskStorage.save()`

```js
/**
 * Saves all tasks to the storage file.
 *
 * Serializes tasks into formatted JSON and writes them
 * synchronously to disk.
 *
 * @returns {void}
 *
 * @throws {Error} Logs file system write errors.
 *
 * @example
 * storage.save();
 *
 * @note
 * - Uses `JSON.stringify()` for serialization.
 * - Uses blocking synchronous file operations.
 * - Existing file contents are overwritten.
 */
```

---

# `TaskStorage.addTask()`

```js
/**
 * Adds a new task to storage and persists changes.
 *
 * @param {Task} task - Task instance to store.
 *
 * @returns {string} Returns the task ID.
 *
 * @example
 * const task = new Task('Learn Node.js');
 * const id = storage.addTask(task);
 *
 * @note
 * - Automatically saves after insertion.
 * - Existing tasks with the same ID will be overwritten.
 */
```

---

# `TaskStorage.updateTask()`

```js
/**
 * Updates an existing task using partial updates.
 *
 * @param {string} taskId - ID of the task to update.
 * @param {Object} updates - Fields to update.
 *
 * @returns {boolean} Returns `true` if update succeeds,
 * otherwise `false`.
 *
 * @example
 * storage.updateTask(taskId, {
 *   priority: 4,
 *   status: 'review'
 * });
 *
 * @note
 * - Automatically persists changes.
 * - Returns `false` if task is missing.
 */
```

---

# `TaskStorage.deleteTask()`

```js
/**
 * Removes a task from storage.
 *
 * @param {string} taskId - ID of the task to remove.
 *
 * @returns {boolean} Returns `true` if deletion succeeds,
 * otherwise `false`.
 *
 * @example
 * storage.deleteTask(taskId);
 *
 * @note
 * - Automatically saves updated storage.
 * - Deleting a non-existent task safely returns `false`.
 */
```

---

# `formatTask()`

```js
/**
 * Formats a task into a human-readable CLI display string.
 *
 * Includes:
 * - status symbols
 * - priority indicators
 * - due dates
 * - tags
 * - timestamps
 *
 * @param {Task} task - Task object to format.
 *
 * @returns {string} Formatted multi-line task display.
 *
 * @example
 * console.log(formatTask(task));
 *
 * @example
 * // Example output
 * [ ] 1234abcd - !!! Finish docs
 *   Write JSDoc comments
 *   Due: 2026-06-01 | Tags: docs, backend
 *   Created: 2026-05-28 10:22:11
 *
 * @note
 * - Assumes task contains valid Date objects.
 * - Uses shortened task IDs for readability.
 * - Missing tags or due dates are displayed with defaults.
 */
```

---

# General Developer Notes

## Important Edge Cases

### Invalid Dates

The application validates dates using:

```js
isNaN(date.getTime())
```

Invalid dates return errors gracefully.

---

## Serialization Caveat

Date objects become strings when saved to JSON.

They must be reconstructed using:

```js
new Date(savedValue)
```

---

## Synchronous File Operations

The application currently uses:

```js
fs.writeFileSync()
```

This blocks the Node.js event loop.

For larger applications, prefer:

```js
fs.promises.writeFile()
```

---

## Missing Validation

Currently not validated:

* empty task titles
* invalid priorities
* invalid statuses
* duplicate task names

Production systems should add schema validation.

---

## Performance Characteristics

| Operation    | Complexity |
| ------------ | ---------- |
| Get Task     | O(1)       |
| Add Task     | O(1)       |
| Delete Task  | O(1)       |
| Filter Tasks | O(n)       |
| Statistics   | O(n)       |

---

## Recommended Future Improvements

* Async persistence
* Sorting support
* Full-text search
* Task pagination
* Schema validation
* Unit tests
* Database integration
* REST API layer
