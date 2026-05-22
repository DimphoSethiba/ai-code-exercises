**Code Comprehension**
**Exercise Part:1**

2.
The application is a task management or to-do management system that allows users to create tasks, view task lists, update task statuses, and delete tasks.

These the tevhnologies being used in this app:
  - Node.js
  - JavaScript
  - Commander.js
  - UUID
  - Jest

The main components are:
 - app.js
 - cli.js
 - models.js
 - storage.js

3.
Validate / correct your understanding
What the app is

Your guess is correct:
This is a CLI-based task / to‑do management system that lets you:

Create tasks (with title, description, priority, due date, tags).
List tasks (optionally filtered by status, priority, or overdue).
Update status, priority, due date.
Add/remove tags.
View details of a single task.
Delete tasks.
See statistics (counts by status/priority, overdue, completed last week).
There is no web UI here; everything is done through the command line.

What you were unsure about (“what this app is about”)

The big picture:



4.
- I was clear on my understanding of the application.

- cli.js → Main Application Entry Point
This is the primary startup file for the application.

JSON:
"main": "cli.js"

- cli.js defines the command-line interface (what commands exist, which flags/arguments they take).
- app.js (TaskManager) is the business logic layer that the CLI calls.
- models.js likely defines the Task class and enums (TaskStatus, TaskPriority).
- storage handles saving/loading tasks from disk (likely JSON file).

**Exercise Part:2**

1.
- Based on the codebase there is currently no dedicated export functionality (CSV, Excel, PDF, etc.) implemented yet.

- Yes, the project contains code related to both data transformation and external file operations, mainly inside storage.js and cli.js. The application uses Node.js filesystem APIs such as fs.readFileSync(), fs.writeFileSync(), and fs.existsSync() to read from and write to a local tasks.json file, which handles task persistence. The code also performs several forms of data transformation, including converting JavaScript objects to JSON using JSON.stringify(), parsing JSON back into objects with JSON.parse(), reconstructing raw JSON data into Task class instances, formatting dates with Date objects, filtering collections of tasks, and transforming task data into human-readable CLI output through the formatTask() function. Additionally, methods like getStatistics() aggregate and transform task data into summarized metrics, showing that the project already has a structured data processing flow in place.

- The project contains several reusable utility-style functions that could support future file operations or export features. The formatTask() function in cli.js can be reused for formatting tasks into readable text outputs, while methods in storage.js such as load(), save(), getAllTasks(), and filtering methods provide reusable data retrieval and persistence functionality. The code also uses reusable serialization and transformation patterns like JSON.stringify(), JSON.parse(), date formatting with toISOString(), and task filtering with .filter(), all of which could be extended for exporting or processing task data.


2.
- Based on the initial search, task data export functionality would most likely belong in a new dedicated module such as export.js, utils/exporter.js, or a service layer alongside storage.js. This would fit well with the current layered architecture, where the project already separates CLI handling, business logic, models, and storage responsibilities. Keeping export logic separate would also improve maintainability and scalability.

- Existing components that would likely need modification include cli.js to add export-related commands, app.js to expose export business logic, and storage.js to provide reusable access to task data for exporting. Reusable methods such as getAllTasks(), getTasksByStatus(), getTasksByPriority(), and getStatistics() could support filtering and report generation, while formatTask() in cli.js could assist with text-based formatting.

- Search terms used during the investigation included fs, JSON.stringify, JSON.parse, writeFile, readFile, save, load, filter, formatTask, and toISOString. The main files identified from these searches were storage.js for file operations and persistence logic, cli.js for task formatting and command handling, and app.js for business logic and statistics generation.
